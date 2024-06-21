# Final Project Teknologi Komputasi Awan
## Kelompok B6
- Revalina Fairuzy Azhari P. (5027231001)
- Chelsea Vania Hariyono (5027231003)
- Salsabila Rahmah (5027231005)
- Riskiyatul Nur Oktarani (5027231013)
- Agnes Zenobia Griselda P. (5027231034)
## Topik Masalah
Anda adalah seorang lulusan Teknologi Informasi, sebagai ahli IT, salah satu kemampuan yang harus dimiliki adalah **kemampuan merancang, membangun, mengelola aplikasi berbasis komputer menggunakan layanan awan untuk memenuhi kebutuhan organisasi.**

Pada suatu saat anda mendapatkan project untuk mendeploy sebuah aplikasi Sentiment Analysis dengan komponen Backend menggunakan python: [sentiment-analysis.py](https://github.com/fuaddary/fp-tka/blob/main/Resources/BE/sentiment-analysis.py) dengan spesifikasi yang telah ditentukan.

### Endpoints:
**1. Analyze Text**
  - **Endpoint**: ```POST /analyze```
  - **Description**: This endpoint accepts a text input and returns the sentiment score of the text.
  - **Request**
  ```
{
   "text": "Your text here"
}
```
  - **Response**
```
{
  "sentiment": <sentiment_score>
}
```
**2. Retrieve History**
- **Endpoint**: ```GET /history```
- **Description**: This endpoint retrieves the history of previously analyzed texts along with their sentiment scores.
- **Response**:
```
{
 {
   "text": "Your previous text here",
   "sentiment": <sentiment_score>
 },
 ...
}
```

Kemudian juga disediakan sebuah Frontend sederhana menggunakan [index.html](https://github.com/fuaddary/fp-tka/blob/main/Resources/FE/index.html) dan [styles.css](https://github.com/fuaddary/fp-tka/blob/main/Resources/FE/styles.css) dengan tampilan antarmuka sebagai berikut
<img width="1186" alt="image" src="https://github.com/revalina675/fp-cloud-computing-B6/assets/151063684/e55a8f07-bdfa-439e-ab2a-c2b2282ef6b1">
Kemudian anda diminta untuk mendesain arsitektur cloud yang sesuai dengan kebutuhan aplikasi tersebut. Apabila dana maksimal yang diberikan adalah **1 juta rupiah per bulan (65 US$) konfigurasi cloud terbaik seperti apa yang bisa dibuat?**

## Rancangan Arsitektur Awan
### Rancangan:
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/151063684/eb25f983-125e-485a-b123-223c10e690a5)

# Rencana Harga
| Nama        | Deskripsi                           | Harga             |
|-----------------|---------------------------------------|-------------------|
| **Load Balancer** | Instance: 1 Droplet                   | $12/bulan         |
|                 | Spec: 1vCPU, 1GB RAM                  |                   |
| **Virtual Machines** | Instance: 2 Droplets                  | $24/bulan         |
|                 | Spec: 2vCPU, 2GB RAM masing-masing    |                   |
|                 | Worker: $18/bulan x 2                 |                   |
| **Database**    | Instance: 1 Droplet                   | $18/bulan         |
|                 | Spec: 2vCPU, 2GB RAM                  |                   |
|   |    | **Total Biaya: $60/bulan** |

## Implementasi
## Hasil Uji
## Kesimpulan
