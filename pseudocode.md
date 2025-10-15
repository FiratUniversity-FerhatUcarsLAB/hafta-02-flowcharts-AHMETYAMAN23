BAŞLA  

// 1. Kart takma ve PIN doğrulama
Kart_Tak()
hak = 3

TEKRAR
    PIN = PIN_GİR()
    
    EĞER PIN_DOĞRU_MU(PIN) İSE
        ÇIK // Doğru PIN girildiyse döngüden çık
    DEĞİLSE
        hak = hak - 1
        EKRANA_YAZ("Hatalı PIN. Kalan deneme hakkı: ", hak)
    SON
    
    EĞER hak == 0 İSE
        EKRANA_YAZ("Kart bloke oldu. Lütfen bankanızla iletişime geçin.")
        Kartı_Yut()
        DUR
    SON
TEKRAR_SONU

// 2. Bakiye sorgulama
bakiye = BAKİYE_SORGULA()

// 3. Para çekme işlemi
TEKRAR
    çekilecek_tutar = TUTAR_GİR()
    
    // 4. Yetersiz bakiye kontrolü
    EĞER çekilecek_tutar > bakiye İSE
        EKRANA_YAZ("Yetersiz bakiye.")
        DEVAM_ET // tekrar tutar gir
    SON
    
    // 5. 20 TL katı kontrolü
    EĞER çekilecek_tutar MOD 20 ≠ 0 İSE
        EKRANA_YAZ("Tutar 20 TL'nin katı olmalıdır.")
        DEVAM_ET
    SON

    // 6. Günlük limit kontrolü
    EĞER çekilecek_tutar > GÜNLÜK_LIMIT İSE
        EKRANA_YAZ("Günlük limit aşıldı. Maksimum çekilebilir tutar: ", GÜNLÜK_LIMIT)
        DEVAM_ET
    SON
    
    // 7. Para verme işlemi
    bakiye = bakiye - çekilecek_tutar
    PARA_VER(çekilecek_tutar)
    FİŞ_YAZDIR()
    EKRANA_YAZ("İşlem başarılı. Kalan bakiye: ", bakiye)
    
    // 8. Başka işlem isteği
    EKRANA_YAZ("Başka bir işlem yapmak ister misiniz? (E/H)")
    cevap = CEVAP_GİR()
    
    EĞER cevap == "H" İSE
        Kartı_İade_Et()
        EKRANA_YAZ("İyi günler dileriz.")
        DUR
    SON

TEKRAR_SONU // Eğer kullanıcı başka işlem isterse başa döner

BİTİR
