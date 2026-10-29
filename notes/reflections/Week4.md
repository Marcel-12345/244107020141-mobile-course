1. Mengapa UI dilarang memanggil Dio langsung? Apa yang rusak jika aturan ini dilanggar?

Memanggil HTTP client (Dio) secara langsung di layer UI melanggar prinsip pemisahan tanggung jawab (Separation of Concerns) dan arsitektur Clean Architecture.

Dampak Jika Aturan Ini Dilanggar:

- Pengujian UI Terhalang (Untestable UI): Anda tidak dapat membuat unit test atau widget test untuk halaman UI tanpa benar-benar mengirim permintaan jaringan ke server sungguhan.

- Duplikasi Kode & Kerapuhan: Jika URL endpoint, header, token authentication, atau format JSON berubah, Anda harus merubah kode di puluhan file widget secara manual.

- Bocornya Detail Implementasi: UI terikat erat pada library tertentu. Jika di kemudian hari Anda ingin mengganti Dio dengan http atau GraphQL, Anda harus merombak seluruh layer UI aplikasi.

- Penanganan Error Tersebar: Logika retry, penanganan status code (401 Unauthorized, 500 Server Error), dan parsing data JSON akan menumpuk di file UI, membuat UI sangat rapuh dan sulit dibaca.

2. Kapan pagination client-side cukup, dan kapan harus mengandalkan pagination server (_page/_limit)?

Client-Side Pagination Cukup Digunakan Saat:

- Jumlah Data Pasti & Kecil: Total seluruh dataset diketahui sedikit (misal: kurang dari 100–300 item, seperti daftar kategori atau negara).

- Data Jarang Berubah & Perlu Akses Instan: Data yang di-fetch sekali lalu difilter, diurutkan, atau dibagi menjadi beberapa halaman secara lokal tanpa latency jaringan tambahan (misal: tabel laporan transaksi bulanan yang sudah di-cache).

Server-Side Pagination (_page/_limit atau Cursor) Wajib Digunakan Saat:

- Dataset Besar/Tak Terbatas: Ribuan hingga jutaan record (misal: feed media sosial, katalog produk e-commerce). Memuat semua data sekaligus akan menyebabkan memory leak dan performa aplikasi anjlok.

- Data Real-time/Dinamis: Data sering bertambah atau berubah di database. Server cukup mengirimkan potongan data (chunk) yang relevan sesuai permintaan.

3. Bagaimana exception repository berubah menjadi AsyncError tanpa try/catch di setiap widget? Kapan try/catch eksplisit tetap dibutuhkan?

### Bagaimana Exception Berubah Menjadi AsyncError Tanpa Try/Catch di Widget?

Riverpod (khususnya AsyncNotifier atau FutureProvider) secara otomatis menangkap exception yang dilempar oleh layer di bawahnya (seperti Repository).

- Ketika Repository melempar exception (misal: throw NetworkException()), fungsi pembangun provider (seperti build() atau method di notifier) membiarkan exception tersebut lolos (uncaught di level fungsi tersebut).

- Riverpod menutupi (wrap) penanganan eksekusi Future tersebut dalam blok try/catch internal.

- Jika ditangkap oleh Riverpod, state notifier secara otomatis diubah menjadi AsyncError(error, stackTrace).

Di layer UI, Anda cukup mendengarkan state ini menggunakan .when() atau .listen():

asyncValue.when(
  data: (data) => DisplayWidget(data),
  loading: () => LoadingWidget(),
  error: (err, stack) => ErrorWidget(err), // Menangani AsyncError secara deklaratif
);

### Kapan Try/Catch Eksplisit Tetap Dibutuhkan di UI/Widget?

- Aksi Imperatif (User Event Handlers): Saat melakukan aksi yang dipicu oleh interaksi pengguna (seperti menekan tombol Submit Form, Delete, atau Payment), di mana Anda memanggil method notifier secara langsung:

// Dalam callback onPressed tombol
try {
  await ref.read(myNotifierProvider.notifier).submitData();
  context.go('/success'); // Navigasi hanya jika sukses
} catch (e) {
  ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('Gagal: $e')));
}

- Kebutuhan Efek Samping Lokal Spesifik: Ketika kegagalan membutuhkan respons UI lokal yang instan seperti mengocok (shake) animasi input box, membuka bottom sheet kustom, atau tindakan pemulihan khusus yang tidak direpresentasikan oleh perubahan state global.