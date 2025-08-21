<h1 align = "center">SQLite and PostGreSQL</h1>

## Build for host
```bash
sudo apt update
sudo apt install libpq-dev
cd $HOME/qt6/host-build
rm -rf *
$ cmake ../src/qtbase-everywhere-src-6.5.1/ -GNinja \
  -DCMAKE_BUILD_TYPE=Release \
  -DINPUT_opengl=es2 \
  -DQT_BUILD_EXAMPLES=OFF \
  -DQT_BUILD_TESTS=OFF \
  -DCMAKE_INSTALL_PREFIX=/home/quang/qt6/host \
  -DFEATURE_sql=ON \
  -DFEATURE_sql_psql=ON
  
cmake --build . --parallel 8
cmake --install .
```

## Build for Pi
```bash
cd $HOME/qt6/pi-build
rm -rf *
cmake ../src/qtbase-everywhere-src-6.5.1/ -GNinja -DCMAKE_BUILD_TYPE=Release -DINPUT_opengl=es2 -DQT_BUILD_EXAMPLES=OFF -DQT_BUILD_TESTS=OFF -DQT_HOST_PATH=$HOME/qt6/host -DCMAKE_STAGING_PREFIX=$HOME/qt6/pi -DCMAKE_INSTALL_PREFIX=/usr/local/qt6 -DCMAKE_TOOLCHAIN_FILE=$HOME/qt6/toolchain.cmake -DQT_QMAKE_TARGET_MKSPEC=devices/linux-rasp-pi4-aarch64 -DQT_FEATURE_xcb=ON -DFEATURE_xcb_xlib=ON -DQT_FEATURE_xlib=ON -DFEATURE_sql=ON -DFEATURE_sql_psql=ON
cmake --build . --parallel 8
cmake --install .
```

---
## Postgre manual
### Structure
```pgsql
PostgreSQL Server
├── User/Role
│   ├─ quang
│   └─ postgres
├── Database (nhiều database)
│   ├─ db1 (owner: quang)
│   │   └─ Schema: public
│   │       ├─ Table: customers
│   │       └─ Table: orders
│   └─ db2 (owner: postgres)
│       └─ Schema: public
│           ├─ Table: products
│           └─ Table: suppliers
```
### Install
```bash
sudo apt install postgresql postgresql-contrib
```
- `postgresql`: main app
- `postgresql-contrib`: extension

### Login 
- Switch user
```bash
sudo -i -u postgres
```
- Login 
```bash
psql
```
- Syntax PostgresSQL
  - `\l`: liet ke cac co so du lieu 
  - `\c testdb`: ket noi vao csdl testdb
  - `\q`: quit
  - `\lu`: liet ke cac user
  
- Syntax SQL
  - `CREATE USER quang WITH PASSWORD '12345';`: tao user quang va dat mk 12345
  - `ALTER USER username CREATEDB;`: Đặt quyền tạo database/role
  - `ALTER USER username CREATEROLE;`: Đặt quyền tạo database/role 
  - `DROP USER username;`: xoa user 
  ---
  - `CREATE SCHEMA myschema AUTHORIZATION username;`: Tạo schema
  - `DROP SCHEMA myschema CASCADE;`: CASCADE xóa tất cả object trong schema
  - `SET search_path TO myschema;`: Sử dụng schema 
  ---
  - Tạo table
    ```sql
    CREATE TABLE tablename (
      id SERIAL PRIMARY KEY,
      name VARCHAR(50) NOT NULL,
      age INT,
      created_at TIMESTAMP DEFAULT NOW()
    );	
    ```
  - `\dt`: Xem danh sách table
  - `\d tablename`: Xem cấu trúc table
  - `DROP TABLE tablename;`: Xoá table
  ---
  - DML – Data Manipulation (thao tác dữ liệu)
    ```sql
    -- Chèn dữ liệu
    INSERT INTO tablename (name, age) VALUES ('Quang', 25);

    -- Cập nhật dữ liệu
    UPDATE tablename SET age = 26 WHERE name = 'Quang';

    -- Xoá dữ liệu
    DELETE FROM tablename WHERE name = 'Quang';

    -- Truy vấn dữ liệu
    SELECT * FROM tablename;
    SELECT name, age FROM tablename WHERE age > 20;
    ```
  - Query nâng cao
    ```sql
    -- Sắp xếp
    SELECT * FROM tablename ORDER BY age DESC;

    -- Giới hạn số row
    SELECT * FROM tablename LIMIT 10;

    -- Count / aggregate
    SELECT COUNT(*) FROM tablename;
    SELECT AVG(age) FROM tablename;

    -- Join giữa 2 table
    SELECT a.name, b.product_name
    FROM customers a
    JOIN orders b ON a.id = b.customer_id;
    ```
  - Transaction
    ```sql
    BEGIN;                     -- bắt đầu transaction
    UPDATE tablename SET age = 30 WHERE id = 1;
    COMMIT;                    -- lưu thay đổi
    ROLLBACK;                  -- hủy thay đổi
    ```
