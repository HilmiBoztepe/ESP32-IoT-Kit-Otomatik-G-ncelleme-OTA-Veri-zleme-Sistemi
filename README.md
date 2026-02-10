# *ESP32-IoT-Kit-Otomatik-Güncelleme-OTA & Veri-İzleme-Sistemi*

🚀 ESP32 IoT Kit: Otomatik Güncelleme (OTA) & Veri İzleme Sistemi

Bu proje, Duranka Teknoloji ESP32 tabanlı sensör kitini kullanarak; verileri hem bir web paneline (FastAPI) hem de MQTT broker'ına aktaran, aynı zamanda kendi yazılım versiyonunu otomatik olarak güncelleyebilen (Self-Updating) bir IoT ekosistemidir.

✨ Öne Çıkan Özellikler

1. 🌐 Akıllı OTA Güncelleme: Cihaz, API üzerinden sunucudaki en güncel versiyonu kontrol eder. Eğer yeni bir .bin dosyası varsa kendini otomatik olarak günceller.

2.📊 Çift Kanallı Veri Aktarımı: Veriler hem HTTP Post ile FastAPI Dashboard'a hem de MQTT üzerinden yayınlanır.

3. 🌡️ Gelişmiş Sensör Entegrasyonu: HDC1080 ve BMP180 ile Sıcaklık, Nem, Basınç, Rakım ve Çiğ Noktası (Dew Point) hesaplama.

4. 📱 Web Tabanlı WiFi Yapılandırma: Cihaz ilk açıldığında AP (Access Point) modunda çalışarak kullanıcıya WiFi bilgilerini girebileceği bir arayüz sunar.

5. 🖥️ OLED Dashboard: Cihaz üzerindeki ekran aracılığıyla anlık durum ve versiyon takibi.

🛠️ Kullanılan Teknolojiler
Donanım: Wemos Lolin32 Lite, HDC1080, BMP180, SSD1306 OLED Ekran.

Gömülü Yazılım: C++ (Arduino/PlatformIO), HTTPUpdate, PubSubClient, ArduinoJson.

Backend: Python, FastAPI, Uvicorn.

## 📂 Proje Yapısı

Proje iki ana dizinden oluşmaktadır: `firmware` (C++ / Arduino) ve `backend` (Python / FastAPI).

```text
├── backend/
│   ├── main1.py            # FastAPI sunucu ve güncelleme mantığı
│   └── updates/            # .bin dosyalarının versiyonlanarak saklandığı klasör
│       └── v2.1.7/         # Örnek versiyon klasörü
│           └── firmware.bin
├── firmware/
│   └── main.cpp            # ESP32 sensör okuma, MQTT ve OTA kodları
└── README.md
```

## 🚀 Kurulum ve Çalıştırma
1. Backend Sunucusunu Başlatma

Sunucu, cihazların veri göndereceği ve güncellemeleri kontrol edeceği merkezdir.

```python
pip install fastapi uvicorn httpx pydantic
python main1.py
```

Dashboard: http://localhost:8000/dashboard adresinden verileri izleyebilirsiniz.

Güncelleme Kontrolü: Sunucu updates/ klasöründeki klasör isimlerini (v1.0.1 vb.) versiyon olarak kabul eder.

2. ESP32 Yazılımını Yükleme

main.cpp dosyasındaki API_BASE_URL ve mqtt_server değişkenlerini kendi bilgisayarınızın yerel IP adresiyle değiştirin.

Arduino IDE veya PlatformIO kullanarak kodu cihazınıza yükleyin.

## 🔄 OTA (Güncelleme) Mantığı Nasıl Çalışır?
- Sorgu: ESP32, belirli aralıklarla /check-update endpoint'ine mevcut VERSION bilgisini gönderir.

- Kıyaslama: FastAPI sunucusu, updates/ klasöründeki en yüksek versiyon numarasını bulur.

- Yanıt: Eğer sunucudaki versiyon cihazdakinden yüksekse, sunucu .bin dosyasının indirme linkini gönderir.

- Güncelleme: ESP32 httpUpdate kütüphanesini kullanarak yeni yazılımı indirir, kurar ve kendini yeniden başlatır.

## 📡 MQTT Spesifikasyonları
Proje, verileri bir MQTT Broker üzerinden hiyerarşik bir yapıda yayınlar. Bu sayede veriler her yerden izlenebilir.

🏗️ Topic Yapısı
| Topic | Açıklama | Örnek Payload |
| :--- | :--- | :--- |
| `iot/sensor/data` | Anlık Sensör Verileri | `{"temp": 25.4, "hum": 45, "ver": "2.1.0"}` |
| `iot/sensor/status` | Bağlantı Durumu (LWT) | `online` / `offline` |
| `iot/sensor/ota` | Güncelleme Logları | `Update in progress...` |

📋 JSON Veri Formatı
```json
{
  "device_id": "ESP32_A1B2",
  "version": "2.1.7",
  "sensors": {
    "temperature": 25.4,
    "humidity": 48.2,
    "pressure": 1012.5,
    "dew_point": 14.2
  }
}
```

## 📝 Veri Akış Şeması
* Sensörler -> Veri Okuma (HDC1080 & BMP180)

* ESP32 -> Veri İşleme (Dew Point Hesabı)

* HTTP POST -> FastAPI Dashboard (Görselleştirme)

* MQTT Publish -> Broker (Node-RED veya Home Assistant entegrasyonu için)

* HTTP GET -> Update Server (Versiyon Kontrolü)

# 🤝 Katkıda Bulunma
Bu proje Duranka Teknoloji kiti üzerinde geliştirilmiştir. Yeni özellikler (Deep Sleep modu, farklı sensör destekleri vb.) eklemek için "Pull Request" gönderebilirsiniz.
