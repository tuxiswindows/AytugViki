# Wordpress Kurulum
Root kullanıcısına geçelim (Duruma Bağlı)
```
sudo su
```
## Apache ve Mysql
İlk önce Apache web serverımızı ve MariaDB veri tabanımızı kuralım.
```
apt-get install apache2 mariadb-server mariadb-client curl
```
Şimdi Php bileşenlerini yükleyelim.
```
apt install php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip vim
```
Mysql kurulumunu yapalım. *Mysql_Root_Sifreniz* kısmına koymak istediğiniz root şifresini giriniz.
```
mysql_secure_installation
Enter
Y
Y
Mysql_Root_Sifreniz
Mysql_Root_Sifreniz
Y
Y
Y
```
Mysql veritabanımızı ayarlayalım. *Mysql_Root_Sifreniz* kısmına bir önceki aşamada belirlediğimiz root şifresini yazın. *Mysql_Kullanici_Sifresi*'ne Wordpress'in verileri okuyacağı kullanıcının şifresini verin.
```
mysql -u root -p
Mysql_Root_Sifreniz
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
GRANT ALL ON wordpress.* TO 'wordpress_user'@'localhost' IDENTIFIED BY 'Mysql_Kullanici_Sifresi';
FLUSH PRIVILEGES;
\q
```
## Wordpress
### Wordpress Kurulum
Wordpress için gerekli dosyaları indirelim. 
```
curl https://wordpress.org/latest.tar.gz -o latest.tar.gz
mkdir /var/www/wordpress
tar xzvf latest.tar.gz --directory /var/www
```
Apache'ye Wordpress Configlerini Oluşturalım. Ben editör olarak *Vim* kullandım siz Nano veya NeoVim kullanabilirsiniz, Nano'nun kullanımı daha kolaydır.
```
vim /etc/apache2/sites-available/wordpress.conf
```
Yaptıktan sonra oraya şunu yapıştırın:
```
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/wordpress

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
### Wordpress Config
Config oluşturun.
```
cp /var/www/wordpress/wp-config-sample.php /var/www/wordpress/wp-config.php
```
Şu komutu çalıştırıp kopyalayın.
```
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```
Kopyaladığınız şeyleri configin en altına yapıştırın.
```
vim /var/www/wordpress/wp-config.php
```
Configteki ```define('DB_NAME' , 'database');``` kısmındaki database yazısını ```wordpress```'e çevirin.
Bunu aynı şekilde ```'DB_USERNAME'```, ```'DB_PASSWORD'``` içinde yapın.
Confiğin en altına ```define('FS_METHOD', 'direct');``` yazın.

### Wordpress'in İzinlerini düzenleme
Şu Komutları Çalıştırın
```
chown -R www-data:www-data /var/www/wordpress
find /var/www/wordpress/ -type d -exec chmod 750 {} \;
find /var/www/wordpress/ -type f -exec chmod 640 {} \;
```
## Bitti
Artık Wordpress kurulmuş oldu, Eğer doğru yaptıysanız Wordpress'in keyfini çıkarabilirsiniz.
