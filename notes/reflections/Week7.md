1. Mengapa Interface Repository Harus Tinggal di Domain, Bukan di Data?

Dalam arsitektur berlapis (Clean Architecture), Domain Layer adalah inti aplikasi (core business logic) yang harus berdiri sendiri dan tidak boleh bergantung pada teknologi luar (seperti framework, database, atau library jaringan).

Aturan Ketergantungan (Dependency Rule): Ketergantungan harus mengarah ke dalam. Domain mendefinisikan apa yang dibutuhkan aplikasi (kontrak/interface repository), sementara Data Layer mengimplementasikan bagaimana cara mendapatkannya (apakah dari API REST, SQLite, atau Firebase).

Apa yang Rusak Bila Dibalik (Interface di Data Layer)?

- Domain Terkontaminasi Detail Teknis: Jika interface berada di data layer, domain harus mengimpor atau mengetahui detail dari data layer. Ini merusak prinsip separation of concerns.

- Sulit Dilakukan Unit Testing: Anda tidak bisa menguji logika bisnis di domain secara independen tanpa melibatkan boilerplate atau dependensi dari data layer (seperti model objek dari package luar).

- Kaku Terhadap Perubahan: Jika Anda ingin mengganti sumber data (misalnya dari Retrofit/Dio ke GraphQL atau Hive), seluruh domain akan ikut terdampak karena terikat langsung pada implementasi teknis.

2. Kapan Use Case Benar-Benar Dibutuhkan vs. Repository Langsung ke Notifier?

Repository Langsung ke Notifier Cukup Digunakan Saat:

- Operasi bersifat simple CRUD atau penerusan data langsung (misalnya mengambil daftar profil pengguna, mengubah status toggle sederhana, atau operasi yang tidak melibatkan aturan bisnis yang rumit).

- Aplikasi berukuran kecil hingga menengah, di mana notifier (atau ViewModel) sudah cukup bersih dan hanya memanggil satu metode repository tanpa logika orkestrasi tambahan.

Use Case (Interactors) Benar-Benar Dibutuhkan Saat:

- Orkestrasi Bisnis Kompleks: Logika melibatkan banyak repository sekaligus (misalnya: CheckoutUseCase yang harus memanggil CartRepository, PaymentRepository, dan InventoryRepository secara berurutan dengan penanganan rollback jika gagal).

- Reusable Business Logic: Aturan bisnis yang sama perlu dipanggil dari berbagai tempat berbeda (misalnya dari UI mobile, background worker, atau deep link handler). Dengan use case, logika tersebut cukup ditulis sekali dan diuji secara terpisah (isolated unit test).

3. Biaya Over-Engineering (Use Case untuk CRUD Satu-Baris) bagi Tim Kecil

Bagi tim kecil atau proyek dengan tenggat waktu ketat, memaksakan pembuatan Use Case untuk setiap operasi CRUD sederhana (misalnya hanya memanggil repository.getNotes()) membawa dampak negatif:

- Ledakan File & Boilerplate (File Bloat): Anda harus membuat tiga hingga empat file terpisah hanya untuk menampilkan satu baris data (file UI, file Notifier, file Use Case, file Repository Interface, dan file Repository Impl). Ini memperlambat kecepatan pengembangan (development velocity).

- Pekerjaan Tambahan yang Tidak Bernilai (Cognitive Overhead): Developer harus melakukan mapping data berulang-ulang melalui terlalu banyak lapisan tanpa ada keuntungan aturan bisnis yang diselesaikan.

- Kapan Biayanya Sepadan? Biaya ini baru sepadan ketika proyek berskala besar, dikerjakan oleh tim yang banyak secara paralel, memiliki masa hidup yang panjang (bertahun-tahun), atau memiliki aturan bisnis yang sangat dinamis dan ketat sehingga pengujian unit pada logika domain menjadi penyelamat utama kualitas aplikasi.