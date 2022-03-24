 <img src="https://www.vectorlogo.zone/logos/vagrantup/vagrantup-icon.svg" alt="vagrant" width="40" height="40"/> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/nginx/nginx-original.svg" alt="nginx" width="40" height="40"/> <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/linux/linux-original.svg" alt="nginx" width="40" height="40"/>  
 
 
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


