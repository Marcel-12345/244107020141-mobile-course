1. Keamanan Refresh Token & Risiko Kebocoran

SharedPreferences menyimpan data dalam bentuk file teks biasa (plain text XML/JSON) di penyimpanan internal perangkat. Perangkat Android yang di-root atau terkena serangan malware (melalui exploit ADB atau aplikasi berbahaya dengan akses fisik/root) dapat membaca file ini dengan sangat mudah.

Apa Risikonya Bila Refresh Token Bocor?

- Akses Tanpa Batas (Long-Lived Impersonation): Tidak seperti Access Token yang berumur pendek (misalnya 15–60 menit), Refresh Token memiliki masa berlaku sangat panjang (mingguan, bulanan, atau bahkan lifetime). Penyerang dapat terus menerus meminta Access Token baru tanpa perlu mengetahui kata sandi pengguna.
- Pengambilalihan Akun Permanen: Penyerang dapat bertindak atas nama pengguna untuk mengakses data sensitif, mengubah email, atau melakukan transaksi hingga token tersebut di-revok secara manual dari sisi server.
- Solusi Keamanan: Simpan Refresh Token di dalam EncryptedSharedPreferences (menggunakan Android Keystore System) atau paket Flutter seperti flutter_secure_storage yang mengenskripsi data menggunakan kunci berbasis perangkat keras.

2. Konsekuensi Mengabaikan onTokenRefresh Selama Satu Semester

onTokenRefresh adalah stream listener di Firebase Cloud Messaging (FCM) yang terpicu setiap kali registrasi token FCM pada perangkat mengalami perubahan/pembaruan di server Firebase.

Apa yang Rusak Jika Diabaikan Selama 1 Semester Perkuliahan?

- Daftar Token di Server Menjadi "Basi" (Stale/Invalid Tokens): Token FCM dapat berubah sewaktu-waktu akibat pembaruan aplikasi, restore data perangkat, clear data/cache, atau reset internal dari Firebase.
- Notifikasi Kampus Gagal Terkirim (Silent Failure): Server kampus akan terus mencoba mengirim notifikasi penting (pengumuman KRS, nilai ujian, atau pengingat pembayaran) ke token lama yang sudah tidak valid. FCM akan mengembalikan respon error NotRegistered atau InvalidRegistration.
- Mahasiswa Kehilangan Informasi Kritis: Mahasiswa tidak akan menerima notifikasi push sama sekali di HP mereka selama berbulan-bulan sampai mereka logout dan login ulang secara manual.

3. Kapan Memakai Topik vs. Token Perangkat (Beserta Contoh Pesan Kampus)
Kriteria | FCM Topics (subscribeToTopic) | Perangkat Spesifik (token)
Konsep | Pub/Sub model. Pesan dikirim ke satu grup/kategori umum tanpa perlu tahu siapa atau berapa banyak penerimanya. | Unicast/Direct targeting. Pesan dikirim khusus ke satu sesi perangkat spesifik milik satu pengguna.
Kapan Digunakan | Informasi massal yang relevan untuk sekelompok pengguna berdasarkan peran, jurusan, atau kelas. | Informasi terpersonalisasi, privat, atau butuh aksi transaksi individual.

Contoh Pesan Kampus:

A. Penggunaan FCM Topic (Misal topic: krs_ti_2026 atau pengumuman_kampus)
- Judul: Pengingat Batas Akhir Pengisian KRS Semester Ganjil
- Bodi: Batas pengisian KRS untuk mahasiswa Jurusan Teknologi Informasi akan ditutup malam ini pukul 23.59 WIB. Harap segera melakukan finalisasi.

B. Penggunaan Device Token (Target: Token Mahasiswa NIM Spesifik)
- Judul: Revisi Dokumen Judul Skripsi
- Bodi: Halo Tsaritsa, Dosen Pembimbing 1 telah memberikan catatan revisi pada draft Bab 2 Anda. Silakan cek sistem akademik untuk detailnya.