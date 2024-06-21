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

### Rencana Harga
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
### Konfigurasi VM-3 (Database)
1. Sambungkan terminal windows ke terminal vm. ```ssh root@146.190.102.47```
(((GAMBAR)))
2. Masukkan password vm
3. Install MongoDB
```
sudo apt update
sudo apt upgrade

# Install dependency
sudo apt install gnupg wget apt-transport-https ca-certificates software-properties-common
echo "deb http://security.ubuntu.com/ubuntu focal-security main" | sudo tee /etc/apt/sources.list.d/focal-security.list
sudo apt-get update
sudo apt-get install libssl1.1

# Install mongodb
wget -qO- https://pgp.mongodb.com/server-7.0.asc | gpg --dearmor | sudo tee /usr/share/keyrings/mongodb-server-7.0.gpg >/dev/null
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/7.0 multiverse" | sudo tee -a /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt update
sudo apt install mongodb-org -y
```

3. Enable MongoDB
```
sudo systemctl start mongod
sudo systemctl enable mongod
```

4. Konfigurasi MongoDB
```
sudo nano /etc/mongod.conf
```
((GAMBAR))

5. Restart service MongoDB
```sudo systemctl restart mongodB```
6. Buka port pada firewall
```sudo ufw allow 27017```
7. Buka shell MongoDB
```mongo```
8. Masuk sebagai user Admin
```use admin```
9. Buat user Admin
```
db.createUser({
user: "KelompokTKA2A",
pwd: "KelompokTKA2A",
roles: [{ role: "userAdminAnyDatabase", db: "admin" }]

})
```

10. Cek user yang baru dibuat
```db.getUser("KelompokTKA2A")```
11. Sambungkan ke MongoDBCompass
```mongodb://146.190.102.47:27017```
12. Jika sudah bisa terhubung dengan Compass maka konfigurasi database berhasil.

### Konfigurasi VM-1 (Worker 1)
1. Sambungkan terminal windows ke terminal vm.
```ssh root@152.42.226.87 image```
Masukkan password vm.
2. Download semua resource keperluan dari github
```
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/index.html
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/styles.css
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/BE/sentiment-analysis.py
```

3. Lakukan beberapa command berikut untuk install nginx
```
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```

4. Install dependency python
```
sudo apt update
sudo apt install python3 -y
sudo apt install python3-pip -y
sudo apt install python3.12-venv

python3 -m venv myenv
source myenv/bin/activate

pip install flask
pip install flask_cors
pip install gunicorn
pip install flask_pymongo
pip install textblob
pip install pymongo
pip install gevent
```

5. Pindahkan index.html kedalam /var/www/html
```
mv index.html /var/www/html/index.html
```

6. Ubah cara fetch pada index.html agar mengarah ke ip worker
GAMBAR
7. Konfigurasikan /etc/nginx/sites-enabled/default
image
Tambahkan routing ke endpoint /analyze dan /history
8. Konfigurasikan ip database pada file sentiment-analysis.py agar tersambung
image
9. Restart nginx
```
sudo service nginx restart
```
10. Jalankan sentiment-analysis.py
image
11. Coba lakukan query untuk mengetes apakah berjalan dengan lancar
image
Jika muncul seperti gambar maka konfigurasi benar.

### Konfigurasi VM-2 (Worker 2)
1. Sambungkan terminal windows ke terminal vm.
```
ssh root@152.42.229.121
```
image
Masukkan password vm.

2. Download semua resource keperluan dari github
```
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/index.html
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/styles.css
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/BE/sentiment-analysis.py
```
3. Lakukan beberapa command berikut untuk install nginx
```
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```

4. Install dependency python
```
sudo apt update
sudo apt install python3 -y
sudo apt install python3-pip -y
sudo apt install python3.12-venv

python3 -m venv myenv
source myenv/bin/activate

pip install flask
pip install flask_cors
pip install gunicorn
pip install flask_pymongo
pip install textblob
pip install pymongo
pip install gevent
```

