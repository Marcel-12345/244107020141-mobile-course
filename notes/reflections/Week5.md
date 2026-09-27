1. Mengapa Daftar Catatan Tidak Boleh Disimpan di SharedPreferences?

SharedPreferences dirancang untuk menyimpan data konfigurasi/preferensi sederhana berukuran kecil (seperti key-value pair isDarkMode: true atau userId: "123"), bukan data aplikasi terstruktur (app data) seperti daftar catatan.

Apa yang Rusak Jika Aturan Ini Dilanggar?
- Masalah Performa & Bloking I/O: SharedPreferences membaca/menulis seluruh file XML/Plist atau SQLite internal ke memori (RAM). Setiap kali Anda memperbarui 1 catatan, seluruh daftar catatan harus diserialisasi ulang menjadi String (JSON) dan ditulis ulang secara utuh ke media penyimpanan. Seiring bertambahnya catatan, proses I/O ini menyebabkan lag dan jank pada UI.

- Kehilangan Kemampuan Query & Indexing: Anda tidak bisa melakukan query efisien seperti WHERE title LIKE '%meeting%' atau sorting berdasarkan tanggal. Pengurutan dan pencarian harus dilakukan manual di memori setelah memuat seluruh String dari disk.

- Risiko Korupsi Data (Race Condition): SharedPreferences tidak memiliki fitur ACID Transactions. Jika aplikasi terhenti mendadak (crash) saat proses penulisan string JSON raksasa sedang berlangsung, file dapat korup dan menyebabkan seluruh daftar catatan hilang/unparsable.

Solusi Seharusnya: Gunakan Local Database berkinerja tinggi yang mendukung indexing dan query seperti Isar, Hive, SQLite (via Drift), atau Realm.

2. Strategi Cache-First vs. Strategi Lainnya

Kapan Cache-First Cukup Digunakan?
- Strategi Cache-First (ambil dari data lokal dulu; jika ada, gunakan; jika tidak ada, panggil jaringan) sangat cocok untuk:

- Data yang jarang berubah (static/semi-static data) seperti profil pengguna, daftar kategori produk, konfigurasi aplikasi, atau artikel berita yang sudah dipublikasikan.

- Skenario Offline-First App, di mana kecepatan render UI awal (perceived performance) dan kemampuan bekerja tanpa koneksi internet menjadi prioritas utama.

Kapan Membutuhkan Strategi Lain?
Network-First (Stale-While-Revalidate / Fresh Data Required):

- Penggunaan: Data yang sensitif terhadap waktu atau cepat berubah, seperti harga saham/kripto, stok produk e-commerce real-time, atau kurs mata uang.

- Alasan: Menampilkan data usang dari cache pada kasus ini dapat merugikan secara finansial atau menyebabkan transaksi gagal akibat harga/stok yang sudah tidak valid.

Network-Only:

- Penggunaan: Proses pembayaran/checkout, verifikasi OTP, atau otentikasi login.

- Alasan: Data transaksi tidak boleh di-cache sama sekali demi alasan keamanan dan integritas status sistem.

3. Transformasi Dirty Flag Menjadi Antrean Sync & Tabel Outbox

Bagaimana Dirty Flag Berubah Menjadi Antrean Sync Tanpa Memblokir UI?
- Penandaan (Dirty Flag): Ketika pengguna membuat atau mengubah catatan dalam keadaan offline, aplikasi menyimpan perubahan tersebut ke database lokal dan menandai baris data tersebut dengan kolom flag, misalnya is_dirty = true atau sync_status = "PENDING".

- Pemberitahuan Reaktif: UI diperbarui secara instan menggunakan data listener lokal (seperti Isar Watcher, Drift Stream, atau LiveQuery). Pengguna melihat perubahan secara cepat (optimistic UI) tanpa menunggu proses jaringan.

- Proses Latar Belakang (Asinkron): Sebuah Background Worker atau Sync Service mendengarkan perubahan status jaringan atau mendeteksi flag is_dirty = true menggunakan kueri asinkron di Isolate terpisah atau menggunakan paket workmanager/Background Task.

- Eksekusi Non-blocking: Service mengirim data yang dirty ke server satu per satu di background thread. Setelah server memberikan respons sukses (HTTP 200/201), lokal database mengupdate flag is_dirty = false. UI tidak pernah terblokir karena IO dan HTTP request berjalan di luar main UI thread.

Kapan Antrean Terpisah (Tabel Outbox Pattern) Menjadi Perlu?
- Dirty Flag langsung pada tabel utama mulai tidak memadai dan Anda butuh Tabel Outbox khusus (misalnya tabel sync_outbox_queue) ketika:

- Pentingnya Urutan Eksekusi (Ordering): Pengguna melakukan serangkaian aksi berurutan pada baris data yang sama dalam keadaan offline (misal: Create Catatan A -> Edit Judul Catatan A -> Hapus Catatan A). Jika hanya memakai dirty flag pada baris catatan, sistem lokal hanya tahu kondisi akhir (terhapus), sehingga server tidak pernah mencatat riwayat perubahan tersebut. Tabel Outbox menyimpan riwayat action/event log secara tertata berdasarkan timestamp (created_at).

- Mendukung Operasi Non-CRUD Spesifik: Ketika perubahan bukan sekadar penggantian nilai kolom, melainkan tindakan spesifik seperti SEND_EMAIL, ADD_TO_CART, atau LIKE_POST.

- Tracking Penanganan Error & Retry Limit: Tabel Outbox memungkinkan Anda menyimpan status terisolasi untuk tiap request antrean, seperti retry_count, last_error_message, dan backoff_delay, tanpa mencemari struktur skema tabel bisnis utama (Catatan).