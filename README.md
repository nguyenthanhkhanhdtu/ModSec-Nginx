 #ModSec-Nginx
 
![image](https://github.com/user-attachments/assets/ab0864e6-fe8c-4fc3-8028-2fb11b264507)

# Cài đặt ModSec trên Ubuntu 20
Đầu tiên : apt update && apt upgrade sau đó apt install nginx

# Cài đặt cái gói phụ trợ :
sudo apt-get install bison build-essential ca-certificates curl dh-autoreconf doxygen flex gawk git iputils-ping libcurl4-gnutls-dev libexpat1-dev libgeoip-dev liblmdb-dev libpcre3-dev libpcre++-dev libssl-dev libtool libxml2 libxml2-dev libyajl-dev locales liblua5.3-dev pkg-config wget zlib1g-dev zlibc libxslt-dev libgd-dev
![image](https://github.com/user-attachments/assets/91dc197e-ec83-4d22-bd90-cfe8cdc5e62f)

# Tải xuống ModSec, đặt trong thư mục opt : 
cd /opt && git clone https://github.com/owasp-modsecurity/ModSecurity
cd ModSecurity

# Kiểm tra các phụ trợ
sudo git submodule init
sudo git submodule update
![image](https://github.com/user-attachments/assets/a31e992d-bad8-4fd7-8ff7-8bbc78e1c3f3)

# Start process
./build.sh

![image](https://github.com/user-attachments/assets/e039222f-c48c-412c-83c9-1325c13fa700)

# Config

./configure

![image](https://github.com/user-attachments/assets/5a7aebb9-9fe1-4c71-b03c-67d4b0f7bd11)

![image](https://github.com/user-attachments/assets/fc9d6d54-150a-4ca2-9686-8d986f26cdf7)

# Tiến hành Make

make

![image](https://github.com/user-attachments/assets/0d63213e-292d-42c7-954c-91358321e4da)

![image](https://github.com/user-attachments/assets/d8397390-e7fb-402d-8dc4-fa47a47a1335)

make install

![image](https://github.com/user-attachments/assets/c05d16b4-43bc-4ef0-a62b-df5be0caa998)

![image](https://github.com/user-attachments/assets/2af9ac62-b6f4-472c-91f9-3f858e5df605)

# Cài đặt ModSecurity-NGINX-Connector
cd /opt && sudo git clone — depth 1 https://github.com/owasp-modsecurity/ModSecurity.git

# Check version nginx và cài nginx cho phù hợp
wget http://nginx.org/download/nginx-1.18.0.tar.gz
tar -xzvf nginx-1.18.0.tar.gz

# Kiểm tra phản hồi nginx và copy nội dung phản hồi đối số
nginx -V

![image](https://github.com/user-attachments/assets/139071f3-7d20-4d02-bdd8-bb409d44690f)

# compile the Modsecurity module

cd nginx-1.18.0

sudo ./configure --add-dynamic-module=../ModSecurity-nginx <nội dung phản hồi đối số>

ví dụ : ![image](https://github.com/user-attachments/assets/b72f671f-ed09-4209-bd79-6499e1f5247e)

Thành công : ![image](https://github.com/user-attachments/assets/9255676d-e510-4c3d-8516-052c9b097856)

tiến hành: sudo make modules ![image](https://github.com/user-attachments/assets/440346bd-d5dc-4763-9045-2b4459b18503)

# Kiểm tra /objs folder

![image](https://github.com/user-attachments/assets/bf04102c-f8e3-4c1a-bed1-dcba12d9894b)

Chú ý module: ngx_http_modsecurity_module.so
ls objs

# Tạo thư mục modules
mkdir /etc/nginx/modules
cp objs/ngx_http_modsecurity_module.so /etc/nginx/modules
nano /etc/nginx/nginx.conf
thêm lệnh: load_module /etc/nginx/modules/ngx_http_modsecurity_module.so;
lưu lại

# Tải xuống core rule và đặt tên là modsecurity-crs
git clone https://github.com/coreruleset/coreruleset modsecurity-crs
cd modsecurity-crs
Rename lại file config : mv crs-setup.conf.example crs-setup.conf
Rename REQUEST EXCLUSION RULES: mv rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf

# Di chuyển modsecurity-crs tới local
mv modsecurity-crs /usr/local

# Tạo thư mục modsec
mkdir -p /etc/nginx/modsec

# sao chép một vài tệp cấu hình vào thư mục này từ kho lưu trữ github modsecurity mặc định vào /etc/nginx/modsec

cp  /opt/Modsecurity/unicode.mapping /etc/nginx/modsec

![image](https://github.com/user-attachments/assets/1d647492-e6c9-48a1-99e2-1c85f41beb0a)

#  Turn on the SecRuleEngine, trong thư mục /etc/nginx/modsec/modsecurity.conf

![image](https://github.com/user-attachments/assets/01ae7feb-232e-466d-a53a-72116cc33fad)

# make main.conf

cd /etc/nginx/modsec 

![image](https://github.com/user-attachments/assets/ef47a1a1-7692-45e5-8435-9791c9092998)

nano main.conf

![image](https://github.com/user-attachments/assets/d83495de-bb5e-40e5-a17c-624808d337ab)

Include /etc/nginx/modsec/modsecurity.conf
Include /usr/local/modsecurity-crs/crs-setup.conf
Include /usr/local/modsecurity-crs/rules/*.conf

# Tất cả file trong thư mục etc/nginx

![image](https://github.com/user-attachments/assets/7d0681c4-2342-4c37-979b-a3fbd16e4c50)

# Thêm nội dung vào file /etc/nginx/sites-available/default đặt dưới  root /var/www/html;
modsecurity on;
modsecurity_rules_file /etc/nginx/modsec/main.conf;

# restart the nginx và done!




