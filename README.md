# Reflection
Fathan Naufal Adhitama (2206825965)

### Commit 1 Reflection notes
> What is inside the handle_connection method?

Fungsi ini menerima koneksi TCP dalam parameter stream, yang kemudian akan dibaca menggunakan `BufReader` untuk membaca data dari stream.

1. `BufReader::new(&mut stream)`: Ini membuat BufReader baru yang akan membaca dari koneksi TCP yang disediakan.

2. `buf_reader.lines()`: Metode ini mengembalikan iterator yang akan menghasilkan baris-baris teks dari BufReader. Ini akan membaca data dari koneksi TCP, baris per baris.

3. `.map(|result| result.unwrap())`: Ini memetakan setiap hasil baris yang diperoleh dari iterator untuk menyingkirkan lapisan hasil Result dan mengambil nilai teks aktual. `unwrap()` digunakan di sini karena kita mengabaikan kemungkinan kesalahan.

4. `.take_while(|line| !line.is_empty())`: Ini mengambil baris-baris dari iterator selama baris tidak kosong. Ini berhenti mengambil baris saat menemui baris kosong.

5. `.collect()`: Ini mengumpulkan baris-baris yang telah dibaca menjadi sebuah `Vec<_>` yang disebut http_request.

6. `println!("Request: {:#?}", http_request)`: Ini mencetak http_request ke konsol dalam format debug ({:#?}). Ini berguna untuk melihat struktur data dari vektor http_request dengan cara yang lebih terstruktur.

Jadi, secara keseluruhan, fungsi handle_connection ini membaca setiap baris dari koneksi TCP yang diberikan hingga menemukan baris kosong (menandakan akhir permintaan HTTP), dan kemudian mencetak permintaan HTTP ke konsol.

### Commit 2 Reflection notes
![Commit 2 screen capture](/assets/images/commit2.jpg) 
Pada kode yang baru ditambahkan ke method `handle_connection`, 
```rust
let status_line = "HTTP/1.1 200 OK"; 
    let contents = fs::read_to_string("hello.html").unwrap(); 
    let length = contents.len(); 
 
    let response = 
        format!("{status_line}\r\nContent-Length: 
{length}\r\n\r\n{contents}"); 
 
    stream.write_all(response.as_bytes()).unwrap();
```
baris-baris ini akan mengirimkan HTTP Response ke client yang berisi `status_line`, panjang konten, dan konten HTML dari `hello.html`. response ini dikirimkan kembali ke klien melalui koneksi TCP dengan baris kode `stream.write_all(response.as_bytes()).unwrap();`

### Commit 3 Reflection notes
![Commit 3 screen capture](/assets/images/commit3.jpg) 
Berdasarkan langkah-langkah yang dijelaskan pada Chapter 20:
- Kita dapat memisahkan respons yang diberikan dengan melakukan pengecekan pada `request_line` apakah sama dengan request line ke path `/`. Jika iya, akan mengirimkan `hello.html`, jika tidak, akan ke `404.html`
- Kode yang awalnya ditulis perlu di-_refactor_ karena masih terdapat duplikasi kode. Dengan melakukan refactoring, blok if-else hanya akan berisi kode yang membedakan kedua kondisi saja dan menghilangkan kode yang bersifat redundan

### Commit 4 Reflection notes
Pada simulasi ini, terdapat 2 request yaitu path `/` dan `/sleep`. Jika kita mengakses path `/`, halaman akan langsung diload sementara path `/sleep` akan menunggu selama 10 detik sebelum diload. Akan tetapi, apabila kita mengakses path `/sleep` dulu kemudian path `/`, kedua path akan menunggu selama 10 detik karena path `/` harus menunggu hingga request sleep selesai diproses. Ini dapat terjadi karena server yang sekarang bersifat single-threaded yang berarti akan memproses request secara bergantian. server tidak akan memproses request kedua jika request pertama belum selesai. Masalah ini akan semakin parah apabila request bertambah banyak.

### Commit 5 Reflection notes
**_Try to understand how the ThreadPool works._**
Untuk menerapkan _multithreading_ pada program kita, salah satu cara yang dapat digunakan adalah dengan menggunakan ThreadPool. ThreadPool adalah kumpulan _spawned_ Thread yang menunggu dan siap untuk melakukan task. Ketika satu Thread sedang mengerjakan suatu request, Thread yang lain dapat mengerjakan request lain tanpa harus menunggu request pertama selesai. Dengan begitu, kita dapat meng-handle beberapa request dalam waktu yang bersamaan. Cara ini dapat meningkatkan Throughput dari program kita. Akan tetapi, Thread akan dibuat dengan jumlah yang terbatas (dalam contoh ini, hanya akan dibuat 4 thread) untuk melindungi program kita dari serangan Denial of Service (DoS).