5. Pindahkan index.html kedalam /var/www/html
```
mv index.html /var/www/html/index.html
```

6. Ubah cara fetch pada index.html agar mengarah ke ip worker
image
image

7.  Konfigurasikan /etc/nginx/sites-enabled/default
image

8.  Tambahkan routing ke endpoint /analyze dan /history
Konfigurasikan ip database pada file sentiment-analysis.py agar tersambung
image

9.  Jika sudah restart nginx
```
bash sudo service nginx restart
```

10.  Jalankan sentiment-analysis.py
image

11. Coba lakukan query untuk mengetes apakah berjalan dengan lancar
image
Jika muncul seperti gambar maka konfigurasi benar.


### Konfigurasi VM-4 (Load-Balancer 1 Round-Robin)
1. Sambungkan terminal VM.
image

2.  Lakukan beberapa command berikut untuk install nginx
```
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```

3. Konfigurasikan file default pada /etc/nginx/sites-enabled/default
image

4.  Restart service nginx
```
sudo service nginx restart
```

5. Jika sudah test load-balancer dengan refresh page berkali-kali
((GAMBAR))
((GAMBAR))


## Hasil Uji
## Uji Endpoint /analyze
128.199.142.10 (worker-1)
![Screenshot 2024-06-20 174051](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/e886869d-ac4a-4ac0-bfff-d1ca8015eb5a)

128.199.140.87 (worker-2)
![Screenshot 2024-06-20 174235](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/0df37291-fae7-46e2-ad74-ce8ea4123283)

## Uji Endpoint /history
128.199.142.10 (worker-1)
![Screenshot 2024-06-20 173715](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/c7455fcc-abb7-4f0a-aa69-7c57cb7c3f6b)

128.199.140.87 (worker-2)
![Screenshot 2024-06-20 174626](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/1ee186c2-138a-4081-9e74-dcce11958395)


## Uji load testing locust
## Uji RPS dalam 60s
![Screenshot 2024-06-21 114837](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/bdd7c208-e62a-492c-9c6e-c9ab8700bb52)

![Screenshot 2024-06-21 115830](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/0cb8236b-afbc-4f91-961e-ac15505b4705)

## Uji peak concurrency dengan spawn rate 50
![Screenshot 2024-06-21 120106](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/ddbf57d1-63a4-42d2-b2f8-74dbae626c5c)

![Screenshot 2024-06-21 120411](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/e4600546-ab1a-4e6a-9dff-ab1a9de93678)

## Uji peak concurrency dengan spawn rate 100
![Screenshot 2024-06-21 120515](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/5874922f-9eff-405c-9d65-9b00f40f495e)

![Screenshot 2024-06-21 120712](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/21e711b6-4c08-4e87-9a37-fe0ed8c49455)

## Uji peak concurrency dengan spawn rate 200
![Screenshot 2024-06-21 120805](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/9f748926-6fc6-47da-b49c-08ee47359d9a)

![Screenshot 2024-06-21 121019](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/7a9726f3-f40c-4f44-b120-eaf7b648dfa3)

## Uji peak concurrency dengan spawn rate 500
![Screenshot 2024-06-21 121416](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/05dc68ab-1982-4a9a-8275-3552c0e26367)

![Screenshot 2024-06-21 121332](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/0eac6d55-0a7f-400c-a76a-a99006017adf)


## Kesimpulan

Algoritme Round Robin pad pengujian kami mencapi RPS maksimum di angka 40,4. Algoritma Round Robin dikenal memiliki kinerja unggul dalam menangani tingkat permintaan yang tinggi per detik. Ini cocok untuk situasi di mana server memiliki kapasitas dan karakteristik kinerja yang serupa, dengan tujuan untuk meningkatkan throughput secara maksimal. Selain itu, Round Robin mencapai tingkat konkurensi puncak dan mampu melakukan skalabilitas yang efektif dalam kondisi tertentu.
