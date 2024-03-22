# Reflection
Fathan Naufal Adhitama (2206825965)

### Commit 1
> What is inside the handle_connection method?

Fungsi ini menerima koneksi TCP dalam parameter stream, yang kemudian akan dibaca menggunakan `BufReader` untuk membaca data dari stream.

1. `BufReader::new(&mut stream)`: Ini membuat BufReader baru yang akan membaca dari koneksi TCP yang disediakan.

2. `buf_reader.lines()`: Metode ini mengembalikan iterator yang akan menghasilkan baris-baris teks dari BufReader. Ini akan membaca data dari koneksi TCP, baris per baris.

3. `.map(|result| result.unwrap())`: Ini memetakan setiap hasil baris yang diperoleh dari iterator untuk menyingkirkan lapisan hasil Result dan mengambil nilai teks aktual. `unwrap()` digunakan di sini karena kita mengabaikan kemungkinan kesalahan.

4. `.take_while(|line| !line.is_empty())`: Ini mengambil baris-baris dari iterator selama baris tidak kosong. Ini berhenti mengambil baris saat menemui baris kosong.

5. `.collect()`: Ini mengumpulkan baris-baris yang telah dibaca menjadi sebuah `Vec<_>` yang disebut http_request.

6. `println!("Request: {:#?}", http_request)`: Ini mencetak http_request ke konsol dalam format debug ({:#?}). Ini berguna untuk melihat struktur data dari vektor http_request dengan cara yang lebih terstruktur.

Jadi, secara keseluruhan, fungsi handle_connection ini membaca setiap baris dari koneksi TCP yang diberikan hingga menemukan baris kosong (menandakan akhir permintaan HTTP), dan kemudian mencetak permintaan HTTP ke konsol.

### Commit 2
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