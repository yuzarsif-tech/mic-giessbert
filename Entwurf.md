---
title: MIC – Gießbert Systementwurf (ilk taslak)
created: 2026-10-15
modified: 2026-10-16
type: note
status: active
tags: [mic, giessbert, entwurf, bom]
---

# Gießbert – Systementwurf (ilk taslak)

Animasyon: [Entwurf.html](Entwurf.html) (sekmeler: Systementwurf / Offene Fragen; sağ üstte 🔍 3D → ESP32.html; sulama akışı, „Weiter ▶“ ile adım adım; Almanca, DE/TR ve ☀/☾ düğmeleri). Blok şeması
HTML'den şimdilik kaldırıldı, içerik aşağıdaki tabloda.
Animasyonun altında **Pseudoalgorithmus** (akış diyagramı, DE/TR): 1 nem ölç → 2 kuru mu? → 3 tankta su var mı? → 4 pompa (maks. X s) → 5 kısa bekle → 1; nemliyse/tank boşsa (uyarı) → 6 uzun bekle (~30 dk) → 1. Ayrıca buton → tank kontrolü → elle sulama.
Animasyonda **Alle / Sensoren / Aktoren** filtresi: seçilen grup parlar, gerisi soluklaşır; altında kısa açıklama (Feuchtesensor, Schwimmerschalter / Pumpe, Q1).
HTML'de Systementwurf sekmesinin başında sade „Komponenten“ listesi var (6 grup, sadece parça adları). Ayrıntılar (MOSFET, diyot, PTC…) sadece aşağıdaki tabloda.
Şema [[Kickoff]] slayt 19'a göre: Sensoren / Bedienung → µC → Aktoren / Rückmeldung, altta Querschnitt.
Fiyatlar henüz kontrol edilmedi; BoM için 23.10.'a kadar netleşecek.

| Blok | Parça | Neden | Feature (Slayt 39) |
| --- | --- | --- | --- |
| Beyin (µC) | ESP32-C3 | Veriliyor; ADC, GPIO, PWM, WiFi | Ortak |
| Sensör | Kapasitif toprak nem sensörü | "Kuru toprak → su"; rezistif paslanır | A – Bedarf erkennen |
| Sensör | Tank seviye sensörü (şamandıra şalteri) | "Boş tank → uyarı", pompa kuru çalışmasın | C – Betrieb verständlich |
| Aktör | Pompa (5 V dalgıç veya peristaltik) | Suyu taşır | B – Wasser dosieren |
| Aktör | Pompa sürücüsü: logic-level MOSFET + flyback diyot + gate pull-down | GPIO 3,3 V / birkaç mA, pompa yüzlerce mA; reset'te pompa kapalı kalsın | B – Wasser dosieren |
| Bedienung | Buton | Elle sulama / uyarıyı onayla | C |
| Rückmeldung | LED (RGB) veya küçük OLED + buzzer | Tank boş, sensör hatası, durum | C – Betrieb verständlich |
| Energie | 5 V USB adaptörü + büyük kondansatör (+ sigorta/PTC) | Pompa kalkış akımı ESP32'yi resetlemesin | Ortak |
| Mechanik | Su tankı, silikon hortum, damlatıcı uç, saksı | Su yolu; elektronikten ayrı | Ortak |

## Dikkat
1. **Sifon:** tank saksıdan yüksekse pompa durunca su akmaya devam eder → tank aşağıda veya peristaltik pompa.
2. **Boot'ta pompa kapalı:** MOSFET gate'inde pull-down (Fehlerfall, slayt 22).
3. **Doz:** süre × debi; tek seferde maksimum süre, sonra bekleme (Harte Nuss).

## Açık sorular (Offene Fragen)
Entwurf.html'de ayrı sekme: „Offene Fragen“. Cevap gelince hem burada hem HTML'deki „Notiz“ satırına yazılır.

**Sensoren – Werte einlesen**
1. Sensörler ESP32'ye nasıl bağlanır? (hangi pin analog/dijital, 3,3 V, GND)
2. ESP32 nem sensöründeki değeri nasıl okur? (gerilim → sayı / %)
3. ESP32 şamandıra şalterini nasıl okur? (evet/hayır → pin)

**Aktor – Pumpe**
4. ESP32 pompayı nasıl açıp kapatır? (pin neden yetmez, Q1 ne yapar)
5. Pompa tipi: ucuz dalgıç mı, hassas peristaltik mi? (sifon etkisi)

**Verdrahtung und Platine**
6. Her şey ESP32'ye nasıl bağlanır? (pinler, kablolar, ortak GND)
7. Neden kendi PCB'mize ihtiyacımız var? (kartta ne, dışarıda ne)

**Festlegungen**
8. Eşik değeri ve maks. pompa süresi ne olacak?
9. Tek saksı mı, birden fazla mı? Uyarı sadece cihazda mı, WiFi ile telefonda mı?