# FinalProject1Kelompok7
### Anggota
1. **Ahmad Murteza Akbari**
2. **Diory Pribadi Sinaga**
3. **Siti Sofiani**

### How To Run
* npm install terlebih dahulu. Kemudian,
* Buat file `.env`, dan isi sesuai dengan yang ada di file `.env.example`.

    ``` javascript
    ACCESS_TOKEN_SECRET=isi secret buat token
    DB_USER=
    DB_HOST=
    DB_DATABASE=
    DB_PASSWORD=
    PORT=
    ```
* Sebelum menggunakan, terlebih dahulu `npm run db:create` untuk membuat database, setelah itu `npm run db:migrate` untuk membuat table users dan reflections.
* Jika ingin menghapus table `npm run db:migrate:undo`, dan menghapus database `npm run db:drop`
* Terakhir `npm run start`. **Server berjalan pada port 8000**

### Directory Structure
``` bash
├───app/
│   ├───controller/
│   │   ├───api/
│   │   │   ├───v1/
│   │   │   │   ├───index.js
│   │   │   │   ├───reflectionController.js
│   │   │   │   └───userController.js
│   │   │   ├───index.js
│   │   │   └───main.js
│   │   └───index.js
│   ├───helpers/
│   │   ├───cekPassword.js
│   │   ├───hash.js
│   │   └───jwt.js
│   ├───middleware/
│   │   ├───authentication.js
│   │   └───index.js
│   ├───models/
│   │   ├───index.js
│   │   ├───reflection.js
│   │   └───user.js
│   └───index.js
├───bin/
│   └───index.js
├───config/
│   ├───config.js
│   └───pool.js
├───db/
│   ├───migrations/
│   │   ├───createTable.js
│   │   └───dropTable.js
│   ├───createDatabase.js
│   └───dropDatabase.js
├───router/
│   ├───partials/
│   │   ├───index.js
│   │   ├───reflectionRoute.js
│   │   └───userRoute.js
│   └───index.js
├───.env
├───.env.example
├───package-lock.json
├───package.json
└───README.md
```
### Dependecies
``` json
"dependencies": {
    "bcryptjs": "^2.4.3",
    "body-parser": "^1.20.0",
    "dotenv": "^16.0.2",
    "express": "^4.18.1",
    "jsonwebtoken": "^8.5.1",
    "pg": "^8.8.0"
  }
```
### Table
``` javascript
CREATE TABLE users (
    id serial primary key,
    email VARCHAR NOT NULL,
    username VARCHAR NOT NULL,
    password VARCHAR NOT NULL
);
CREATE TABLE reflections (
    id serial primary key,
    success VARCHAR NOT NULL,
    low_point  VARCHAR NOT NULL,
    take_away VARCHAR NOT NULL,
    owner_id INT NOT NULL,
    created_date timestamp,
    modified_date timestamp,
    CONSTRAINT fk_users
        FOREIGN KEY(owner_id)
        REFERENCES users(id)
);
```

### Endpoint
``` javascript
POST  /api/v1/users/register  //Register User
POST /api/v1/users/login     //Login User

GET /api/v1/reflections        //Get reflections user
POST /api/v1/reflections       //Post reflections user
PUT /api/v1/reflections/:id    //Update reflections user
DELETE /api/v1/reflections/:id  //Delete reflections user
```

### Penjelasan
* Register user terdiri dari `email,username` dan `password`
    ``` javascript
    POST {{host}}/users/register
    Content-Type: application/json

    {
    "email": "diory4@gmail.com",
    "username": "diory2",
    "password": "Diory123?"
    }
    ```
    Email dan username harus unique dan Password minimum delapan karakter, setidaknya satu huruf besar, satu huruf kecil, satu angka dan satu karakter khusus

* Login user terdiri dari `email` dan `password`
    ``` javascript
    POST {{host}}/users/login
    Content-Type: application/json
    
    {
      "email": "diory2@gmail.com",
      "password": "12345"
    }
    ```

* Get reflections, menampilkan reflections user yang terkait
* Post reflection terdiri atas `succes`, `low_point`, dan `take_away`
    ``` javascript
    POST {{host}}/reflections
    Authorization: Bearer {{accessToken}}
    Content-Type: application/json

    {
        "success":"today success",
        "low_point":"new low point user 3",
        "take_away":"new Take Away user 3"
    }
    ```
* Update reflection user dapat mengupdate `succes`, `low_point`, dan `take_away` sekaligus atau boleh salah satu atau dua saja. Dan tidak diperbolehkan mengupdate reflection punya orang lain, jika ingin mengupdate punya orang lain akan muncul pesan akan muncul seperti dibawah ini
  <p style="font-style:italic;color:#ffcc00 ">Anda Tidak Boleh Mengedit yang bukan punya Anda!</p>

  ``` javascript
  PUT {{host}}/reflections/2
  Authorization: Bearer {{accessToken}}
  Content-Type: application/json
  
  {
  "success":"today Not success",
  "low_point":"new low point new user 3",
  "take_away":"new Take Away new user 3"
  }
  ```

* Delete reflection user tidak dapat dilakukan jika bukan punya user yang berkaitan, jika mencoba untuk menghapus maka akan muncul pesan seperti dibawah ini
  <p style="font-style:italic;color:#ffcc00 ">Anda Tidak Boleh Menghapus yang bukan punya Anda!</p>
  
  ``` javascript
  DELETE {{host}}/reflections/6
  Authorization: Bearer {{accessToken}}
  ```