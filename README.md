# BambangShop Publisher App

Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project

In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:

1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment

1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable | type | description |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)

- [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
- **STAGE 1: Implement models and repositories**
  - [x] Commit: `Create Subscriber model struct.`
  - [x] Commit: `Create Notification model struct.`
  - [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
  - [x] Commit: `Implement add function in Subscriber repository.`
  - [x] Commit: `Implement list_all function in Subscriber repository.`
  - [x] Commit: `Implement delete function in Subscriber repository.`
  - [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
- **STAGE 2: Implement services and controllers**
  - [x] Commit: `Create Notification service struct skeleton.`
  - [x] Commit: `Implement subscribe function in Notification service.`
  - [x] Commit: `Implement subscribe function in Notification controller.`
  - [x] Commit: `Implement unsubscribe function in Notification service.`
  - [x] Commit: `Implement unsubscribe function in Notification controller.`
  - [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
- **STAGE 3: Implement notification mechanism**
  - [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
  - [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
  - [x] Commit: `Implement publish function in Program service and Program controller.`
  - [x] Commit: `Edit Product service methods to call notify after create/delete.`
  - [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections

This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. Menurut saya, pada kasus BambangShop ini kita belum benar-benar membutuhkan interface atau trait terpisah untuk Subscriber jika perilaku yang dimiliki semua subscriber masih sama dan implementasinya hanya satu. Pada pattern Observer, interface biasanya dipakai supaya Subject bisa berinteraksi dengan banyak jenis observer tanpa peduli implementasi konkretnya. Namun di project ini, subscriber saat ini hanya direpresentasikan sebagai data yang berisi url dan name, lalu perilakunya juga masih terfokus pada satu jenis mekanisme notifikasi HTTP. Jadi sebuah struct model saja sudah cukup untuk kebutuhan sekarang. Trait baru akan terasa penting kalau nanti ada beberapa tipe subscriber dengan perilaku berbeda, misalnya ada subscriber yang menerima webhook, ada yang menyimpan ke message queue, atau ada yang membutuhkan format notifikasi berbeda.

2. Untuk data yang memang harus unik seperti id pada Program dan url pada Subscriber, menurut saya Vec saja kurang ideal jika operasi yang sering dilakukan adalah pencarian, pengecekan duplikasi, atau penghapusan berdasarkan key unik. Vec masih bisa dipakai, tetapi setiap kali ingin mencari atau menghapus elemen kita perlu melakukan iterasi satu per satu, sehingga kurang efisien dan lebih rawan error saat aplikasi berkembang. Struktur seperti Dashmap atau map/dictionary lebih cocok karena key unik memang menjadi pusat identitas datanya. Dengan map, kita bisa langsung mengakses data berdasarkan id atau url, lebih mudah menjaga keunikan, dan operasi tambah/cari/hapus juga lebih natural untuk kasus ini.

3. Menurut saya, Singleton dan Dashmap sebenarnya menjawab dua kebutuhan yang berbeda, jadi Singleton saja tidak otomatis menggantikan kebutuhan Dashmap. Singleton pattern berfokus pada memastikan hanya ada satu instance global dari suatu objek. Sementara itu, Dashmap dipakai untuk memastikan akses terhadap data bersama tetap aman saat ada banyak thread yang berjalan bersamaan. Dalam kasus Subscribers, kita memang bisa saja mengatakan variabel statis itu adalah bentuk satu sumber data tunggal, mirip gagasan Singleton. Tetapi masalah utamanya bukan cuman harus satu instance, melainkan juga bagaimana instance itu diakses secara aman secara paralel. Karena itu, menurut saya kita tetap butuh struktur data yang thread-safe seperti Dashmap. Jadi Singleton tidak menggantikan Dashmap, kalaupun konsep Singleton ingin dipakai, ia lebih cocok dipadukan dengan container thread-safe, bukan digunakan sebagai pengganti.

#### Reflection Publisher-2

1. Menurut saya pemisahan antara Model, Service, dan Repository membantu kode menjadi lebih rapi dan lebih mudah dirawat. Model fokus pada bentuk data yang dipakai aplikasi. Repository fokus pada cara data disimpan dan diambil. Service fokus pada aturan bisnis dan alur proses aplikasi. Dengan pemisahan ini setiap bagian punya tanggung jawab yang jelas. Prinsip ini sejalan dengan ide separation of concerns dan single responsibility principle. Saat ada perubahan pada cara penyimpanan data atau aturan bisnis, kita bisa mengubah bagian yang relevan saja tanpa membuat seluruh model ikut menjadi rumit.

2. Jika kita hanya memakai Model, maka setiap model akan menampung terlalu banyak tanggung jawab sekaligus. Program tidak hanya menyimpan data produk, tetapi juga harus mengatur validasi, notifikasi, dan akses data. Subscriber tidak hanya menjadi representasi data, tetapi juga harus menangani logika pendaftaran, penghapusan, dan pengiriman informasi. Notification juga bisa ikut menjadi tempat logika bisnis dan aliran komunikasi antarmodel. Akibatnya interaksi antar model akan semakin rapat dan saling bergantung. Kode akan lebih sulit dibaca, lebih sulit diuji, dan lebih sulit dikembangkan karena perubahan kecil pada satu model bisa memengaruhi banyak bagian lain.

3. Saya sudah cukup banyak eksplor Postman dan menurut saya tool ini sangat membantu untuk menguji pekerjaan saat ini. Postman memudahkan saya mengirim request ke endpoint yang sedang dibuat tanpa harus menulis client secara manual. Saya bisa mencoba berbagai method HTTP, melihat response body, status code, dan header dengan cepat. Untuk project ini Postman membantu mengecek apakah endpoint subscribe dan unsubscribe berjalan sesuai harapan. Fitur yang menurut saya paling membantu adalah collection, environment, history, dan test script. Collection membantu menyusun request dengan rapi. Environment membantu mengganti base URL atau variabel dengan mudah. History memudahkan melacak request yang sudah pernah dicoba. Test script menarik karena bisa dipakai untuk otomatis memeriksa response.

#### Reflection Publisher-3

1. Menurut saya, pada tutorial ini kita menggunakan variasi Observer pattern berupa push model. Publisher secara aktif mengirimkan data notifikasi ke setiap subscriber melalui HTTP POST saat terjadi event seperti create, publish, dan delete product. Subscriber tidak perlu meminta data sendiri ke publisher karena informasi penting seperti nama produk, tipe produk, URL produk, nama subscriber, dan status event sudah dikirim langsung oleh publisher di dalam payload notifikasi.

2. Jika pada kasus ini kita memakai pull model, ada beberapa keuntungan dan kerugian. Keuntungannya, payload notifikasi awal bisa dibuat lebih sederhana karena publisher cukup memberi sinyal bahwa ada perubahan, lalu subscriber dapat mengambil detail yang dibutuhkan sendiri. Cara ini bisa memberi fleksibilitas lebih besar ke subscriber karena setiap subscriber bebas menentukan kapan dan data apa yang ingin diambil. Namun kerugiannya, jumlah request antar service bisa bertambah karena setelah menerima sinyal, subscriber masih harus melakukan request tambahan ke publisher. Ini membuat alur komunikasi lebih lambat dan lebih kompleks. Dalam konteks tutorial ini, push model terasa lebih cocok karena kebutuhan datanya sederhana dan publisher sudah tahu informasi apa yang perlu langsung dikirim ke subscriber.

3. Jika kita memutuskan untuk tidak menggunakan multi threading dalam proses notifikasi, maka pengiriman notifikasi ke subscriber akan berjalan secara berurutan. Dampaknya, response dari endpoint utama seperti create, publish, atau delete bisa menjadi lebih lambat karena server harus menunggu setiap pengiriman notifikasi selesai satu per satu. Jika ada subscriber yang lambat atau tidak responsif, proses pada publisher juga bisa ikut tertahan. Ini bisa menurunkan performa aplikasi, terutama ketika jumlah subscriber bertambah. Dengan multi threading, publisher dapat memproses pengiriman notifikasi ke banyak subscriber secara paralel sehingga endpoint utama tetap lebih responsif dan tidak terlalu bergantung pada kecepatan masing masing subscriber.
