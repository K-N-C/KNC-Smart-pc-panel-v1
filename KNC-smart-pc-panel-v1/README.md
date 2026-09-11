# KNC Smart PC Panel

Bilgisayarınızdaki sıcaklık, kullanım ve performans verilerini ESP32'ye bağlı
16x2 LCD ekrana aktaran, ayrıca SinRic Pro üzerinden evden uzaktayken bile
bilgisayarınızı açmanızı sağlayan bir yardımcı yazılım.

## Özellikler

* HWiNFO'dan CPU/GPU/RAM/VRAM/SSD sıcaklık ve kullanım verilerini okur
* ESP32 + 16x2 I2C LCD ekranda sayfalar halinde gösterir
* DS18B20 ile kasa içi sıcaklığı doğrudan ESP32 üzerinden ölçer
* Yapılandırılabilir sıcaklık uyarı eşikleri (ekranda titreşen uyarı)
* SinRic Pro üzerinden uzaktan PC açma (röle ile Power SW simülasyonu)
* Sistem tepsisinde arka planda çalışır, Windows açılışında otomatik başlar
* İlk çalıştırmada adım adım kurulum sihirbazı

## Kurulum

### Windows Programı

exe dosyasını indirip çalıştırın sizi kurulum ekranı karşılayacak kurulum ekranından gerekli bilgileri doldurup devam edin.

### ESP32 Firmware

`KNCSmartPCPanel\\\_ESP32.ino` dosyasını Arduino IDE ile açıp aşağıdaki
kütüphaneleri kurduktan sonra ESP32'ye yükleyin:

* LiquidCrystal I2C
* OneWire
* DallasTemperature
* SinricPro

Donanım bağlantıları ve pin şeması için `.ino` dosyasının başındaki yorum
bloğuna bakın.

## Gereksinimler

* Windows 10/11
* [HWiNFO](https://www.hwinfo.com/) (Shared Memory Support etkinleştirilmiş)
* ESP32 geliştirme kartı + 16x2 I2C LCD ekran
* (Opsiyonel) Röle modülü + DS18B20 sıcaklık sensörü



## Notlar

* Pencereyi kapatma (X) programı kapatmaz, sistem tepsisine küçültür. Tamamen
kapatmak için tepsi simgesine sağ tık → **Çıkış**.
* Kasa sıcaklığı PC'den değil DS18B20'den okunuyor; sensör takılı
değilse veya bağlantı koparsa ekranda **"CASE SENSOR YOK"** yazar.
* Sıcaklık eşiklerini Ayarlar → **Sıcaklık Eşikleri** sekmesinden değiştirip
Kaydet'e bastığınızda ESP32'ye anında gönderilir (yeniden başlatma gerekmez).
* SinRic Pro bilgisi her kaydedildiğinde ESP32 kendini yeniden başlatır
(bağlantının temiz kurulması için) — birkaç saniye ekran boş kalabilir.



## Lisans

[LICENSE.md](LICENSE.md) dosyasına bakın. Kişisel ve ticari olmayan kullanım
için ücretsizdir. HWiNFO, SinRic Pro ve diğer üçüncü taraf ürün/hizmet adları
ilgili hak sahiplerine aittir; bu proje onların resmi bir parçası değildir.

