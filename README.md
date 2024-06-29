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
| **Virtual Machines** | Instance: 2 Droplets                  | $36/bulan         |
|                 | Spec: 2vCPU, 2GB RAM masing-masing    |                   |
|                 | Worker: $18/bulan x 2                 |                   |
| **Database**    | Instance: 1 Droplet                   | $12/bulan         |
|                 | Spec: 2vCPU, 2GB RAM                  |                   |
|   |    | **Total Biaya: $60/bulan** |

## Implementasi Rancangan
### SET UP Konfigurasi Database MongoDB
1. Sambungkan terminal windows ke terminal vm, lalu masukkan password droplets ```ssh root@152.42.214.44```
   ![Screenshot 2024-06-24 053410](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/fe8746e9-62df-46da-9a1a-883e6b06381c)

2. Lakukan instalasi MongoDB
```
sudo apt update

# Install dependency
sudo apt install gnupg wget apt-transport-https ca-certificates software-properties-common
echo "deb http://security.ubuntu.com/ubuntu focal-security main" | sudo tee /etc/apt/sources.list.d/focal-security.list
sudo apt-get update
sudo apt-get install libssl1.1

# Install mongodb
curl -fsSL https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
apt-key list
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
sudo apt update
sudo apt install mongodb-org
```

3. Ennable server MongoDB
```
sudo systemctl start mongod
sudo systemctl enable mongod
```

4. Atur konfigurasi MongoDB, ganti port ke 0.0.0.0 agar dapat diakses dari port manapun
```
sudo nano /etc/mongod.conf
```
![Screenshot 2024-06-20 123017](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/88ee5509-e78b-476f-bd69-1caf9f20a9e0)

5. Restart service MongoDB
```sudo systemctl restart mongodB```
6. Izinkan akses port pada firewall
```sudo ufw allow 27017```
7. Akses shell MongoDB
```mongo```
8. Masuk sebagai user Admin
```use admin```
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/baaa8480-4352-425e-ac3a-9ddd917e58b8)

10. Buat user Admin
```
db.createUser({
user: "KelompokTKA_B6",
pwd: "KelompokTKA_B6",
roles: [{ role: "userAdminAnyDatabase", db: "admin" }]

})
```

10. Cek user yang baru dibuat
```db.getUser("KelompokTKA_B6")```
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/c41945c6-00f6-4528-b6b0-727cfe34b007)

12. Sambungkan ke MongoDBCompass
```mongodb://152.42.214.44:27017```
13. Jika sudah bisa terhubung dengan Compass maka konfigurasi database berhasil.
    ![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/6b16882e-ec14-490d-98c4-30c35ec46049)


### SET UP Konfigurasi Worker 1
1. Sambungkan terminal windows ke terminal vm, lalu masukkan password droplets
```ssh root@128.199.142.10```
![Screenshot 2024-06-24 055856](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/e56d69eb-32f3-42b7-8e76-f97ec0cf7839)

2. Download resource keperluan dari github
```
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/index.html
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/styles.css
```

3. Lakukan command berikut untuk instalasi nginx
```
sudo apt update
sudo apt install nginx -y
```

4. Instalasi dependency python, lalu masuk ke shell ```myenv```
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

