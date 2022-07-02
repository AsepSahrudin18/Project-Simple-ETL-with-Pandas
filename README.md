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
Ada permintaan datang dari tim logistik bahwa mereka membutuhkan kode pos dari peserta agar pengiriman piala lebih mudah dan cepat sampai. Maka dari itu buatlah kolom baru bernama **postal_code** yang memuat informasi mengenai kode pos yang diambil dari alamat peserta (kolom *address*).

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

```
```
# Mengambil kota dari peserta dengan menggunakan regex dari kolom address lalu disimpan kedalam kolom baru bernama city
# lalu cek info untuk memastikan kembali data kota dari kolom address yang telah di ambil ke kolom city
df_participant['city'] = df_participant['address'].str.extract(r'(?<=\n)(\w.+)(?=,)')
```
output:

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5000 entries, 0 to 4999
Data columns (total 12 columns):
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
 11  city            5000 non-null   object 
dtypes: float64(1), object(11)
memory usage: 468.9+ KB
```
keterangan:
data kota berhasil diambil dari kolom address pada kolom *city* no. 11 pada info tersebut.

## Transform Bagian III - Github
---
Salah satu parameter untuk mengetahui proyek apa saja yang pernah dikerjakan oleh peserta yaitu dari *git repository* mereka.

studi kasus:
Diketahui bahwa profil github mereka merupakan gabungan dari **first_name** dan **last_name** yang sudah di-*lowercase*. 

* Tugas kali ini yaitu membuat kolom baru bernama **github_profile** yang merupakan *link* profil *github* dari peserta.

```
# Cek 5 baris pertama dari df_participant untuk mengidentifikasi dataframe terlebih dahulu
df_participant = pd.read_csv('https://storage.googleapis.com/dqlab-dataset/dqthon-participants.csv')
df_participant['postal_code'] = df_participant['address'].str.extract(r'(\d+)$')
df_participant['city'] = df_participant['address'].str.extract(r'(?<=\n)(\w.+)(?=,)') 
df_participant.head()
```
```
df_participant['github_profile'] = 'https://github.com/' + df_participant['first_name'].str.lower() + df_participant['last_name'].str.lower()
df_participant.head()
```
## Transform Bagian IV - Nomor Handphone
---
Menentukan format nomor handphone yang benar. Pada kasus ini melakukan cleansing pada *phone number* untuk standarisasi data nomor telpon:
* Jika awalan nomor HP berupa angka 62 atau +62 yang merupakan kode telepon Indonesia, maka diterjemahkan ke 0.
* Tidak ada tanda baca seperti kurung buka, kurung tutup, strip⟶ ()-
* Tidak ada spasi pada nomor HP nama kolom untuk menyimpan hasil *cleansing* pada nomor HP yaitu **cleaned_phone_number**

```
df_participant['cleaned_phone_number'] = df_participant['phone_number'].str.replace(r'^(\+62|62)', '0')
df_participant['cleaned_phone_number'] = df_participant['cleaned_phone_number'].str.replace(r'[()-]', '')
df_participant['cleaned_phone_number'] = df_participant['cleaned_phone_number'].str.replace(r'\s+', '')
# mengecek 5 baris pertama dari phone number
df_participant.head()
```
## Transform Bagian V - Nama Tim
---
Dataset saat ini belum memuat nama tim, dan rupanya dari tim Data Analyst membutuhkan informasi terkait nama tim dari masing-masing peserta.

studi kasus:
Diketahui bahwa nama tim merupakan gabungan nilai dari kolom ***first_name, last_name, country dan institute***.

Tugas:
Membuat kolom baru dengan nama ***team_name*** yang memuat informasi nama tim dari peserta.

```
def func(col):
    abbrev_name = "%s%s"%(col['first_name'][0],col['last_name'][0]) #Singkatan dari Nama Depan dan Nama Belakang dengan mengambil huruf pertama
    country = col['country']
    abbrev_institute = '%s'%(''.join(list(map(lambda word: word[0], col['institute'].split())))) #Singkatan dari value di kolom institute
    return "%s-%s-%s"%(abbrev_name,country,abbrev_institute)

df_participant['team_name'] = df_participant.apply(func, axis=1)
# cek 5 baris pertama untuk memastikan
df_participant.head()
```

## Transform Bagian VI - Email
---
Setelah dilihat kembali dari data peserta yang dimiliki, ternyata ada satu informasi yang penting namun belum tersedia, yaitu *email*.

Tugas sebagai Data Engineer diminta untuk menyediakan informasi *email* dari peserta dengan aturan bahwa format email sebagai berikut:

