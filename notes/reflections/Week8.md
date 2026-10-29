1. Apa beda LGTM buta dengan approve yang bertanggung jawab? Apa risikonya bagi tim?

LGTM (Looks Good To Me) Buta:

- Praktik: Memberikan tanda centang (approve) atau komentar "LGTM" hanya berdasarkan asumsi, melihat jumlah baris yang sedikit, atau karena ingin cepat-cepat menutup tugas tanpa benar-benar membaca, menguji, atau memahami logika kode yang diubah.

- Risiko bagi Tim: Menjadi celah masuknya bug kritikal, celah keamanan, pelanggaran standar arsitektur (seperti melanggar batas Clean Architecture yang baru saja dibahas), atau menurunnya kualitas kode secara keseluruhan secara senyap. Tim kehilangan fungsi filter kualitas.

Approve yang Bertanggung Jawab:

- Praktik: Melakukan review secara aktif—memeriksa alur logika, memastikan penanganan error sudah benar, mengecek apakah ada efek samping (side effects) ke bagian lain, memastikan uji unit terpenuhi, dan memahami mengapa perubahan tersebut dibuat. Jika ada bagian yang tidak dipahami, reviewer berani bertanya atau mendiskusikannya sebelum memberikan persetujuan.

2. Kapan komentar wajib berstatus request changes, dan kapan cukup saran?

Wajib Berstatus Request Changes (Blokir Merge):

- ondisi: Ditemukan kesalahan fatal yang tidak boleh sampai masuk ke branch utama (misalnya: bug logika serius, potensi memory leak, celah keamanan seperti SQL Injection atau kebocoran token rahasia, melanggar aturan arsitektur inti, atau kode menyebabkan crash).

- Tujuan: Secara tegas menahan proses penggabungan kode hingga pengembang memperbaiki masalah tersebut.

Cukup Sebagai Saran / Komentar Biasa (Non-Blocking):

- Kondisi: Masalah bersifat kosmetik, peningkatan gaya penulisan (code style), usapan optimasi mikro yang tidak mendesak, atau preferensi penamaan variabel yang lebih rapi tetapi kodenya sendiri sudah benar dan aman secara fungsional.

- Tujuan: Memberikan ruang bagi pengembang untuk mempertimbangkan masukan tersebut tanpa harus memblokir perilisan atau kemajuan tugas saat itu juga.