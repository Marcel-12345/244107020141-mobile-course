1. Apa perbedaan cara berpikir imperative dan declarative saat membangun UI?

Imperative (Bagaimana): Berfokus pada langkah-langkah manual untuk merubah UI. Anda harus melacak state saat ini, lalu secara eksplisit memanggil perintah untuk menambah, menghapus, atau mengubah elemen UI secara langsung ketika ada perubahan data (seperti element.setText("Hello") atau element.setVisibility(GONE)).
Declarative (Apa): Berfokus pada mendeskripsikan UI berdasarkan kondisi/data saat ini (UI = f(state)). Cukup mendefinisikan bagaimana bentuk UI untuk setiap state yang mungkin terjadi. Ketika data berubah, kerangka kerja (seperti Flutter) akan otomatis memperbarui tampilan agar sesuai dengan state baru tersebut.

2. Kapan Expanded membantu dan kapan penggunaannya justru menghasilkan layout error?

Sangat Membantu: Saat ditempatkan langsung di dalam Flex widget (Row, Column, atau Flex) untuk membagi sisa ruang kosong yang tersedia secara fleksibel atau memaksa child widget mengisi ruang yang ada tanpa melebihi batas.

Menghasilkan Layout Error (Unbounded Height/Width): Ketika Expanded dimasukkan ke dalam widget yang memberikan ruang tanpa batas (unbounded constraints).

Contoh: Menempatkan Expanded di dalam SingleChildScrollView atau ListView yang dapat di-scroll secara vertikal. Kerangka kerja tidak bisa menghitung sisa ruang pada sumbu tanpa batas tersebut, sehingga memicu error seperti RenderFlex overflowed atau HasSize / Unbounded constraints error.

3. Bagaimana breakpoint dan theme memengaruhi pengalaman pengguna?

Breakpoint (Adaptabilitas):

- Pengalaman Pengguna: Memastikan alur navigasi dan tata letak informasi terasa alami di berbagai ukuran layar (HP, tablet, desktop).

- Dampak: Mencegah elemen UI terpotong, teks terlalu kecil, atau ruang layar terbuang sia-sia. Pengguna mendapatkan tata letak yang dioptimalkan sesuai perangkat (misalnya mengganti bottom navigation bar pada HP menjadi navigation rail/sidebar pada layar lebar).

Theme (Konsistensi & Aksesibilitas):

- Pengalaman Pengguna: Menjaga hirarki visual, identitas produk, dan kenyamanan mata.

- Dampak: Mendukung Dark/Light mode untuk kenyamanan mata di berbagai kondisi pencahayaan, serta menjaga kontras warna dan ukuran tipografi agar aplikasi mudah dibaca oleh semua pengguna (termasuk yang memiliki keterbatasan visual).


4. Apa yang Anda verifikasi dari rekomendasi AI setelah tugas inti selesai?

Memverifikasi tampilan (atau lainnya) jika tidak yakin selesai.