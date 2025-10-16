// HASTA İŞLEM SİSTEMİ PSEUDOCODE

// --- Başlangıç ---
başla

// Hasta kimlik doğrulama
tekrar:
    TC <- input("Lütfen TC Kimlik Numaranızı giriniz:")
    if TC geçerli formatta değilse:
        yaz("Geçersiz TC numarası! 11 haneli olmalı.")
        git tekrar
    end if

    hasta <- veritabanındaHastaAra(TC)
    if hasta == yok:
        yaz("Kayıtlı hasta bulunamadı. Lütfen danışmaya başvurun.")
        bitir
    else:
        yaz("Hoş geldiniz, " + hasta.ad + " " + hasta.soyad)
    end if

// Ana işlem döngüsü
tekrar_islem:
    yaz("Lütfen yapmak istediğiniz işlemi seçiniz:")
    yaz("1 - Randevu Al")
    yaz("2 - Tahlil Sonucu Gör")
    yaz("0 - Çıkış")

    secim <- input("Seçiminiz: ")

    // --- KOŞUL: İşlem Seçimi ---
    if secim == 1 then
        // ======================
        // RANDEVU MODÜLÜ BAŞLANGIÇ
        // ======================

        yaz("Poliklinikler listeleniyor...")
        poliklinikler <- sistemdenPoliklinikListesiAl()
        
        for her poliklinik in poliklinikler:
            yaz(poliklinik.kod + " - " + poliklinik.ad)
        end for

        pSecim <- input("Lütfen poliklinik seçiniz (kod giriniz): ")
        secilenPol <- poliklinikBul(pSecim)
        if secilenPol == yok:
            yaz("Geçersiz poliklinik seçimi!")
            git tekrar_islem
        end if

        // Doktor listesi döngüsü
        yaz("Doktorlar listeleniyor...")
        doktorlar <- doktorListesiGetir(secilenPol)

        for her dr in doktorlar:
            yaz(dr.id + " - " + dr.ad + " (" + dr.unvan + ")")
        end for

        dSecim <- input("Doktor seçiniz (ID): ")
        secilenDr <- doktorBul(dSecim)
        if secilenDr == yok:
            yaz("Geçersiz doktor seçimi!")
            git tekrar_islem
        end if

        // Randevu saati seçimi döngüsü
        saatler <- uygunSaatleriGetir(secilenDr)
        if saatler boş:
            yaz("Bu doktorda uygun randevu bulunmuyor.")
            git tekrar_islem
        end if

        yaz("Uygun saatler:")
        for her s in saatler:
            yaz("- " + s)
        end for

        sSecim <- input("Randevu saatinizi seçiniz (örn: 10:30): ")
        if sSecim not in saatler:
            yaz("Geçersiz saat seçimi!")
            git tekrar_islem
        end if

        // Randevu onayı
        yaz("Randevu Özeti:")
        yaz("Hasta: " + hasta.ad + " " + hasta.soyad)
        yaz("Poliklinik: " + secilenPol.ad)
        yaz("Doktor: " + secilenDr.ad)
        yaz("Saat: " + sSecim)
        
        onay <- input("Onaylıyor musunuz? (E/H): ")
        if onay == "E" or onay == "e":
            randevuKaydet(hasta, secilenPol, secilenDr, sSecim)
            smsGonder(hasta.tel, "Randevunuz onaylandı: " + sSecim)
            yaz("✅ Randevunuz başarıyla oluşturuldu ve SMS gönderildi.")
        else:
            yaz("Randevu iptal edildi.")
        end if

        // Randevu modülü bitti
        // ======================
        
    else if secim == 2 then
        // ======================
        // TAHLİL MODÜLÜ BAŞLANGIÇ
        // ======================

        yaz("Tahlil sonuçları sorgulanıyor...")
        tahliller <- tahlilListesiGetir(hasta.TC)

        // Koşul: Tahlil var mı?
        if tahliller boş:
            yaz("Henüz yapılmış tahlil bulunmamaktadır.")
            git tekrar_islem
        end if

        // Tahlil seçimi döngüsü
        yaz("Mevcut tahlilleriniz:")
        for her t in tahliller:
            yaz(t.id + " - " + t.tarih + " (" + t.tur + ")")
        end for

        tSecim <- input("Tahlil ID'si giriniz: ")
        secilenTahlil <- tahlilBul(tSecim)

        if secilenTahlil == yok:
            yaz("Geçersiz tahlil seçimi!")
            git tekrar_islem
        end if

        // Koşul: Sonuç hazır mı?
        if secilenTahlil.sonucHazir == FALSE:
            yaz("⏳ Sonuçlar henüz hazır değil. Lütfen daha sonra tekrar deneyin.")
            git tekrar_islem
        else:
            yaz("✅ Sonuçlar bulundu:")
            yaz(secilenTahlil.sonucMetni)
            indirme <- input("Sonuçları PDF olarak indirmek ister misiniz? (E/H): ")
            if indirme == "E" or indirme == "e":
                pdfOlusturVeIndir(secilenTahlil)
                yaz("PDF dosyası indirildi.")
            end if
        end if

        // Tahlil modülü bitti
        // ======================

    else if secim == 0 then
        yaz("Sistemden çıkış yapılıyor...")
        bitir

    else
        yaz("Geçersiz seçim! Lütfen 1, 2 veya 0 giriniz.")
    end if

    // --- Döngü: başka işlem yapmak ister misiniz? ---
    devam <- input("Başka işlem yapmak ister misiniz? (E/H): ")
    if devam == "E" or devam == "e":
        git tekrar_islem
    else:
        yaz("İyi günler, " + hasta.ad + "!")
        bitir
    end if

bitir