6. Gabungkan file styles.css ke index.html lalu ubah cara fetch pada index.html agar mengarah ke ip worker
```html
   <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sentiment Analysis</title>
    <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="styles.css">
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f4f4f9;
            margin: 0;
            padding: 20px;
        }

        h1 {
            color: #333;
            margin-bottom: 20px;
        }

        .form-container, .history-container {
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        .history-container {
            height: 400px; /* Fixed height for history container */
            overflow-y: auto;
        }

        .history-container h2 {
            margin-bottom: 20px;
        }

        .history-item {
            background: #fff;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            margin-bottom: 10px;
        }

        #result {
            margin-top: 20px;
            font-size: 18px;
            color: #333;
        }

        .row {
            display: flex;
            align-items: stretch;
            height: 400px; /* Same height as history container */
        }

        .row .col-md-6 {
            display: flex;
            flex-direction: column;
        }

        /* Highlighting styles */
        .positive {
            background-color: #d4edda;
            border-color: #c3e6cb;
            color: #155724;
        }

        .negative {
            background-color: #f8d7da;
            border-color: #f5c6cb;
            color: #721c24;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="text-center">Sentiment Analysis</h1>
        <div class="row">
            <div class="col-md-6">
                <div class="form-container">
                    <form id="sentiment-form">
                        <div class="form-group">
                            <textarea id="text-input" class="form-control" rows="4" placeholder="Enter text here..."></textarea>
                        </div>
                        <button type="submit" class="btn btn-success btn-block">Analyze</button>
                    </form>
                    <p id="result" class="text-center mt-4"></p>
                </div>
            </div>
            <div class="col-md-6">
                <div class="history-container">
                    <h2 class="text-center">History</h2>
                    <ul id="history" class="list-group mt-3"></ul>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.getElementById('sentiment-form').addEventListener('submit', async function(event) {
            event.preventDefault();
            const text = document.getElementById('text-input').value;
            try {
                const response = await fetch('http://128.199.142.10/analyze', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({ text }),
                });
                if (!response.ok) throw new Error('Network response was not ok');
                const result = await response.json();
                const resultElement = document.getElementById('result');
                resultElement.textContent = `Sentiment Score: ${result.sentiment}`;
                resultElement.className = result.sentiment > 0 ? 'positive' : 'negative';
                fetchHistory();
            } catch (error) {
                console.error('There has been a problem with your fetch operation:', error);
            }
        });

        async function fetchHistory() {
            try {
                const response = await fetch('http://128.199.142.10/history');
                if (!response.ok) throw new Error('Network response was not ok');
                const history = await response.json();
                const historyList = document.getElementById('history');
                historyList.innerHTML = '';
                history.forEach(item => {
                    const listItem = document.createElement('li');
                    listItem.className = `list-group-item history-item ${item.sentiment > 0 ? 'positive' : 'negative'}`;
                    listItem.textContent = `Text: ${item.text}, Sentiment: ${item.sentiment}`;
                    historyList.appendChild(listItem);
                });
            } catch (error) {
                console.error('There has been a problem with your fetch operation:', error);
            }
        }

        // Fetch history on page load
        fetchHistory();
    </script>
</body>
</html>
```
   
7. Atur konfigurasi menggunakan ```sudo nano /etc/nginx/sites-enabled/default```
   ![Screenshot 2024-06-20 121939](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/4ae4b105-d965-4497-9b14-196945caa0bc)

   Tambahkan routing ke endpoint /analyze dan /history
8. Konfigurasikan IP database pada file sentiment-analysis.py agar tersambung dengan worker ```sudo nano sentiment-analysis,py```
![Screenshot 2024-06-20 130556](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/113a6d89-c98f-41bd-ab4b-831f02de8bdd)

9. Restart service nginx
```
sudo service nginx restart
```
10. Jalankan sentiment-analysis.py
    ```
    python sentiment-analysis.py
    ```
![Screenshot 2024-06-20 130752](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/b1a1365b-cc18-4a79-8760-085317fa3571)

12. Coba lakukan query pada ```http://128.199.142.10``` untuk testing apakah sentiment-analysis berjalan dengan lancar
![Screenshot 2024-06-20 132818](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/8a584aed-1692-45bc-8f09-8cccbe2e7b39)
![Screenshot 2024-06-21 112745](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/b49862a1-0ba5-4b75-9469-ec07f0e80cc9)



### SET UP Konfigurasi Worker 2
1. Sambungkan terminal windows ke terminal vm, lalu masukkan password droplets
```
ssh root@128.199.140.87
```
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/fc4e54a2-5425-4f24-b9e7-07c507d6e383)


2. Download resource keperluan dari github
```
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/index.html
wget https://raw.githubusercontent.com/fuaddary/fp-tka/main/Resources/FE/styles.css
```
3. Lakukan beberapa command berikut untuk instalasi nginx
```
sudo apt update
sudo apt install nginx -y
```

4. Instalasi dependency python, lalu masuk ke shell ```myenv```
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

