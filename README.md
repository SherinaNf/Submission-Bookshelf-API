# Submission Bookshelf API
> Link Tutorial ada di [BLOG](https://netcodingxr.blogspot.com/2024/01/dicoding-submission-bookshelf-api.html).

Istilah back-end, front-end, server, client, web service, REST API serta belajar cara berkomunikasi antara client dengan server menggunakan cURL.
Dasar-dasar Node.js dalam pengembangan Back-End seperti global object, process, modularization, event, dan yang lainnya.
Membangun web server menggunakan Node.js native maupun Hapi Framework. Selain itu, Anda sudah membuat RESTful API sederhana yang mendukung fungsionalitas aplikasi catatan.
Melakukan deploy RESTful API pada Amazon EC2.
Pengujian RESTful API secara otomatis menggunakan Postman.
Tentu Anda juga sudah mengerjakan seluruh latihan yang diberikan pada kelas ini.
Untuk bisa lulus dan mendapatkan sertifikat dari akademi ini, Anda harus mengerjakan tugas yakni membuat proyek Bookshelf API sesuai kriteria lengkap di bawah ini. Tim Reviewer akan memeriksa pekerjaan Anda dan memberikan reviu pada proyek yang Anda buat.

## Kriteria
Terdapat 7 kriteria utama yang harus Anda penuhi dalam membuat proyek Bookshelf API.
### Kriteria 1 : Aplikasi menggunakan port 9000
Aplikasi yang Anda buat harus menggunakan port 9000. Jika komputer yang Anda gunakan untuk membuat submission tidak bisa memakai port 9000,  buatlah submission dengan port lain, lalu ketika submission hendak dikirimkan silakan ganti portnya ke 9000.
### Kriteria 2 : Aplikasi dijalankan dengan perintah npm run start.
Aplikasi yang Anda buat harus memiliki runner script start. Cara membuatnya, Anda tambahkan properti start ke dalam properti scripts pada package.json seperti berikut:
```
{
  "name": "submission",
  ...
  "scripts": {
    "start": "node src/server.js",
  }
}
```
Pastikan aplikasi tidak dijalankan dengan menggunakan nodemon. Jika Anda ingin menggunakan nodemon dalam proses development, masukkan nodemon kedalam runner script lain, contohnya:
```
{
  "name": "submission",
  ...
  "scripts": {
    "start": "node src/server.js",
    "start-dev": "nodemon src/server.js",
  }
}
```
### Kriteria 3 : API dapat menyimpan buku
API yang Anda buat harus dapat menyimpan buku melalui route:
* Method : POST
* URL : /books
* Body Request:
```
{
    "name": string,
    "year": number,
    "author": string,
    "summary": string,
    "publisher": string,
    "pageCount": number,
    "readPage": number,
    "reading": boolean
}
```
Objek buku yang disimpan pada server harus memiliki struktur seperti contoh di bawah ini:
```
{
    "id": "Qbax5Oy7L8WKf74l",
    "name": "Buku A",
    "year": 2010,
    "author": "John Doe",
    "summary": "Lorem ipsum dolor sit amet",
    "publisher": "Dicoding Indonesia",
    "pageCount": 100,
    "readPage": 25,
    "finished": false,
    "reading": false,
    "insertedAt": "2021-03-04T09:11:44.598Z",
    "updatedAt": "2021-03-04T09:11:44.598Z"
}
```
Properti yang ditebalkan diolah dan didapatkan di sisi server. Berikut penjelasannya:
* id : nilai id haruslah unik. Untuk membuat nilai unik, Anda bisa memanfaatkan nanoid. Untuk Anda yang menggunakan CommonJS untuk sistem modularisasi, pastikan memasang nanoid versi 3 melalui perintah: npm install nanoid@3.
* finished : merupakan properti boolean yang menjelaskan apakah buku telah selesai dibaca atau belum. Nilai finished didapatkan dari observasi pageCount === readPage.
* insertedAt : merupakan properti yang menampung tanggal dimasukkannya buku. Anda bisa gunakan new Date().toISOString() untuk menghasilkan nilainya.
* updatedAt : merupakan properti yang menampung tanggal diperbarui buku. Ketika buku baru dimasukkan, berikan nilai properti ini sama dengan insertedAt.
Server harus merespons gagal bila:
* Client tidak melampirkan properti namepada request body. Bila hal ini terjadi, maka server akan merespons dengan: <br>
&nbsp;&nbsp;&nbsp;&#x25CB; Status Code : 400 <br>
&nbsp;&nbsp;&nbsp;&#x25CB; Response Body: <br>
```
{
    "status": "fail",
    "message": "Gagal menambahkan buku. Mohon isi nama buku"
}
```
* Client melampirkan nilai properti readPage yang lebih besar dari nilai properti pageCount. Bila hal ini terjadi, maka server akan merespons dengan: <br>
&nbsp;&nbsp;&nbsp;&#x25CB; Status Code : 400
&nbsp;&nbsp;&nbsp;&#x25CB; Response Body:
```
{
    "status": "fail",
    "message": "Gagal menambahkan buku. readPage tidak boleh lebih besar dari pageCount"
}
```
Bila buku berhasil dimasukkan, server harus mengembalikan respons dengan :
* Status Code : 201
* Response Body:
```
{
    "status": "success",
    "message": "Buku berhasil ditambahkan",
    "data": {
        "bookId": "1L7ZtDUFeGs7VlEt"
    }
}
```
### Kriteria 4 : API dapat menampilkan seluruh buku
API yang Anda buat harus dapat menampilkan seluruh buku yang disimpan melalui route:
* Method : GET
* URL: /books
Server harus mengembalikan respons dengan:
* Status Code : 200
* Response Body:
```
{
    "status": "success",
    "data": {
        "books": [
            {
                "id": "Qbax5Oy7L8WKf74l",
                "name": "Buku A",
                "publisher": "Dicoding Indonesia"
            },
            {
                "id": "1L7ZtDUFeGs7VlEt",
                "name": "Buku B",
                "publisher": "Dicoding Indonesia"
            },
            {
                "id": "K8DZbfI-t3LrY7lD",
                "name": "Buku C",
                "publisher": "Dicoding Indonesia"
            }
        ]
    }
}
```
Jika belum terdapat buku yang dimasukkan, server bisa merespons dengan array books kosong.
```
{
    "status": "success",
    "data": {
        "books": []
    }
}
```
### Kriteria 5 : API dapat menampilkan detail buku
API yang Anda buat harus dapat menampilkan seluruh buku yang disimpan melalui route:
* Method : GET
* URL: /books/{bookId}
Bila buku dengan id yang dilampirkan oleh client tidak ditemukan, maka server harus mengembalikan respons dengan:
* Status Code : 404
* Response Body:
```
{
    "status": "fail",
    "message": "Buku tidak ditemukan"
}
```
Bila buku dengan id yang dilampirkan ditemukan, maka server harus mengembalikan respons dengan:
* Status Code : 200
* Response Body:
```
{
    "status": "success",
    "data": {
        "book": {
            "id": "aWZBUW3JN_VBE-9I",
            "name": "Buku A Revisi",
            "year": 2011,
            "author": "Jane Doe",
            "summary": "Lorem Dolor sit Amet",
            "publisher": "Dicoding",
            "pageCount": 200,
            "readPage": 26,
            "finished": false,
            "reading": false,
            "insertedAt": "2021-03-05T06:14:28.930Z",
            "updatedAt": "2021-03-05T06:14:30.718Z"
        }
    }
}
```
### Kriteria 6 : API dapat mengubah data buku
API yang Anda buat harus dapat mengubah data buku berdasarkan id melalui route:
* Method : PUT
* URL : /books/{bookId}
* Body Request:
```
{
    "name": string,
    "year": number,
    "author": string,
    "summary": string,
    "publisher": string,
    "pageCount": number,
    "readPage": number,
    "reading": boolean
}
```
Server harus merespons gagal bila:
* Client tidak melampirkan properti name pada request body. Bila hal ini terjadi, maka server akan merespons dengan: <br>
&nbsp;&nbsp;&nbsp;&#x25CB; Status Code : 400 <br>
&nbsp;&nbsp;&nbsp;&#x25CB; Response Body: <br>
```
{
    "status": "fail",
    "message": "Gagal memperbarui buku. Mohon isi nama buku"
}
```
* Client melampirkan nilai properti readPage yang lebih besar dari nilai properti pageCount. Bila hal ini terjadi, maka server akan merespons dengan: <br>
&nbsp;&nbsp;&nbsp;&#x25CB; Status Code : 400 <br>
&nbsp;&nbsp;&nbsp;&#x25CB; Response Body: <br>
```
{
    "status": "fail",
    "message": "Gagal memperbarui buku. readPage tidak boleh lebih besar dari pageCount"
}
```
* Id yang dilampirkan oleh client tidak ditemukkan oleh server. Bila hal ini terjadi, maka server akan merespons dengan: <br>
&nbsp;&nbsp;&nbsp;&#x25CB;  Code : 404 <br>
&nbsp;&nbsp;&nbsp;&#x25CB; Response Body: <br>
```
{
    "status": "fail",
    "message": "Gagal memperbarui buku. Id tidak ditemukan"
}
```
Bila buku berhasil diperbarui, server harus mengembalikan respons dengan:
* Status Code : 200
* Response Body:
```
{
    "status": "success",
    "message": "Buku berhasil diperbarui"
}
```
### Kriteria 7 : API dapat menghapus buku
API yang Anda buat harus dapat menghapus buku berdasarkan id melalui route berikut:
* Method : DELETE
* URL: /books/{bookId}
Bila id yang dilampirkan tidak dimiliki oleh buku manapun, maka server harus mengembalikan respons berikut:
* Status Code : 404
* Response Body:
```
{
    "status": "fail",
    "message": "Buku gagal dihapus. Id tidak ditemukan"
}
```
Bila id dimiliki oleh salah satu buku, maka buku tersebut harus dihapus dan server mengembalikan respons berikut:
* Status Code : 200
* Response Body:
``` {
    "status": "success",
    "message": "Buku berhasil dihapus"
}
```
## Pengujian
Ketika membangun Bookshelf API, tentu Anda perlu menguji untuk memastikan API berjalan sesuai dengan kriteria yang ada. 
Anda perlu meng-import kedua berkas tersebut pada Postman untuk menggunakannya. Caranya, ekstrak berkas yang sudah diunduh hingga menghasilkan dua berkas file JSON.
Kemudian pada aplikasi Postman, klik tombol import yang berada di atas panel kiri aplikasi Postman.
Kemudian klik tombol Upload Files untuk meng-import kedua berkas JSON hasil ekstraksi.
Setelah itu, Bookshelf API Test Collection dan Environment akan tersedia pada Postman Anda.
Environment
## Saran
Submission Anda akan dinilai oleh Reviewer guna menentukkan kelulusan Anda. Untuk lulus dari kelas ini, proyek Bookshelf API harus memenuhi seluruh pengujian otomatis pada Postman request yang bertanda [Mandatory]. Bila salah satu pengujiannya gagal, maka proyek Anda akan kami tolak.
Submission Anda akan dinilai oleh Reviewer dengan skala 1-5. Untuk mendapatkan nilai tinggi, silakan penuhi pengujian otomatis pada request yang bertanda [Optional]. Berikut detail dari fitur atau persyaratan opsional dari submission ini:
* Tambahkan fitur query parameters pada route GET /books (Mendapatkan seluruh buku). <br>
&nbsp;&nbsp;&nbsp;&#x25CB; ?name : Tampilkan seluruh buku yang mengandung nama berdasarkan nilai yang diberikan pada query ini. Contoh /books?name=”dicoding”, maka akan menampilkan daftar buku yang mengandung nama “dicoding” secara non-case sensitive  (tidak peduli besar dan kecil huruf). <br>
&nbsp;&nbsp;&nbsp;&#x25CB; ?reading : Bernilai 0 atau 1. Bila 0, maka tampilkan buku yang sedang tidak dibaca (reading: false). Bila 1, maka tampilkan buku yang sedang dibaca (reading: true). Selain itu, tampilkan buku baik sedang dibaca atau tidak. <br>
&nbsp;&nbsp;&nbsp;&#x25CB; ?finished : Bernilai 0 atau 1. Bila 0, maka tampilkan buku yang sudah belum selesai dibaca (finished: false). Bila 1, maka tampilkan buku yang sudah selesai dibaca (finished: true). Selain itu, tampilkan buku baik yang sudah selesai atau belum dibaca. <br>
* Menggunakan ESLint dan salah satu style guide agar gaya penulisan kode JavaScript lebih konsisten. Serta ketika dijalankan perintah npx eslint . tidak terdapat error yang muncul.
## Ketentuan Berkas Submission
* Berkas submission yang dikirim merupakan folder proyek dari Bookshelf API dalam bentuk ZIP. 
* Pastikan di dalam folder proyek yang Anda kirim terdapat berkas package.json.
* Pastikan Anda hapus dulu berkas node_modules pada folder proyek sebelum mengkompresi dalam bentuk ZIP.
## Submission Anda akan Ditolak bila
* Kriteria wajib Bookshelf API tidak terpenuhi.
* Ketentuan berkas submission tidak terpenuhi.
* Menggunakan port selain 9000.
* Pada package.json tidak tersedia script start.
* Script start menggunakan nodemon.
* Menggunakan database seperti Postgres, MSSQL, MySQL, MariaDB, atau MongooDB untuk menyimpan data. Pastikan submission Anda mudah dijalankan dan diuji tanpa reviewer perlu memasang database engine apa pun.
* Menggunakan JSON sebagai tempat penyimpanan data yang menyebabkan postman test menjadi gagal.
* Proyek yang Anda kirim tidak dapat dijalankan dengan baik (Reviewer menggunakan Node.js versi LTS 18.13.0).
* Menggunakan bahasa pemrograman dan teknologi lain, selain JavaScript dan Node.js.
* Menggunakan Framework Node.js selain Hapi Framework.
* Melakukan kecurangan seperti tindakan plagiasi.
