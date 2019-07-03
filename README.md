# apiSample
2018年度に行っていたLINEのサマーインターンのデータベース処理のAPIのサンプルです
## 環境

- Java 10.0.2
- Kotlin 1.2.41
- Spring Boot 2.0.4
- Gradle 4.8.1
- IntelliJ IDEA 2018.2


## 簡易デプロイ＋起動方法(組込Tomcatを使用)
- IntelliJ IDEAのコンソールからGradleビルド
```
>gradlew build
```

- build/libs/ 下の apiSample-0.0.1-SNAPSHOT.jar を /home/ec2-user/server 下に転送

- jarを起動
```
$ cd ~/server
$ sudo java -jar ./apiSample-0.0.1-SNAPSHOT.jar
```

- Started ApiSampleApplicationKt メッセージを確認して curl -X GET http://{AWSサーバドメイン}/user で動作確認

## 動作確認
```
$ curl -X GET http://{AWSサーバドメイン}/user
{"greeting": "Hello World!"}

$ curl -X GET http://{AWSサーバドメイン}/user/1/profile
{"id":1,"name":"Atsushi Kimura","email":"akimura@potkitchen.com","created_at":"2018-08-08T00:00:00.000+0000","updated_at":"2018-08-08T00:00:00.000+0000"}
```

## APIリファレンス

### GET `/user`
> 動作確認用

#### Request

#### Response

| Parameter | Type | Description |
| -------- | -------- | -------- |
| greeting | String | Hello World! |

### GET `/user/{id}/profile`
> ユーザ情報取得

#### Request

| Parameter | Type | Description |
| -------- | -------- | -------- |
| id | String | |

#### Response

| Parameter | Type | Description |
| -------- | -------- | -------- |
| id | String | |
| name | String | |
| email | String | |
| created_at | Date | |
| updated_at | Date | |

### POST `/user/search`
> ユーザ名検索

#### Request

| Parameter | Type | Description |
| -------- | -------- | -------- |
| search_str | String | |

#### Response

| Parameter | Type | Description |
| -------- | -------- | -------- |
| results[].id | Long | |
| results[].name | String | |
| results[].email | String | |


## サーバセットアップ
```
$ sudo yum update -y
$ wget https://potkitchen.sakura.ne.jp/jre-10.0.2_linux-x64_bin.tar.gz
$ tar zxf jre-10.0.2_linux-x64_bin.tar.gz
$ sudo cp -pr jre-10.0.2 /usr/lib/jvm
$ sudo /usr/sbin/alternatives --install /usr/bin/java java /usr/lib/jvm/jre-10.0.2/bin/java 20000
$ rm -fr jre-10.0.2_linux-x64_bin.tar.gz jre-10.0.2
$ sudo alternatives --config java

There are 2 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
   1           /usr/lib/jvm/jre-1.7.0-openjdk.x86_64/bin/java
*+ 2           /usr/lib/jvm/jre-10.0.2/bin/java

Enter to keep the current selection[+], or type selection number: 2

$ java -version
java version "10.0.2" 2018-07-17
Java(TM) SE Runtime Environment 18.3 (build 10.0.2+13)
Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.2+13, mixed mode)
$ sudo yum install -y mysql mysql-server
$ sudo passwd mysql
$ New password: 
$ Retype new password: 
$ su - mysql
$ mysql_install_db
$ exit
$ mkdir server
$ cd server
$ wget https://potkitchen.sakura.ne.jp/apiSample.zip
$ unzip apiSample.zip
$ cd ..
$ sudo /etc/init.d/mysqld start
$ sudo /usr/libexec/mysql55/mysqladmin -u root password ''
$ mysql -h localhost -u root -p
Enter password: 

mysql> CREATE DATABASE IF NOT EXISTS apisample DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;

mysql> USE apisample;

mysql> CREATE TABLE users (
    ->   id bigint(13) NOT NULL,
    ->   name varchar(255) COLLATE utf8mb4_bin NOT NULL,
    ->   email varchar(255) COLLATE utf8mb4_bin NOT NULL,
    ->   created_at timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    ->   updated_at timestamp NOT NULL DEFAULT '0000-00-00 00:00:00'
    -> ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin;

mysql> INSERT INTO users (id, name, email, created_at, updated_at) VALUES
    -> (1, 'Atsushi Kimura', 'akimura@potkitchen.com', '2018-08-08 00:00:00', '2018-08-08 00:00:00'),
    -> (2, 'Hiroyuki Moriyama', 'hiroyuki.moriyama@codecamp.jp', '2018-08-08 01:00:00', '2018-08-08 01:00:00'),
    -> (3, 'Yudai Shimoyama', 'yudai.shimoyama@codecamp.jp', '2018-08-08 02:00:00', '2018-08-08 02:00:00');

mysql> ALTER TABLE users ADD PRIMARY KEY (id);

mysql> ALTER TABLE users MODIFY id bigint(13) NOT NULL AUTO_INCREMENT, AUTO_INCREMENT=4;

mysql> COMMIT;

mysql> CREATE USER dbuser;

mysql> GRANT ALL PRIVILEGES ON apisample.* TO dbuser@'localhost' IDENTIFIED BY 'lineschool';
mysql> exit
```
