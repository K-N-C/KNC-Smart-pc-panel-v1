# KNC Smart PC Panel — Proje Tanıtımı

## Bu Proje Nedir?

KNC Smart PC Panel, bilgisayarınızın **CPU/GPU sıcaklığı, kullanım oranı, fan
hızı, FPS** gibi anlık verilerini küçük bir **16x2 LCD ekranda** gösteren,
masaüstünüzde durabilecek bir donanım + yazılım projesidir. Ayrıca isteğe
bağlı olarak **evden uzaktayken bile bilgisayarınızı açmanızı** sağlar.

İki parçadan oluşur:

* **Windows programı** (bilgisayarınızda çalışır, verileri okur ve gönderir)
* **ESP32 + LCD ekran** (bu verileri alıp gösteren küçük bir donanım)

## Nasıl Çalışır? (Genel Mantık)

```
[Bilgisayarınız]                                                                               [ESP32 + LCD]
     │                                                                                              │
  HWiNFO             ──►             Windows Programı     ──►     USB         ──►            LCD Ekranda gösterir
 (sensör verileri)         (Verileri okur,her saniye USB kablosuyla gönderir)      (sayfalar halinde CPU/GPU/RAM/... döner)
                                
```

1. **HWiNFO** adlı program, bilgisayarınızdaki tüm sensörleri
(sıcaklık, kullanım, fan hızı vb.) okur.
2. **KNC Smart PC Panel** (Windows programı), bu verileri HWiNFO'dan alır ve
her saniye USB kablosu üzerinden **ESP32**'ye gönderir.
3. **ESP32**, gelen bu verileri **16x2 LCD ekranda** sayfalar hâlinde (CPU,
GPU, RAM, VRAM, sıcaklıklar, fanlar, güç, ağ hızı, FPS...) döngüsel
olarak gösterir.
4. ESP32'ye ayrıca bir **DS18B20 sıcaklık sensörü** bağlanır — bu, kasa içi
sıcaklığı doğrudan ölçüp ekranda gösterir (ve PC programına geri bildirir).
5. İsteğe bağlı bir **röle modülü**, anakartın güç düğmesi pinlerine bağlanır.
**SinRic Pro** adlı bir bulut servisi üzerinden (evden uzaktayken bile)
telefonunuzdan komut gönderip bilgisayarınızı açabilirsiniz.

## ESP32 Nedir, Bu Projede Ne İşe Yarar?

ESP32, üzerinde **Wi-Fi** özelliği de bulunan, küçük, ucuz ve programlanabilir
bir mikrodenetleyici (mini bilgisayar) kartıdır. Arduino ile aynı mantıkla
çalışır ama Wi-Fi'ye sahip olması onu bu proje için ideal yapar. Bu projede
ESP32'nin görevleri:

* USB üzerinden PC'den gelen verileri okumak ve LCD ekranda göstermek
* DS18B20 sıcaklık sensöründen kasa içi sıcaklığı ölçmek
* Wi-Fi'ye bağlanıp SinRic Pro bulut servisi ile konuşmak
* Uzaktan "aç" komutu geldiğinde bir röleyi tetikleyip anakartın güç
düğmesine "basmış" gibi davranmak

## Projenin Amacı

Oyun bilgisayarlarında veya güçlü sistemlerde sıcaklıkları anlık takip etmek
genelde ekrana bakmayı (overlay açmayı) gerektirir. Bu proje, bu bilgileri
**ayrı, küçük bir fiziksel ekranda** her an görünür hâlde tutarak hem şık bir
görünüm sağlar hem de bilgisayarınızı elinizin altında olmadığı zamanlarda
bile (uzaktan açma özelliğiyle) yönetmenizi kolaylaştırır. Kişisel ve
ticari olmayan kullanım için ücretsiz bir hobi projesidir.

## Kullanılan Malzemeler (Donanım)

|Malzeme|Açıklama|
|-|-|
|ESP32 geliştirme kartı|Wi-Fi özellikli herhangi bir ESP32 DevKit|
|16x2 I2C LCD ekran|I2C adaptörlü olmalı (4 pinli: VCC/GND/SDA/SCL)|
|Röle modülü (opsiyonel)|5V tek kanallı, uzaktan açma özelliği için|
|DS18B20 sıcaklık sensörü|Kasa içi sıcaklık ölçümü için, dijital (1-Wire)|
|4.7kΩ direnç |DS18B20'nin veri hattı için (pull-up direnci)|
|USB kablosu|ESP32'yi PC'ye bağlamak için (hem güç hem veri)|
|Jumper kablolar|Bağlantılar için|
|Kutu/kasa (opsiyonel)|Düzgün bir görünüm için|





## Donanım bağlantıları

