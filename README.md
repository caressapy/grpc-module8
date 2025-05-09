## Refleksi

> Apa saja perbedaan utama antara metode *unary*, *server streaming*, dan *bi-directional streaming* dalam RPC (Remote Procedure Call), dan dalam situasi apa masing-masing metode lebih cocok digunakan?

Perbedaan mendasar antara *unary*, *server streaming*, dan *bi-directional streaming* terletak pada cara pengiriman *request* dan penerimaan *response*. Dalam metode *unary*, klien mengirimkan satu *request* dan menunggu satu *response* dari server. Sementara itu, pada metode *server streaming*, klien mengirimkan *request* dan menerima beberapa *response* secara berurutan dari server. Metode *bi-directional streaming* memungkinkan klien untuk mengirimkan beberapa *request* dalam satu koneksi dan menerima beberapa *response* sekaligus dari server. Metode *unary* ideal untuk keperluan seperti autentikasi, pengambilan data dari *database*, atau melakukan perhitungan. Sementara itu, *server streaming* lebih tepat digunakan saat server perlu mengirimkan banyak data, seperti untuk mendapatkan update berita, cuaca, atau harga saham terkini. Untuk aplikasi yang membutuhkan komunikasi *real-time*, seperti aplikasi chat atau analitik waktu nyata, metode *bi-directional streaming* adalah pilihan terbaik.

> Apa saja pertimbangan keamanan yang perlu diperhatikan dalam implementasi layanan gRPC di Rust, terutama terkait dengan autentikasi, otorisasi, dan enkripsi data?

Saat mengimplementasikan layanan gRPC di Rust, penting untuk memastikan bahwa setiap *request* yang dikirim oleh klien memiliki autentikasi dan otorisasi yang valid. Ini berarti setiap *request* harus diverifikasi untuk memastikan bahwa klien memiliki hak akses yang sesuai. Selain itu, setiap *request* juga harus dienkripsi untuk mencegah potensi serangan *man-in-the-middle*, yang mengharuskan server untuk mendekripsi dan mengenkripsi data setiap kali memproses *request*. Proses ini membutuhkan pemanggilan fungsi tambahan untuk menangani setiap *request* dengan aman.

> Apa tantangan yang mungkin muncul saat menangani *bi-directional streaming* di Rust gRPC, terutama dalam aplikasi seperti chat?

Tantangan utama dalam *bi-directional streaming* adalah potensi terjadinya *race-condition*. Dalam aplikasi seperti chat, *race-condition* bisa terjadi ketika server menerima dua *request* dalam waktu yang hampir bersamaan, yang menyebabkan kebingungannya dalam menentukan *response* mana yang harus diprioritaskan. Oleh karena itu, penting untuk memastikan bahwa ada mekanisme sinkronisasi data setelah setiap *request* dan bahwa penanganan *request* dilakukan secara *thread-safe*.

> Apa keuntungan dan kerugian menggunakan `tokio_stream::wrappers::ReceiverStream` untuk melakukan streaming *response* dalam layanan gRPC di Rust?

Keuntungan utama menggunakan `tokio_stream::wrappers::ReceiverStream` adalah ia memberikan abstraksi untuk *streaming*, sehingga kita tidak perlu mengkhawatirkan implementasi detail dari *streaming* tersebut. Penggunaan pustaka ini juga menyederhanakan pengelolaan sinkronisasi data, yang sudah disediakan oleh pustaka tersebut. Namun, kelemahan penggunaan pustaka ini adalah kompleksitas pemrograman *asynchronous* yang dapat membuat implementasi menjadi lebih sulit dipahami dan dikelola.

> Bagaimana struktur kode gRPC Rust dapat dirancang untuk memfasilitasi penggunaan ulang kode dan modularitas, serta mendukung pemeliharaan dan pengembangan lebih lanjut?

