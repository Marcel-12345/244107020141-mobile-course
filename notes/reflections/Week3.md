1. Kapan setState masih cukup, dan kapan state harus naik ke Riverpod?

setState Cukup: Saat state bersifat lokal dan sementara pada satu widget saja, serta tidak memengaruhi logika bisnis atau widget lain.

- Contoh: Mengubah visibilitas kata sandi (toggle show/hide password), mengontrol animation controller, atau menyimpan nilai input teks sementara sebelum dikirim.

Harus Naik ke Riverpod: Saat state bersifat global, dibagikan (shared), atau persisten lintas halaman/layar.

- Contoh: Data otentikasi pengguna, daftar keranjang belanja, tema aplikasi, caching data API, atau ketika logika bisnis perlu dipisahkan dari layer UI agar mudah diuji (testable).

2. Apa perbedaan context.go dan context.push, dan kapan masing-masing tepat digunakan?

context.go (Declarative Routing / Navigation Tree):

- Cara Kerja: Mengubah lokasi jalur (path) URI aplikasi secara deklaratif dan mengganti tumpukan halaman (stack) sesuai dengan konfigurasi rute.

- Kapan Digunakan: Untuk navigasi utama antar halaman/fitur yang terpisah (misalnya berpindah dari /login ke /dashboard, atau berpindah tab navigasi utama). Menekan tombol back pada OS akan mengikuti struktur hierarki lokasi URL, bukan riwayat klik.

context.push (Imperative Stacking):

- Cara Kerja: Menambahkan halaman baru tepat di atas tumpukan halaman (stack) yang sedang aktif saat ini.

- Kapan Digunakan: Untuk membuka alur sementara atau halaman detail yang secara eksplisit ingin dapat di-pop/ditutup kembali ke halaman sebelumnya (misalnya membuka halaman Detail Produk dari daftar pencarian atau membuka dialog/modal full-screen).

3. Bagaimana AsyncValue mencegah bug dibanding tiga boolean terpisah?

Pendekatan manual dengan tiga boolean terpisah (misalnya isLoading, isError, hasData) rentan terhadap invalid/impossible states—seperti kondisi bug di mana isLoading = true dan isError = true terjadi secara bersamaan jika pengembang lupa mengosongkan state sebelumnya.

- Mencegah Bug State (Impossible States): AsyncValue menggunakan pola Sealed Class / Pattern Matching yang memodelkan status asinkron sebagai eksklusif satu sama lain (mutually exclusive): hanya bisa berupa AsyncLoading, AsyncData, atau AsyncError.

- Menjamin Penanganan Semua Kondisi: Dengan sintaks .when() atau .map(), compiler memaksa Anda menangani ketiga kondisi tersebut secara eksplisit. Anda tidak akan secara tidak sengaja lupa menampilkan indikator loading atau pesan error saat data gagal dimuat.

- Mempertahankan Data Lama Saat Refresh: AsyncValue tetap menyimpan nilai data sebelumnya (value) saat proses memuat ulang (re-fetching) sedang berjalan, sehingga UI dapat menampilkan data cached atau loading overlay secara mulus tanpa layar tiba-tiba kosong (flicker).

4. Bagian mana dari hasil AI yang Anda perbaiki, dan mengapa?