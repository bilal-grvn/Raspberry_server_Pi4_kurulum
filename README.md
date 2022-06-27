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
  <img width="460" height="300" src="image/rasp_image.gif?raw=true">
</p>

seçeneklerini takip edelim ve image yüklenecek kartı seçerek yükleme işlemine başlayalım.
Sonrasında image yükleme işlemi tamamlandığında SD kartımızı Raspberry bilgisayarımıza takalım.

sistem ilk açıldığında default olarak `kullanıcı adı:ubuntu`, `şifre:ubuntu` gelir.

Ve sonrasında yeni şifre girmemizi isteyecek. Sistem terminal ekranı üzerinden işlemekte. 
Bu yüzden saat, dil, bölge, klavye gibi ayarları kendimiz terminal ekranından yapmamız gerekiyor.
İlk başta ethernet kablosu ile internete bağlanmamız gerekicek.

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
   addresses:
   - 192.168.1.200/24
   dhcp4: true
   optional: true
   gateway4: 162.162.1.1
   nameservers:
	addresses:
	- 192.168.1.1
	- 8.8.8.8
	- 8.8.4.4
	search: []
 version: 2
```
düzenlemeleri yaptıktan sonra `Ctrl+X` ve `Y` (yes) `enter` diyerek çıkalım.

```sh
$ sudo netplan generate
```
```sh
$ sudo netplan applay
```
diyerek yapılandırma işlemini gerçekleştirelim. Bu sayede static IP atama işlemini gereçekleştirmiş oluruz.

Terminal ekranından wifi ağına bağlanabilmemiz için aşağıdaki kod ile yükleme yapalım
```sh
$ sudo netplan applay
```
yükleme işleminden sonra terminal ekranına
```sh
$ nmtui
```
yazarsak bizi wifi ağına bağlamak için yönlendirecektir.
