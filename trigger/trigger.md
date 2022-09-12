|Nama|BobChrismansyah
|---|---
|NIM|H071201087

### Trigger MySql

* Masuk ke MySQL dan membuat database triggers

```text
E:\xampp\mysql\bin>mysql -u root -r
Welcome to the MariaDB monitor.  Commands end with ; or \g.
```

* Buat database triggers_1:
  
```sql
CREATE DATABASE triggers_1;
///Query OK, 1 row affected (0.001 sec)
```

* Query OK berarti database berhasil dibuat, kemudian kita masuk ke database triggers_1:
  
```sql
use triggers_1
```

* Kemudian kita membuat tabel _person_ yang berisi _age_ dan _name_:
  
```sql
CREATE TABLE person (name varchar(45), age int);
```

* Isi sample data ke tabel _person_:
  
```sql
INSERT INTO person VALUES ('Bob Chrismansyah', 20), ('Ongky', 20), ('Pedi', 20);
///Query OK, 3 rows affected (0.003 sec)
///Records: 3  Duplicates: 0  Warnings: 0
```

* Tampilkan hasil dari tabel _person_:

```sql
SELECT * FROM person;
///3 rows in set (0.000 sec)
```

|name |age|
|------|---|
|Bob Chrismansyah|20|
|Ongky|20 |
|Pedi|20 |

* Kemudian kita membuat table _average_age_ dalam database triggers_1 untuk melihat rata-rata _age_ dalam tabel yang sudah diisikan. Setelah itu akan ditampilkan rata-rata _age_ (umur) dari _person_

```sql
 CREATE TABLE average_age (average double);
///Membuat tabel average_age untuk menampilkan rata-rata age dari person
 ```

```sql
INSERT INTO average_age SELECT AVG(age) FROM person;
///masukkan rata-rata age dari person ke tabel average_age
```

```sql
SELECT * FROM average_age;
///tampilkan rata-rata age dari person
```

|average|
|------|
|20|

* Membuat tabel bernama _person_archive_ dengan kolom yang berisi _name_, _age_ dan _time_.
  
```sql
CREATE TABLE person_archive (
    -> name varchar(45),
    -> age int,
    -> time timestamp DEFAULT NOW());
```

* Membuat trigger BEFORE INSERT untuk mengecek age sebelum mengisi data ke dalam tabel, ini sama saja dengan fungsi if dalam sintaks pemrograman.

```sql
DELIMITER $$
CREATE TRIGGER person_bi BEFORE INSERT ON person
FOR EACH ROW
    IF NEW.age < 18 THEN
        SIGNAL SQLSTATE '50001'
        SET MESSAGE_TEXT = 'Age must be greater than 18';
    END IF;
```

* Setelah membuat trigger BEFORE INSERT yang dimana NEW.age harus diatas 18, maka akan mengecek otomatis inputan age, jika kurang dari 18 akan terjadi ERROR 1644 (50001): Person must be older than 18.
Sebagai Contoh:

```sql
INSERT INTO person VALUES ('Ica', 17);
///ERROR 1644 (50001): Person must be older than 18.
///akan terjadi error karena age kurang dari 18
```

AFTER INSERT Trigger

```sql
DELIMITER //
CREATE TRIGGER person_ai AFTER INSERT
    ON person
    FOR EACH ROW
    UPDATE average_age SET average = (SELECT AVG(age) FROM person);
```