```
Format email:
xxyy@aa.bb.[ac/com].[cc]

Keterangan:
xx -> nama depan (first_name) dalam lowercase
yy -> nama belakang (last_name) dalam lowercase
aa -> nama institusi

Untuk nilai bb, dan cc mengikuti nilai dari aa. Aturannya:
- Jika institusi nya merupakan Universitas, maka
  bb -> gabungan dari huruf pertama pada setiap kata dari nama Universitas dalam lowercase
  Kemudian, diikuti dengan .ac yang menandakan akademi/institusi belajar dan diikuti dengan pattern cc
- Jika institusi bukan merupakan Universitas, maka
  bb -> gabungan dari huruf pertama pada setiap kata dari nama Universitas dalam lowercase
  Kemudian, diikuti dengan .com. Perlu diketahui bahwa pattern cc tidak berlaku pada kondisi ini

cc -> merupakan negara asal peserta, adapun aturannya:
- Jika banyaknya kata pada negara tersebut lebih dari 1 maka ambil singkatan dari negara tersebut dalam lowercase
- Namun, jika banyaknya kata hanya 1 maka ambil 3 huruf terdepan dari negara tersebut dalam lowercase

Contoh:
  Nama depan: Citra
  Nama belakang: Nurdiyanti
  Institusi: UD Prakasa Mandasari
  Negara: Georgia
  Maka,Email nya: citranurdiyanti@upm.geo
  -----------------------------------
  Nama depan: Aris
  Nama belakang: Setiawan
  Institusi: Universitas Diponegoro
  Negara: Korea Utara
  Maka, Email nya: arissetiawan@ud.ac.ku
```
```
def func(col):
    first_name_lower = col['first_name'].lower()
    last_name_lower = col['last_name'].lower()
    institute = ''.join(list(map(lambda word: word[0], col['institute'].lower().split()))) #Singkatan dari nama perusahaan dalam lowercase

    if 'Universitas' in col['institute']:
        if len(col['country'].split()) > 1: #Kondisi untuk mengecek apakah jumlah kata dari country lebih dari 1
            country = ''.join(list(map(lambda word: word[0], col['country'].lower().split())))
        else:
            country = col['country'][:3].lower()
        return "%s%s@%s.ac.%s"%(first_name_lower,last_name_lower,institute,country)

    return "%s%s@%s.com"%(first_name_lower,last_name_lower,institute)

df_participant['email'] = df_participant.apply(func, axis=1)
```

## Transform Bagian VII - Tanggal Lahir
---

Melakukan *formatting* terhadap kolom **birth_date** menjadi **YYYY-MM-DD** dan simpan di kolom yang sama.


MySQL merupakan salah satu database yang sangat populer dan digunakan untuk menyimpan data berupa tabel, termasuk data hasil pengolahan yang sudah kita lakukan ini nantinya bisa dimasukkan ke MySQL.

Meskipun begitu, ada suatu aturan dari MySQL terkait format tanggal yang bisa mereka terima yaitu YYYY-MM-DD dengan keterangan:

* YYYY: 4 digit yang menandakan tahun
* MM: 2 digit yang menandakan bulan
* DD: 2 digit yang menandakan tanggal
* Contohnya yaitu: 2021-04-07

Jika kita lihat kembali pada kolom tanggal lahir terlihat bahwa nilainya belum sesuai dengan format DATE dari MySQL


**sc:** https://www.mysqltutorial.org/mysql-date/

Task:Melakukan *formatting* terhadap kolom **birth_date** menjadi **YYYY-MM-DD** dan simpan di kolom yang sama.
```
df_participant['birth_date'] = pd.to_datetime(df_participant['birth_date'], format='%d %b %Y')
df_participant.head()
```

## Transform Bagian VII - Tanggal Daftar Kompetisi
---
Selain punya aturan mengenai format DATE, MySQL juga memberi aturan pada data yang bertipe DATETIME yaitu YYYY-MM-DD HH:mm:ss dengan keterangan:

* YYYY: 4 digit yang menandakan tahun
* MM: 2 digit yang menandakan bulan
* DD: 2 digit yang menandakan tanggal
* HH: 2 digit yang menandakan jam
* mm: 2 digit yang menandakan menit
* ss: 2 digit yang menandakan detik

Contohnya yaitu: 2021-04-07 15:10:55

Karena data mengenai waktu registrasi peserta (register_time) belum sesuai format yang seharusnya.
Tugas kali ini yaitu mengubah register_time ke format DATETIME sesuai dengan aturan dari MySQL lalu menyimpan hasil tersebut ke kolom register_at.

```
df_participant['register_at'] = pd.to_datetime(df_participant['register_time'], unit='s')
df_participant.head()
```

## Kesimpulan
Jika dilihat kembali, dataset yang sudah dilakukan proses tranformasi data saat ini sudah berbeda dengan saat proses extract sebelumnya. Ada beberapa kolom tambahan yang memanfaatkan nilai kolom lain.


Dataset sebelum di transform (saat proses extract)
```
df_participant.info()

output:

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
Dataset yang sudah di transform:
```
df_participant.info()
```
output:
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5000 entries, 0 to 4999
Data columns (total 17 columns):
 #   Column                Non-Null Count  Dtype         
---  ------                --------------  -----         
 0   participant_id        5000 non-null   object        
 1   first_name            5000 non-null   object        
 2   last_name             5000 non-null   object        
 3   birth_date            5000 non-null   datetime64[ns]
 4   address               5000 non-null   object        
 5   phone_number          5000 non-null   object        
 6   country               5000 non-null   object        
 7   institute             5000 non-null   object        
 8   occupation            5000 non-null   object        
 9   register_time         5000 non-null   float64       
 10  postal_code           5000 non-null   object        
 11  city                  5000 non-null   object        
 12  github_profile        5000 non-null   object        
 13  cleaned_phone_number  5000 non-null   object        
 14  team_name             5000 non-null   object        
 15  email                 5000 non-null   object        
 16  register_at           5000 non-null   datetime64[ns]
dtypes: datetime64[ns](2), float64(1), object(14)
memory usage: 664.2+ KB
```

## Load
---
Pada bagian load ini, data yang sudah ditransformasi sedemikian rupa sehingga sesuai dengan kebutuhan tim analyst dimasukkan kembali ke database yaitu Data Warehouse (DWH). Biasanya, dilakukan pendefinisian skema database terlebih dahulu seperti:

* Nama kolom
* Tipe kolom
* Apakah primary key, unique key, index atau bukan
* Panjang kolomnya
Karena umumnya Data Warehouse merupakan database yang terstruktur sehingga mereka memerlukan skema sebelum datanya dimasukkan.

Pandas sudah menyediakan fungsi untuk memasukkan data ke database yaitu to_sql().



