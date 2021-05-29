# 설치하기

## AWS Ubuntu 터미널(SSH) 환경 설치 설치 방법
Amazon Lightsail 대시보드에서 `[Create instance]`버튼을 클릭 후 인스턴스를 생성합니다.  

## 설치 정보 입력
### 1. 원하는 국가 선택 (Select your instance location)
- `Seoul/ap-northeast-2`를 선택합니다.

### 2. 플랫폼 선택 (Select a platform)
- `Linux/Unix`를 선택합니다.

### 3. 운영체제 선택 (Select a blueprint > OS Only)
- `OS Only > Ubuntu 18.04 LTS`를 선택합니다.

### 4. 가격정책 선택 (Choose your instance plan)
- 인스턴스의 원하는 가격정책을 선택합니다.

### 5. 인스턴스 이름 (Identify your instance)
- 해당 호스팅 인스턴스 이름을 입력합니다.

## 네트워크 설정

### 1. 고정 아이피 연결 [선택사항]
- PUBLIC IP 섹션에 `Attach static IP`를 클릭하여 static IP 주소를 생성하고 연결합니다.

### 2. HTTPS 443 규칙 생성
- 생성한 인스턴스를 클릭하여 인스턴스 상세페이지의 `Networking`메뉴를 클릭합니다.
- `Add rule`버튼을 클릭 후 `Web server/HTTPS`을 `[Create]`버튼을 클릭하여 생성합니다.

##  SSH 연결

### 1. 웹 터미널 접속
- 인스턴스 상세페이지에서 `Connect` 섹션에 `[Connect using SSH]`를 클릭하여 터미널 접속을 바로할 수 있습니다.

##  XpressEngine3 환경 설치

### 1. Apache2 설치하기
```text
$ sudo apt-get update
$ sudo apt-get install apache2 -y
$ sudo a2enmod rewrite
$ sudo systemctl restart apache2
```
apache2가 제대로 설치되었는지 확인하고 싶다면 인스턴스 상세페이지에 Static IP 주소를 인터넷 브라우저 주소창에 입력하면 됩니다.

### 2. PHP 7.2 설치하기
```text
$ sudo apt-get install software-properties-common -y
$ sudo add-apt-repository ppa:ondrej/php -y
$ sudo apt-get update -y
$ sudo sudo apt-get install php7.2 php7.2-gd php7.2-cgi php7.2-cli php7.2-common php7.2-fpm php7.2-mbstring php7.2-dom php7.2-zip php7.2-pdo php7.2-tokenizer php7.2-xml php7.2-ctype php7.2-json php7.2-mysql php7.2-curl php7.2-mysql -y
$ sudo apt install libapache2-mod-php7.2
$ sudo a2enmod php7.2
$ sudo systemctl restart apache2
```

### 3. MySQL 8.0 설치하기
```text
$ sudo wget https://dev.mysql.com/get/mysql-apt-config_0.8.13-1_all.deb
$ sudo dpkg -i mysql-apt-config_0.8.13-1_all.deb
$ sudo apt-get update
$ sudo apt-get install mysql-server -y
```

### 4. Composer 1.8.6 설치하기
```text
$ sudo apt-get install curl -y
$ sudo curl -s https://getcomposer.org/installer | php
$ sudo mv composer.phar /usr/local/bin/composer
$ sudo composer self-update 1.8.6
```

### 5. XpressEngine3 의존성 설치하기
```text
$ cd ~
$ git clone https://github.com/xpressengine/xpressengine.git
$ cd ~/xpressengine
$ composer install
```

#### 5-1. Composer - proc_open() : fork failed - Cannot allocate memory 해결방법
```text
$ sudo fallocate -l 4G /swapfile
$ sudo chmod 600 /swapfile
$ sudo mkswap /swapfile
```
```text
$ sudo vim /etc/fstab
```
재부팅 이후에도 swap이 유지될 수 있게 VIM으로 열린 `/etc/fstab`에 다음 내용 추가.
```text
/swapfile swap swap defaults 0 0
```

### 6. XpressEngine3 데이터베이스 생성
```text
$ mysql -uroot -p
$ create database xe;
# exit;
```

### 7. XpressEngine3 Apache2 설정
```text
$ mysql -uroot -p
$ create database xe;
# exit;
```

### 8. XpressEngine3 Apache2 설정
```text
$ sudo vim /etc/apache2/sites-available/000-default.conf
```

```text
<VirtualHost *:80>
ServerAdmin webmaster@localhost
DocumentRoot /home/ubuntu/xpressengine

<Directory /home/ubuntu/xpressengine>
    AllowOverride All
    Require all granted
    DirectoryIndex index.php index.html
</Directory>

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

```text
$ sudo service apache2 restart
```

```text
$ chmod 0707 ./storage ./bootstrap ./bootstrap/cache ./config ./config/production ./vendor ./plugins index.php composer.phar
```

### 9. XpressEngine3 설치
```text
$ php artisan xe:install
> Driver [mysql]: mysql
> Host [localhost]: localhost
> Port [3306]: 3306
> Database name: xe
> UserID [root]: root
...
```

### 9. XpressEngine3 완료
static IP 주소로 이동하면 XE3가 설치된 것을 확인할 수 있습니다.