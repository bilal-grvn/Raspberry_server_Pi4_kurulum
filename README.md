<h1 align="center">
👋 Herkese Selamlar 👋
</h1>


## `Raspberry Pi4` için yaptığım `Ubuntu Server 20.04` kurulumu ve sonrası işlemleri anlatıyorum. Kısa kodlar ile detaya girmeden bilgi vermeye çalıştım. Umarım sizlere de faydası olur


# 🚀 UBUNTU SERVER 20.04 Kurulumu

ilk olarak [Raspberry Pi imager](https://www.raspberrypi.com/software/) programı yardımı ile SD kartımıza image dosyamızı yüklüyoruz.

`Raspberry Pi imager` programını açtığımızda sırası ile

`other general-purpose OS`

`ubuntu`

`Ubuntu Server 20.04.4 LTS (RPi 3/4/400) `

<p align="center">
  <img width="560" height="400" src="image/rasp_image.gif?raw=true">
</p>

seçeneklerini takip edelim ve image yüklenecek kartı seçerek yükleme işlemine başlayalım.
Sonrasında image yükleme işlemi tamamlandığında SD kartımızı Raspberry bilgisayarımıza takalım.

sistem ilk açıldığında default olarak `kullanıcı adı:ubuntu`, `şifre:ubuntu` gelir.

Ve sonrasında yeni şifre girmemizi isteyecek. Sistem terminal ekranı üzerinden işlemekte. 
Bu yüzden saat, dil, bölge, klavye gibi ayarları kendimiz terminal ekranından yapmamız gerekiyor.
İlk başta ethernet kablosu ile internete bağlanmamız gerekicek.

# -------------------------------------------------------------------------------------------
# 🚀 Terminal Ekranından Bölge ve Klavye Ayarları

Tarih ayarlarını yapabilmemiz için aşağıdaki komut ile yükleme yapmamız gerekiyor.
```sh
$ sudo apt install htpdate
```
yükleme tamamlandıktan sonra  
```sh
$ date
```
yazdığımızda aktif olan saat bilgisini verir.
```sh
$ timedatectl list-timezones
```
yazdığımızda saat bölgelerini gösterir. Burada `Europe/Istanbul` seçeneğini görmemiz gerek.
```sh
$ timedatecti set-timezone Europe/Istanbul
```
yazdığımızda saat ve bölge ayarlamasını yapmış oluruz.

Klavye ayarları için 
```sh
$ sudo dpkg-reconfigure keyboard-configuration
```
ile giriş yaptığımızda klavye ayarlarını yapabiliriz. Sonrasında bilgisayarı baştan başlatmamız gerekir.

# -------------------------------------------------------------------------------------------
# 🚀 Static IP atama 

```sh
$ ifconfig
```
ya da
```sh
$ hostname -I
```
komutu ile kablolu ve kablosuz ağdaki IP adresini görüntüleyebiliriz ama öncesinde 
```sh
$ sudo apt install net-tools
``` 
ile yükleme yapmamız gerekiyor

```sh
$ ls /sys/class/net
```
ile ağımızdaki networkleri görebiliriz. Genellikle `eth0`, `wlan0`, `lo` çıkacaktır.

Static IP ataması için
```sh
$ sudo nano /etc/netplan/50-cloud-init.yaml
```
içerisine girip bazı düzenlemeler yapmamız gerekiyor. 

Burada dikkat etmemiz gereken bir nokta; boşlukları `TAB` ile vermeyin. Hata veriyor
```sh
network:
 ethernets:
  eth0:
   addresses: [192.168.1.200/24]
   gateway4: 162.162.1.1
   dhcp4: true
   optional: true
   nameservers:
    addresses: [4.2.2.2, 8.8.8.8]
    search: []
 version: 2

 wifis:
  wlan0:
   optional: true
   access-points:
    "SSID_NAME":
     password: "password"
   dhcp4: true
```
düzenlemeleri yaptıktan sonra `Ctrl+X` ve `Y` (yes) `enter` diyerek çıkalım.

```sh
$ sudo netplan generate
```
```sh
$ sudo netplan apply
```
diyerek yapılandırma işlemini gerçekleştirelim. Bu sayede static IP atama ve wifi bağlantı işlemlerini gereçekleştirmiş oluruz.


EK olarak [nmcli](https://www.cyberithub.com/30-nmcli-command-examples-in-linux-rhel-centos-cheat-sheet/) kodlarını kullanarak da wifi ile ilgili bir çok işlem yapılabilir.

Ayrıca terminal ekranından wifi ağına bağlanabilmemiz için aşağıdaki kod ile yükleme yapalım
```sh
$ sudo apt install network-manager
```
yükleme işleminden sonra terminal ekranına
```sh
$ nmtui
```
yazarsak bizi wifi ağına bağlamak için yönlendirecektir.

# -------------------------------------------------------------------------------------------
# 🚀 USB den dosya çekme 

Terminal ekranına aşağıdaki satırı yazarsak USB leri listeler
```sh
$ fdisk -l
```
<p align="center">
  <img src="image/usb_list.PNG?raw=true">
</p>

listeden `sda` ya izin vermek için aşağıdaki satırı yazarız
```sh
$ sudo chmod 666 /dev/sda
```
<p align="center">
  <img src="image/usb_izin.PNG?raw=true">
</p>

USB ye gerekli izinleri verdikten sonra, USB yi `/media` klasörü içerisinde göstermek için aşağıdaki satır yazılır 
```sh
$ sudo mount /dev/sda1 /media
```

`/media` klasörü içerisinden USB ye ait dosyalar görünür. Ve buradan gerekli klasörü istenen yere aşağıdaki satır ile kopyalabiliriz

```sh
$ sudo cp -r /media/raspberry_ws /home/ubuntu
```

Aşağıdaki satır ile istenen klasörü silebiliriz
```sh
$ sudo rm -r /media/raspberry_ws 
```

USB ile işimiz bittikten sonra sistemden çıkarmak için aşağıdaki kod yazılır
```sh
$ sudo umount /media
```