|LCD I2C (SDA)|GPIO 21|Kartınızın varsayılan I2C pini (değişmediyse)|
|LCD I2C (SCL)|GPIO 22|Kartınızın varsayılan I2C pini (değişmediyse)|
|Röle IN (sinyal)|**GPIO 26**|Aktif-HIGH röle varsayılıyor|
|Röle VCC / GND|5V (veya 3.3V) / GND|Röle modülünüze göre|
|Röle NO+COM|Anakart **Power SW (PWR\_SW)** pinlerine **paralel**|Kasa güç düğmesiyle aynı yere|
|DS18B20 DATA|**GPIO 4**|DATA ile VCC arasına 4.7kΩ pull-up direnci şart|
|DS18B20 VCC / GND|3.3V / GND||

Devre şeması ekte bulunmaktadır 

Röle **tam 1 saniye** tetiklenip otomatik kapanır (fiziksel power button'a kısa
basış simülasyonu) — anakartı zorla kapatacak kadar uzun tutulmaz.



## Gerekli Yazılımlar

**Bilgisayarda çalıştırmak için:**

* **HWiNFO**  — sensör verilerini okumak için.
Kurulumda "Shared Memory Support" seçeneği işaretlenmeli.
* **KNC Smart PC Panel** — bu projenin Windows programı (hazır `.exe` olarak
ya da kaynak koddan Visual Studio ile derlenebilir).

**ESP32'yi programlamak için (bir kere yapılır):**

* **Arduino IDE** (ücretsiz)
* Arduino IDE içine **ESP32 kart desteği** eklenmesi (Board Manager üzerinden)
* Şu kütüphaneler (Arduino IDE'nin Library Manager'ından kurulur):
`LiquidCrystal I2C`, `OneWire`, `DallasTemperature`, `SinricPro`

**Uzaktan açma özelliğini kullanmak isteyenler için (opsiyonel):**

* **SinRic Pro** üzerinde bir hesap ve bir "Switch" cihazı

## Sıfırdan Kurulum (Hiç Bilmeyen Biri İçin)

1. **Donanımı birleştirin:** LCD ekranı ESP32'nin I2C pinlerine, DS18B20'yi
(varsa) belirtilen pine, röleyi (varsa) belirtilen pine bağlayın. Tam pin
listesi `.ino` dosyasının başında.
2. **Arduino IDE'yi kurun**, ESP32 kart desteğini ve yukarıdaki 4 kütüphaneyi
ekleyin.
3. ESP32'yi USB ile bilgisayara takın, Arduino IDE'de `.ino` dosyasını açıp
doğru kartı/portu seçip **Upload** (Yükle) butonuna basın.
4. **HWiNFO'yu indirip kurun**, Settings → Shared Memory Support'u işaretleyin
ve HWiNFO'yu açık bırakın.
5. **KNC Smart PC Panel** programını çalıştırın. İlk açılışta sizi bir
**Kurulum Sihirbazı** karşılar; sırasıyla dil seçimi, kullanım koşulları
onayı, HWiNFO kontrolü, doğru COM portunun seçimi, sensörlerin
eşleştirilmesi ve (isteğe bağlı) Wi-Fi/SinRic Pro bilgisi adımlarını
takip etmeniz yeterli.
6. Kurulum bitince ekranda birkaç saniye içinde gerçek verileriniz dönmeye
başlar. 🎉

Bir sorun yaşarsanız, program arka planda çalışırken (X ile kapatsanız bile
sistem tepsisinde çalışmaya devam eder) **Ayarlar** ve **Kurulum Sihirbazı**
seçeneklerinden ayarları istediğiniz zaman tekrar düzenleyebilirsiniz.

## Öne Çıkan Özellikler

* Gerçek zamanlı CPU/GPU/RAM/VRAM sıcaklık ve kullanım takibi
* Kasa içi sıcaklığın doğrudan ESP32 üzerinden ölçülmesi (DS18B20)
* Ayarlanabilir sıcaklık uyarı eşikleri (ekranda yanıp sönen uyarı)
* SinRic Pro ile evden uzaktan bilgisayar açma
* Türkçe / İngilizce dil desteği
* Windows açılışında otomatik başlama, sistem tepsisinde çalışma
* Sensör eşleştirmede arama/filtreleme desteği


## LCD ekran sayfaları

Sayfa 1 
CPU 53.4°   %18
GPU 49.6°   %14

Sayfa 2
RAM   55.6°    %46
VRAM  50.5°    %64

sayfa 3
BOARD 40.5°
CASE 30.2°

Sayfa 4
SSD1  42°
SSD2  40°

sayfa 5
NET ↓  12 MB 
NET ↑  1.5 MB

Sayfa 6
CPU  1450 rpm 
GPU  1500 rpm 

Sayfa 7
CPU 65W
GPU 180W

Sayfa 8
FPS 150
WiFi OK USB OK 


Uyarı ekranı
⚠ CPU HOT ! ⚠ 
  87.2°C

PC kapalı  
KNCSmartPCPanel
WiFi OK  Pc OFF  

Bilgisayar açılırken
KNC Smart PC

Starting...
↓
Loading...
↓
Normal sayfalar.

*Bu proje kişisel ve ticari olmayan kullanım için ücretsiz olarak paylaşılmıştır.
Kullanım koşulları ve sorumluluk reddi için `LICENSE.md` dosyasına bakınız.*

