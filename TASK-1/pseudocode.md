FONKSIYON ATM_ParaCekme()
BAŞLANGIÇ

    // Değişken Tanımlamaları
    int HATALI_PIN_SAYISI = 0
    const int PIN_LIMITI = 3
    const int GUNLUK_LIMIT = 1000 // Örnek günlük limit
    float HESAP_BAKIYESI = 500.00 // Örnek hesap bakiyesi
    float GUNLUK_CEKILEN_TOPLAM = 0.00 // Gün içinde çekilen toplam tutar
    bool ISLEM_DEVAM = DOĞRU
    string KART_DURUMU = "AKTIF"

    // 1. Kart Takma ve PIN Girişi
    EKRANA_YAZ("Lütfen kartınızı takınız.")
    KART_TAKILDI = KART_OKUYUCUDAN_AL()

    EĞER KART_TAKILDI İSE
        DÖNGÜ: PIN_GİRİŞ_DÖNGÜSÜ
            EKRANA_YAZ("Lütfen 4 haneli PIN kodunuzu giriniz.")
            PIN_GİRİLDİ = KULLANICIDAN_AL()
            DOĞRU_PIN = BANKA_SISTEMINDEN_KONTROL_ET(KART_NUMARASI) // Varsayımsal

            EĞER PIN_GİRİLDİ EŞİT DEĞİL DOĞRU_PIN İSE
                HATALI_PIN_SAYISI = HATALI_PIN_SAYISI + 1
                EKRANA_YAZ("Hatalı PIN. Kalan deneme hakkı: " + (PIN_LIMITI - HATALI_PIN_SAYISI))
            
                EĞER HATALI_PIN_SAYISI EŞİT PIN_LIMITI İSE
                    KART_DURUMU = "BLOKE"
                    EKRANA_YAZ("PIN'i 3 kez hatalı girdiniz. Kartınız bloke edilmiştir.")
                    KART_İADE_ET()
                    ÇIKIŞ YAP: PIN_GİRİŞ_DÖNGÜSÜ // Döngüden çık
                SON
            DEĞİLSE // PIN Doğru
                ÇIKIŞ YAP: PIN_GİRİŞ_DÖNGÜSÜ // Döngüden çık
            SON
        SON

        EĞER KART_DURUMU EŞİT "BLOKE" İSE
            GERİ DÖN // İşlemi sonlandır

        // 2. Ana İşlem Döngüsü
        DÖNGÜ: ANA_İŞLEM_DÖNGÜSÜ (ISLEM_DEVAM EŞİT DOĞRU İKEN)
            
            EKRANA_YAZ("Lütfen yapmak istediğiniz işlemi seçiniz:")
            EKRANA_YAZ("1. Para Çekme")
            EKRANA_YAZ("2. Bakiye Sorgulama")
            EKRANA_YAZ("3. Çıkış")
            SECIM = KULLANICIDAN_AL()

            EĞER SECIM EŞİT "3" İSE
                ISLEM_DEVAM = YANLIŞ
            DEĞİLSE EĞER SECIM EŞİT "2" İSE
                // 3. Bakiye Sorgulama
                EKRANA_YAZ("Hesap Bakiyeniz: " + HESAP_BAKIYESI)
            DEĞİLSE EĞER SECIM EŞİT "1" İSE

                // Para Çekme Adımları
                EKRANA_YAZ("Çekmek istediğiniz tutarı giriniz.")
                CEKILECEK_TUTAR = KULLANICIDAN_AL_SAYI()
                
                // 4. Günlük Limit Kontrolü
                EĞER (GUNLUK_CEKILEN_TOPLAM + CEKILECEK_TUTAR) > GUNLUK_LIMIT İSE
                    EKRANA_YAZ("Günlük para çekme limitiniz aşıldı.")
                    EKRANA_YAZ("Kalan günlük limit: " + (GUNLUK_LIMIT - GUNLUK_CEKILEN_TOPLAM))
                
                // 5. 20 TL Katı Kontrolü
                DEĞİLSE EĞER MODULO(CEKILECEK_TUTAR, 20) EŞİT DEĞİL 0 İSE
                    EKRANA_YAZ("Lütfen 20 TL'nin katları şeklinde bir tutar giriniz.")
                
                // 6. Yetersiz Bakiye Kontrolü
                DEĞİLSE EĞER CEKILECEK_TUTAR > HESAP_BAKIYESI İSE
                    EKRANA_YAZ("Hesabınızda yeterli bakiye bulunmamaktadır.")
                
                // 7. İşlem Başarılı
                DEĞİLSE 
                    // Bakiyeyi güncelle
                    HESAP_BAKIYESI = HESAP_BAKIYESI - CEKILECEK_TUTAR
                    GUNLUK_CEKILEN_TOPLAM = GUNLUK_CEKILEN_TOPLAM + CEKILECEK_TUTAR
                    
                    // Para Verilir
                    ATM_PARA_VER(CEKILECEK_TUTAR)
                    EKRANA_YAZ(CEKILECEK_TUTAR + " TL çekildi.")
                    
                    // Fiş Çıkar
                    FIS_YAZDIR("Çekilen Tutar: " + CEKILECEK_TUTAR + ", Yeni Bakiye: " + HESAP_BAKIYESI)
                    EKRANA_YAZ("Lütfen paranızı ve fişinizi alınız.")

                SON // Koşul Kontrolleri Sonu

            DEĞİLSE
                EKRANA_YAZ("Geçersiz seçim.")
            SON

            // Başka işlem yapmak ister misiniz?
            EĞER ISLEM_DEVAM EŞİT DOĞRU İSE
                EKRANA_YAZ("Başka bir işlem yapmak ister misiniz? (E/H)")
                DEVAM_CEVABI = KULLANICIDAN_AL_METIN()
                EĞER DEVAM_CEVABI EŞİT "H" VEYA DEVAM_CEVABI EŞİT "h" İSE
                    ISLEM_DEVAM = YANLIŞ
                SON
            SON

        SON // ANA_İŞLEM_DÖNGÜSÜ Sonu

        // 8. Kartı İade Et ve Çıkış
        EKRANA_YAZ("Bizi tercih ettiğiniz için teşekkürler.")
        KART_İADE_ET()
    
    DEĞİLSE
        EKRANA_YAZ("Kart takılma hatası.")
    SON

GERİ DÖN
SON FONKSIYON // ATM_ParaCekme
