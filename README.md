# Project Simple ETL with Pandas
## Data Engineer - ETL Project

### "Mengolah data pendaftar hackathon yang diselenggarakan oleh DQLab bernama DQThon"
***

## Pengantar
Di masa pandemi seperti ini, kompetisi coding seperti *Competitive Programming* maupun *Hackathon* banyak diselenggarakan karena sangat memungkinkan untuk dilakukan secara online.

***Hackathon*** merupakan kompetisi membuat perangkat lunak *(software)* yang dilaksanakan secara marathon yang biasanya diikuti secara tim. Umumnya, peserta *hackathon* diminta untuk mengembangkan *platform* (*mobile, web, desktop,* dll.) dalam kurun waktu tertentu untuk menyelesaikan permasalahan yang sudah ditetapkan/didefinisikan oleh penyelenggara ataupun berdasarkan tema yang dipilih oleh tim tersebut.

Untuk bisa mengikuti *hackathon* dari suatu instansi, calon peserta diwajibkan untuk mendaftarkan diri mereka pada situs/*form* tertentu dengan memasukkan beberapa informasi yang diminta oleh penyelenggara tersebut.

## Dataset Project yang Akan Dikerjakan
Pada proyek kali ini, diminta untuk mengolah data pendaftar *hackathon* yang diselenggarakan oleh **DQLab** bernama **DQThon**.

Dataset ini terdiri dari 5000 baris data (5000 pendaftar) dengan format CSV *(Comma-separated values)* dan memiliki beberapa kolom diantaranya:


1. **participant_id**: ID dari peserta/partisipan *hackathon*. Kolom ini bersifat *unique* sehingga antar peserta pasti memiliki ID yang berbeda
2. **first_name**: nama depan peserta
3. **last_name**: nama belakang peserta
4. ***birth_date***: tanggal lahir peserta
5. ***address***: alamat tempat tinggal peserta
6. ***phone_number***: nomor hp/telepon peserta
7. ***country***: negara asal peserta
8. ***institute***: institusi peserta saat ini, bisa berupa nama perusahaan maupun nama universitas
9. ***occupation***: pekerjaan peserta saat ini
10. ***register_time***: waktu peserta melakukan pendaftaran *hackathon* dalam *second*

Pada proyek ini nantinya diminta untuk menghasilkan beberapa kolom dengan memanfaatkan kolom-kolom yang ada, sehingga akhir dari proyek ini berupa hasil transformasi data dengan beberapa kolom baru selain dari 10 kolom diatas.

## Extract, Transform dan Load (ETL)
Merupakan kumpulan proses untuk "memindahkan" data dari satu tempat ke tempat lain.
Tempat yang dimaksud adalah dari sumber data (bisa berupa database aplikasi, *file, logs*, database dari *3rd party*, dan lainnya) ke data *warehouse*.



**Dataset:** 
```
https://storage.googleapis.com/dqlab-dataset/dqthon-participants.csv.
```
## Extract
Extract merupakan proses meng-ekstraksi data dari sumber, sumber data ini bisa berupa relational data (SQL) atau tabel, nonrelational (NoSQL) maupun yang lainnya.

```
import pandas as pd
df_participant = pd.read_csv('https://storage.googleapis.com/dqlab-dataset/dqthon-participants.csv')
```
## Transform
Transform merupakan proses melakukan transformasi data, atau perubahan terhadap data. Umumnya seperti:
* Merubah nilai dari suatu kolom ke nilai baru,
* Menciptakan kolom baru dengan memanfaatkan kolom lain,
* *Transpose* baris menjadi kolom (atau sebaliknya),
* Mengubah format data ke bentuk yang lebih standard (contohnya, kolom *date*, maupun *datetime* yang biasanya memiliki nilai yang tidak standard maupun nomor HP yang biasanya memiliki nilai yang tidak sesuai format standardnya), dan lainnya. 

## Transform Bagian I - Kode Pos
---
Ada permintaan datang dari tim logistik bahwa mereka membutuhkan kode pos dari peserta agar pengiriman piala lebih mudah dan cepat sampai. Maka dari itu buatlah kolom baru bernama postal_code yang memuat informasi mengenai kode pos yang diambil dari alamat peserta (kolom *address*).

```
import pandas as pd
df_participant = pd.read_csv('https://storage.googleapis.com/dqlab-dataset/dqthon-participants.csv')
# cek info untuk mengetahui informasi pada dataframe
df_participant.info()
```
output:
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5000 entries, 0 to 4999
Data columns (total 10 columns):
 #   Column          Non-Null Count  Dtype  
---  ------          --------------  -----  
 0   participant_id  5000 non-null   object 
 1   first_name      5000 non-null   object 
 2   last_name       5000 non-null   object 
 3   birth_date      5000 non-null   object 
 4   address         5000 non-null   object 
 5   phone_number    5000 non-null   object 
 6   country         5000 non-null   object 
 7   institute       5000 non-null   object 
 8   occupation      5000 non-null   object 
 9   register_time   5000 non-null   float64
dtypes: float64(1), object(9)
memory usage: 390.8+ KB
```
Mengambil kode pos menggunakan regex dari kolom *address* kemudian disimpan ke kolom baru *postal_kode* dan cek kembali menggunakan .info
```
#Masukkan regex didalam fungsi extract
df_participant['postal_code'] = df_participant['address'].str.extract(r'(\d+)$') 
print(df_participant.info())
```
output:
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5000 entries, 0 to 4999
Data columns (total 11 columns):
 #   Column          Non-Null Count  Dtype  
---  ------          --------------  -----  
 0   participant_id  5000 non-null   object 
 1   first_name      5000 non-null   object 
 2   last_name       5000 non-null   object 
 3   birth_date      5000 non-null   object 
 4   address         5000 non-null   object 
 5   phone_number    5000 non-null   object 
 6   country         5000 non-null   object 
 7   institute       5000 non-null   object 
 8   occupation      5000 non-null   object 
 9   register_time   5000 non-null   float64
 10  postal_code     5000 non-null   object 
dtypes: float64(1), object(10)
memory usage: 429.8+ KB
None
```
keterangan:
pada kolom no 10, kolom benama postal_code berhasil dibuat dengan memuat informasi kode pos yang diambil dari kolom *address*


## Transform Bagian II - Kota
---
Selain kode pos, mereka juga membutuhkan kota dari peserta.

```
# cek info, dan 5 baris pertama untuk mengidentifikasi kota dari peserta
df_participant.info()
df_participant.head()

output:

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5000 entries, 0 to 4999
Data columns (total 11 columns):
 #   Column          Non-Null Count  Dtype  
---  ------          --------------  -----  
 0   participant_id  5000 non-null   object 
 1   first_name      5000 non-null   object 
 2   last_name       5000 non-null   object 
 3   birth_date      5000 non-null   object 
 4   address         5000 non-null   object 
 5   phone_number    5000 non-null   object 
 6   country         5000 non-null   object 
 7   institute       5000 non-null   object 
 8   occupation      5000 non-null   object 
 9   register_time   5000 non-null   float64
 10  postal_code     5000 non-null   object 
dtypes: float64(1), object(10)
memory usage: 429.8+ KB
```
```
![Screenshot at 2022-07-02 11-02-33](https://user-images.githubusercontent.com/73177538/176985831-957704aa-6725-4f7d-a584-6ca0a35c0132.png)

```
