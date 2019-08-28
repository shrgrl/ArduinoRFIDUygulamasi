# ArduinoRFIDUygulamasi

Arduino Uno ile çeşitli sensörlerden fiziksel bilgi alabilir, bu bilgiler ile çeşitli deneyler yapabilirsiniz. Ayrıca motor, LED, buzzer gibi uyarıcılardan bir çıktı elde edebilirsiniz. Bu gibi elektronik komponentleri Arduino Uno kartına bağlayarak kontrol etmek için temel bir programlama bilgisi yeterlidir. Projelerin seviyesine göre gerekli olan elektronik ve programlama bilgisi seviyesi de artacaktır. Boyut olarak çok daha küçük ve çok daha büyük modeller olsa da Arduino Uno’nun boyutu projelere göre en standart olanıdır. 14 adet dijital çıkış pini bulunması 14 farklı dijital sensörün ve uyarıcının kontrol edilebileceği anlamına gelmektedir. Bu da birçok proje için yeterli bir sayıdır. Bu dijital çıkışlardan 5 tanesi PWM çıkışıdır. Motorların hızı, LED’lerdeki parlaklık seviyeleri gibi analog olarak kontrol edilmesi istenen uyarıcılar bu PWM pinlerine bağlanarak kontrol edilir. Arduino Uno’daki 6 tane analog giriş ise analog giriş sinyali alabildiğimiz sensörler içindir.
Arduino Uno ile LED yakıp söndürmek gibi en temel uygulamalardan drone, robot, akıllı ev otomasyonu, hırsız alarm sistemi, park sensörü gibi daha gelişmiş projeler de yapabilirsiniz. Bu tamamen ne yapmak istediğinizle alakalıdır. Kısacası Arduino Uno, standart boyutlarda bir kontrol kartı olup, basitten zora birçok uygulamada elektronik devreleri kontrol etmenizi sağlamaktadır.

![](https://github.com/shrgrl/ArduinoRFIDUygulamasi/blob/master/images/img1.jpg)

Bu projede belirlediğimiz kartı okuduğunda yeşil led yanarken, farklı bir kart okuttuğumuzda kırmızı led yanacak.

Gerekli malzemeler;
<td>
  <li>Arduino UNO</li>
  <li>Led x2</li>
  <li>220Ω Direnç x2</li>
  <li>RFID-RC522 Modülü</li>
  <li>Breadboard</li>
  <li>Jumper Kablo</li>
</td>

Öncelikle doğru devre şeması tasarlamam gerekti. Devrenin fritzing şeması aşağıdaki gibidir:

![](https://github.com/shrgrl/ArduinoRFIDUygulamasi/blob/master/images/img2.jpg)

Fritzing şemasını hazırladığım devreyi dikkatlice kurdum. Ardından kullanacağım kod parçasını arduino programı yazdım.

```
#include <SPI.h>
#include <MFRC522.h>
 
MFRC522 mfrc522(10, 9);
 
void setup()
{
Serial.begin(9600);
SPI.begin();
mfrc522.PCD_Init();
pinMode(8, OUTPUT);
pinMode(7, OUTPUT);
}
void loop()
{

if ( ! mfrc522.PICC_IsNewCardPresent()){
return;
}

if ( ! mfrc522.PICC_ReadCardSerial()) {
return;
}
 
Serial.print("Tag:");
String content= "";
for (byte i = 0; i < mfrc522.uid.size; i++) {
Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
Serial.print(mfrc522.uid.uidByte[i], HEX);
content.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
content.concat(String(mfrc522.uid.uidByte[i], HEX));
}
content.toUpperCase();
content = content.substring(1);
if(content == "A6 4A 76 AC"){
digitalWrite(8, HIGH);
delay(3000);
digitalWrite(8, LOW);
}else{
digitalWrite(7, HIGH);
delay(3000);
digitalWrite(7, LOW);
}
Serial.println();
}
```

Projede kullanacağım MFRC522 kütüphanesi benim programımda yüklü değildi. Bunun için indirdiğim MFRC522 kütüphanesinin zip dosyasını indirdim ve bilgisayarımdaki arduino kurulum dosyasının içindeki libraries içine ekledim.

![](https://github.com/shrgrl/ArduinoRFIDUygulamasi/blob/master/images/img3.jpg)

Kütüphaneyi ekledikten sonra programı derledim ve çalıştırdım. Herşey sorunsuz ilerledi. Projeyi çalıştırdıktan sonra ilk olarak seri port ekranını açıp okuttuğum kartın içerisindeki bilgiyi aldım ve okuduğum bilgiyi aşağıdaki satıra yazdım.

```
if(content == "4B 00 B3 22")
```

İstediğimiz kartın bilgisini projeye tanıttıktan sonra projeyi tekrar derleyip yükledim ve tekrar kartımı okuttum. Tanıttığım kartı gösterdiğimde yeşil led yanarken farklı bir kart okuttuğumda kırmızı led yandı.

![](https://github.com/shrgrl/ArduinoRFIDUygulamasi/blob/master/images/img4.jpg)
