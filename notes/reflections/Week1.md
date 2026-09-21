1. Kapan native lebih tepat dipilih daripada cross-platform?

Pengembangan native (Swift/Kotlin) lebih disarankan ketika aplikasi membutuhkan:

Akses Hardware Spesifik & Tingkat Tinggi: Aplikasi yang bergantung pada fitur low-level hardware (misalnya pemrosesan Bluetooth Low Energy yang kompleks, kontrol kamera manual raw, sensor khusus, atau AR/VR interaktif).

Performa Maksimal (Zero Overhead): Game 3D berkinerja tinggi, pemrosesan video/audio real-time, atau aplikasi yang butuh optimasi memori dan rendering hingga batas maksimal perangkat.

Adopsi Fitur OS Hari Pertama (Day-1 OS Features): Aplikasi yang harus langsung mendukung fitur API terbaru yang dirilis Apple atau Google pada hari pertama tanpa menunggu library pihak ketiga diperbarui.

2. Bagaimana perubahan state berhubungan dengan widget tree dan UI deklaratif?

Flutter menggunakan paradigma UI Deklaratif, yang berarti tampilan UI adalah fungsi dari state saat ini: UI = f(state).
UI Deklaratif: Anda tidak mengubah elemen UI secara manual (seperti mengubah teks pada tombol secara langsung). Anda cukup memperbarui data (state), dan Flutter akan membangun ulang tampilan sesuai state baru tersebut.Perubahan State & Widget Tree:Ketika terjadi perubahan data (misalnya pemanggilan setState() atau lewat state management seperti Provider/Bloc), Flutter menandai widget terkait sebagai "kotor" (dirty).Flutter merespons dengan memicu metode build(), yang memicu pembuatan ulang sub-pohon (subtree) dari Widget Tree yang terdampak.Flutter membandingkan widget tree baru dengan element tree yang ada (reconcile/diffing) untuk memperbarui Render Tree secara efisien tanpa harus merombak seluruh struktur dari nol.

3. Mengapa commit kecil dengan pesan jelas bermanfaat bagi pekerjaan tim dan portfolio?

Membuat commit Git yang berukuran kecil (atomik) dengan pesan yang deskriptif membawa dampak signifikan:

Bagi Pekerjaan Tim:

Mempermudah Code Review: Anggota tim dapat memahami alasan dan konteks perubahan kode dengan cepat tanpa harus membaca ratusan baris sekaligus.

Isolasi Bug & Debugging: Jika terjadi kesalahan, tim dapat melacak komit penyebab (Git bisect) dan melakukan revert pada fitur spesifik tersebut tanpa merusak pekerjaan lain.

Minim Conflict: Mengurangi risiko merge conflict yang parah saat menggabungkan kode dengan anggota tim lain.

Bagi Portfolio:

Menunjukkan Profesionalisme: Menampilkan workflow kerja yang rapi, terstruktur, dan disiplin sesuai standar industri.

Memperlihatkan Alur Berpikir: Perekrut atau senior developer dapat melihat cara Anda memecahkan masalah langkah demi langkah (step-by-step problem solving) melalui riwayat komit project Anda.