Struktur kode gRPC di Rust bisa dibuat lebih modular dengan mendefinisikan *traits* atau *interface* di file `.proto`, yang kemudian diimplementasikan dalam kode Rust menggunakan *struct*. Setiap layanan dapat dipisahkan ke dalam file terpisah agar tidak tercampur dalam satu file besar. File-file ini bisa diorganisasikan dalam folder terpisah, seperti folder `unary`, `server streaming`, dan `bi-directional streaming`, yang masing-masing berisi layanan yang sesuai dengan metode komunikasi yang relevan.

> Dalam implementasi MyPaymentService, langkah-langkah tambahan apa yang mungkin diperlukan untuk menangani logika pemrosesan pembayaran yang lebih kompleks?

Untuk menangani logika pemrosesan pembayaran yang lebih kompleks, akan sangat berguna untuk menerapkan *multi-threading* agar layanan dapat menerima dan memproses beberapa *request* dari klien yang berbeda tanpa perlu menunggu proses sebelumnya selesai. Ini akan meningkatkan performa dan responsivitas sistem.

> Apa dampak adopsi gRPC sebagai protokol komunikasi terhadap arsitektur dan desain sistem terdistribusi, khususnya terkait dengan interoperabilitas dengan teknologi dan platform lain?

gRPC mempermudah komunikasi antar platform dan teknologi karena klien hanya perlu membuka satu koneksi untuk mengirim dan menerima *request*. gRPC juga menggunakan Protobuf untuk mendefinisikan layanan dan serialisasi data, yang mendukung interoperabilitas karena platform lain hanya perlu menerima file Protobuf yang sama dan menggunakan pustaka untuk menangani definisi Protobuf. Ini mengurangi kebutuhan untuk membuat definisi dan serialisasi data sendiri, sehingga mempercepat integrasi antar sistem.

> Apa keuntungan dan kerugian menggunakan HTTP/2, protokol dasar gRPC, dibandingkan dengan HTTP/1.1 atau HTTP/1.1 dengan WebSocket untuk REST API?

Keuntungan menggunakan HTTP/2 dibandingkan HTTP/1.1 atau HTTP/1.1 dengan WebSocket adalah klien hanya perlu membuka satu koneksi dengan server untuk mengirimkan banyak *request* dan menerima banyak *response* secara bersamaan. Selain itu, HTTP/2 memproses *request* dan *response* secara paralel, yang mengurangi kemungkinan antrian terblokir karena satu *request* memakan waktu lama untuk diproses. Namun, kelemahan HTTP/2 adalah implementasinya yang lebih kompleks, karena harus mengelola banyak *request* dalam satu koneksi dan memastikan tidak terjadi *race condition*. HTTP/2 juga lebih rentan terhadap penurunan kinerja jika koneksi internet tidak stabil.

> Bagaimana model *request-response* pada REST API berbeda dengan kemampuan *bi-directional streaming* pada gRPC dalam hal komunikasi waktu nyata dan responsivitas?

Model *request-response* pada REST API cenderung kurang responsif dibandingkan dengan *bi-directional streaming* pada gRPC. Dalam REST, satu koneksi hanya digunakan untuk satu *request* dan satu *response*, yang memerlukan pembukaan koneksi berulang untuk komunikasi *real-time*. Hal ini menyebabkan peningkatan overhead karena setiap pembukaan koneksi memerlukan beberapa tahapan. Sebaliknya, pada gRPC, koneksi tetap terbuka untuk komunikasi berkelanjutan, memungkinkan *response* diterima secara real-time tanpa perlu membuka koneksi baru.

> Apa implikasi dari pendekatan berbasis skema pada gRPC, menggunakan Protocol Buffers, dibandingkan dengan sifat JSON yang lebih fleksibel dan tanpa skema pada payload REST API?

Dengan menggunakan Protocol Buffers di gRPC, kita dapat memastikan bahwa data yang dikirim sesuai dengan spesifikasi yang telah didefinisikan sebelumnya, yang meningkatkan konsistensi dan keandalan. Di sisi lain, penggunaan JSON dalam REST API memerlukan kesepakatan antara klien dan server tentang format JSON yang diterima. Hal ini bergantung pada kehati-hatian dari kedua belah pihak, sehingga tidak dapat dijamin bahwa data yang dikirim sesuai dengan format yang disepakati.