6.  Gabungkan file styles.css ke index.html lalu ubah cara fetch pada index.html agar mengarah ke ip worker
```html
   <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sentiment Analysis</title>
    <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="styles.css">
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f4f4f9;
            margin: 0;
            padding: 20px;
        }

        h1 {
            color: #333;
            margin-bottom: 20px;
        }

        .form-container, .history-container {
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        .history-container {
            height: 400px; /* Fixed height for history container */
            overflow-y: auto;
        }

        .history-container h2 {
            margin-bottom: 20px;
        }

        .history-item {
            background: #fff;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            margin-bottom: 10px;
        }

        #result {
            margin-top: 20px;
            font-size: 18px;
            color: #333;
        }

        .row {
            display: flex;
            align-items: stretch;
            height: 400px; /* Same height as history container */
        }

        .row .col-md-6 {
            display: flex;
            flex-direction: column;
        }

        /* Highlighting styles */
        .positive {
            background-color: #d4edda;
            border-color: #c3e6cb;
            color: #155724;
        }

        .negative {
            background-color: #f8d7da;
            border-color: #f5c6cb;
            color: #721c24;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="text-center">Sentiment Analysis</h1>
        <div class="row">
            <div class="col-md-6">
                <div class="form-container">
                    <form id="sentiment-form">
                        <div class="form-group">
                            <textarea id="text-input" class="form-control" rows="4" placeholder="Enter text here..."></textarea>
                        </div>
                        <button type="submit" class="btn btn-success btn-block">Analyze</button>
                    </form>
                    <p id="result" class="text-center mt-4"></p>
                </div>
            </div>
            <div class="col-md-6">
                <div class="history-container">
                    <h2 class="text-center">History</h2>
                    <ul id="history" class="list-group mt-3"></ul>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.getElementById('sentiment-form').addEventListener('submit', async function(event) {
            event.preventDefault();
            const text = document.getElementById('text-input').value;
            try {
                const response = await fetch('http://128.199.140.87/analyze', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({ text }),
                });
                if (!response.ok) throw new Error('Network response was not ok');
                const result = await response.json();
                const resultElement = document.getElementById('result');
                resultElement.textContent = `Sentiment Score: ${result.sentiment}`;
                resultElement.className = result.sentiment > 0 ? 'positive' : 'negative';
                fetchHistory();
            } catch (error) {
                console.error('There has been a problem with your fetch operation:', error);
            }
        });

        async function fetchHistory() {
            try {
                const response = await fetch('http://128.199.140.87/history');
                if (!response.ok) throw new Error('Network response was not ok');
                const history = await response.json();
                const historyList = document.getElementById('history');
                historyList.innerHTML = '';
                history.forEach(item => {
                    const listItem = document.createElement('li');
                    listItem.className = `list-group-item history-item ${item.sentiment > 0 ? 'positive' : 'negative'}`;
                    listItem.textContent = `Text: ${item.text}, Sentiment: ${item.sentiment}`;
                    historyList.appendChild(listItem);
                });
            } catch (error) {
                console.error('There has been a problem with your fetch operation:', error);
            }
        }

        // Fetch history on page load
        fetchHistory();
    </script>
</body>
</html>
```
   

7. Atur konfigurasi menggunakan ```sudo nano /etc/nginx/sites-enabled/default```
   ![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/affb84fe-8f80-4c26-81dd-da1763655954)

   Tambahkan routing ke endpoint /analyze dan /history
9. Konfigurasikan IP database pada file sentiment-analysis.py agar tersambung dengan worker ```sudo nano sentiment-analysis.py```
 ![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/ecc56984-5244-4547-a16b-f62f8bd3f9c8)

10.  Restart service nginx
```
sudo service nginx restart
```

10.  Jalankan sentiment-analysis.py
```
python sentiment-analysis.py
```
![Screenshot 2024-06-20 135302](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/f3acb5d9-63a9-4ab1-a4b5-13a3da177a48)

12. Coba lakukan query pada ```http://128.199.140.87``` untuk testing apakah sentiment-analysis berjalan dengan lancar
    ![Screenshot 2024-06-20 135410](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/f75becfc-ef01-4745-821e-05abc07860d6)

    ![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/1c34a02b-4948-416d-938d-ccf53127409d)




### Konfigurasi Load-Balancer menggunkan algoritma Round-Robin
1. Sambungkan terminal VM ```ssh root@178.128.96.149```, lalu masukkan password droplets
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/fd2a7232-2b1b-422c-8bbc-08bfd944ed59)


2.  Lakukan command berikut untuk instalasi nginx
```
sudo apt update
sudo apt install nginx -y
```

