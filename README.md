 <img src="https://www.vectorlogo.zone/logos/vagrantup/vagrantup-icon.svg" alt="vagrant" width="40" height="40"/> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/nginx/nginx-original.svg" alt="nginx" width="40" height="40"/> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/linux/linux-original.svg" alt="nginx" width="40" height="40"/>  
 
 <img  src="https://seishin.me/content/images/size/w960/2018/10/1-TrNJZqECEj0eVuJDeNKtNQ.png" alt="vagrant" width="800" height="400"/> 
 
vagrant-node-js-nginx-load-balancer
============================

> Bu alıştırma da nginx ile load balancer ve reverse proxy aşamalarını canlandıracağım bunu yaparken  vagrant üzerinden çoklu sanal makinaları nasıl yöneteceğimizi göreceğiz

### A typical top-level directory layout

    .
    ├── Service 1                   
    ├── Service 2                 
    ├── Service 3                 
   


### Gerekli ön şartlar

Birden fazla sanal makinayı yönetebilmemiz için vagrantı ve virtualboxı kurmamız gerekecektir.(https://www.vagrantup.com/)(https://www.virtualbox.org/)


> **Q: Virtualbox Örneği?**
> 
> <img src="https://github.com/nuri35/vagrant-node-js-nginx-load-balancer/blob/master/images/images1.PNG" width="450" height="450"/>
>

    .
    ├── Service 1
    ├── Service 2                  
    │   ├── Vagrantfile         
    │          
    │                   
    └── Service 3
    ````
    
 ### Vagrant konfigrasyon    
```
 NAME = 'web1'  
IP = "192.168.1.12"

Vagrant.configure("2") do |config|  
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = NAME
  config.vm.network "public_network", ip: IP

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", '2048']
  end
end
```
````
   
### 3 servis oluşturduktan sonra vagrant sayesinde farklı sanal makinaları yönetmeye başlayalım.

### Source files
```
vagrant init -h  => init komutu 
Vagrant up  => Makinayı ayağa kaldırır.
vagrant destroy => sonlandırır.
vagrant autocomplete install --bash --zsh. => Vagrant, komutları otomatik tamamlama yeteneği sağlar
Vagrant ssh => Bu komut sayesinde kendi bilgisayarımız üzerinden istenilen makinanın shell'ine bağlanabiliyoruz.
```
````
   

### Şimdi sırada sanal makinalarımıza yaptığımız Node js,git ve social-media uygulamasını kuracağız.

Node js (https://nodejs.org/en/download/package-manager/) kurulumunu gerçekleştirmek için ilgili linke tıklıyoruz. Gerekli linux işletimi için shell üzerinden yüklemeyi yapabilmek için bu adımları uyguluyoruz.En son Node js'in versiyonuna bakıyoruz.

```
curl -fsSL https://deb.nodesource.com/setup_17.x | sudo -E bash -
sudo apt-get install -y nodejs

# Using Debian, as root
curl -fsSL https://deb.nodesource.com/setup_17.x | bash -
apt-get install -y nodejs
node -v 

OUTPUT => v16.14.2 
```
````


### Şimdi sırada Mongodb veritabanımızı shell üzerinden kuracağız ve 

```
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
mkdir /etc/apt/sources.list.d/mongodb-org-5.0.list
cd ./etc/apt/sources.list.d/mongodb-org-5.0.list
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
sudo apt-get update
sudo systemctl start mongod

```
````

 <img  src="https://github.com/nuri35/vagrant-node-js-nginx-load-balancer/blob/master/images/1.png" alt="vagrant" width="700" height="400"/> 
 
 -----------------------------------------------------
 
 ### veritabanımızın çalıştığını kontrol etmek için 
 ```
vagrant@web1:~/social-media-app-docker-with-testing$ mongo

```
````
  ### ÇIKTI 
  
   ```
MongoDB shell version: 3.0.15
connecting to: test
Server has startup warnings:
2022-03-28T12:24:01.242+0000 I CONTROL  [initandlisten]
2022-03-28T12:24:01.243+0000 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/enabled is 'always'.
2022-03-28T12:24:01.254+0000 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
2022-03-28T12:24:01.255+0000 I CONTROL  [initandlisten]
2022-03-28T12:24:01.258+0000 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/defrag is 'always'.
2022-03-28T12:24:01.265+0000 I CONTROL  [initandlisten] **        We suggest setting it to 'never'
2022-03-28T12:24:01.265+0000 I CONTROL  [initandlisten]

```
````
  
   ### Artık 2 aynı uygulamamız da farklı makinalarda ayakta  bunun için npm start diyerek başlatıyoruz. 
   
   
 <img  src="https://github.com/nuri35/vagrant-node-js-nginx-load-balancer/blob/master/images/ekran%202.PNG" alt="vagrant" width="600" height="400"/> 
   
   ### Sonuç olarak nginx ile loadbalancing işlemimizi yapabiliriz.Bunun için nginx konfigrasyonlarımızı yapmak durumundayız.
 
 <p>İlk olarak nginx yüklü makinamızda aşağıdaki komutu çalıştırarak ilgili klasöre giriyoruz</p>
   
```
sudo vi /etc/nginx/sites-available/default

```
````

 İlgili yerde i tuşuna basarak insert modunu aktif ediyoruz.Aşağıdaki kodu yazıyoruz.
 
 ###  HTTP Load Balancing

   ```
       http {
    upstream my_upstream {
        server server1.example.com;
        server server2.example.com;
    }

    server {
        listen 80;
        location / {
            proxy_set_header Host $host;
            proxy_pass http://my_upstream;
        }
    }
}
```
````

Böylelikle nginx ip adresinden isteğimizi gönderdiğimizde (192.168.1.12:5000) gibi belirttiğimiz adreslere isteği yollayacak ve yeniden client tarafına nginx üzerinden sonucu ulaştıracaktır.

 ###  nginx yüklü olmayan makine üzerinde ki local ip adresi üzerinden ulaşılan uygulama 
 
 <img  src="https://github.com/nuri35/vagrant-node-js-nginx-load-balancer/blob/master/images/ip%202%20resim.PNG" alt="" width="600" height="300"/> 

 ###  nginx yüklü olan makine üzerinde ki  ip adresi üzerinden ulaşılan uygulama 

 <img  src="https://github.com/nuri35/vagrant-node-js-nginx-load-balancer/blob/master/images/ip%20resim.PNG" alt="" width="600" height="300"/> 
 
 ------------------------------
 
  ##### Şimdi apache benchmark üzerinden testimizi yaparak nginx sayesinde aldığımız sonucu görelim
  
 
 
 
 