3. Konfigurasikan file default pada /etc/nginx/sites-enabled/default ```sudo nano /etc/nginx/sites-enabled/default```
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/f34d7845-927a-4723-a250-d11e2fb067a3)

4.  Restart service nginx
```
sudo service nginx restart
```

5. Jika sudah test load-balancer dengan refresh page berkali-kali
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/9183e892-8f6e-4c1c-889d-5fdbdd8dc7e8)
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/31d4bb5e-76b3-4788-9176-da23d45d810b)




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


![Screenshot 2024-06-29 143542](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/f680c001-7c2e-4af5-bc8b-bf9d0e77e1ff)


## Uji peak concurrency dengan spawn rate 50
![Screenshot 2024-06-21 120106](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/ddbf57d1-63a4-42d2-b2f8-74dbae626c5c)

![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/083760b2-0e97-4276-96f8-1e4306f2d06a)


## Uji peak concurrency dengan spawn rate 100
![Screenshot 2024-06-21 120515](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/5874922f-9eff-405c-9d65-9b00f40f495e)

![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/a3002a09-a529-4c5e-ae92-2b7f6bfa01de)


## Uji peak concurrency dengan spawn rate 200
![Screenshot 2024-06-21 120805](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/9f748926-6fc6-47da-b49c-08ee47359d9a)

![Screenshot 2024-06-29 143904](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/f559b227-916d-4243-aed1-1fefa8411280)


## Uji peak concurrency dengan spawn rate 500
![Screenshot 2024-06-21 121416](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/05dc68ab-1982-4a9a-8275-3552c0e26367)

![Screenshot 2024-06-29 143648](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/5d883da6-c1d3-46b1-be47-6f4213ede175)


## Tampilan Project DigitalOcean
![image](https://github.com/revalina675/fp-cloud-computing-B6/assets/150202762/84ac0aed-d6bd-4502-94d3-2c64e10a0bce)

## Kesimpulan

Algoritme Round Robin pad pengujian kami mencapi RPS maksimum di angka 40,4. Algoritma Round Robin dikenal memiliki kinerja unggul dalam menangani tingkat permintaan yang tinggi per detik. Ini cocok untuk situasi di mana server memiliki kapasitas dan karakteristik kinerja yang serupa, dengan tujuan untuk meningkatkan throughput secara maksimal. Selain itu, Round Robin mencapai tingkat konkurensi puncak dan mampu melakukan skalabilitas yang efektif dalam kondisi tertentu.

## Saran
- **Analisis Kebutuhan**: Sebelum memilih algoritme penjadwalan, lakukan analisis mendalam terhadap kebutuhan sistem dan karakteristik beban kerja. Kemudian, pilih algoritme yang paling sesuai berdasarkan itu.
- **Monitoring dan Evaluasi**: Implementasikan sistem monitoring untuk terus mengawasi kinerja server dan melakukan evaluasi berkala. Dengan demikian, Anda dapat mengidentifikasi bottleneck dan melakukan penyesuaian yang diperlukan.
- **Load Balancer**: Pastikan menggunakan load balancer yang dapat mendistribusikan beban secara efisien. Load balancer yang baik dapat meningkatkan kinerja dan stabilitas sistem secara keseluruhan.
- **Peningkatan Kapasitas**: Jika tingkat permintaan terus meningkat, pertimbangkan untuk menambah kapasitas server atau menggunakan teknik skalabilitas horizontal dengan menambah lebih banyak server.
- **Pengujian dan Simulasi**: untuk melakukan pengujian dan simulasi sebanyak mungkin. Hal ini akan membantu menemukan setiap potensi masalah dan memastikan bahwa sistem ini mampu menangani kondisi puncak.
- **Optimasi Daya**: mengoptimalkan penggunaan sumber daya, seperti CPU dan memori, sehingga setiap server dapat berjalan pada kapasitas maksimum tanpa kelebihan muatan.
- **Kegagalan atas dan redundansi**: Implementasikan mekanisme failover dan redundansi untuk memastikan ketersediaan tinggi dan menghindari downtime jika terjadi kegagalan pada salah satu server.

Dengan memperhatikan saran-saran di atas, diharapkan proyek di masa mendatang lebih lancar dan berjalan optimal.

## Link Youtube
[Revisi Kelompok B6](https://youtu.be/7M9Hew_UGF8)
