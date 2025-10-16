// DERS KAYIT SİSTEMİ PSEUDOCODE

// --- Başlangıç ---
başla

// Öğrenci girişi (öğrenci no + şifre)
ogrenci <- NULL
loginTekrar:
    ogrNo <- input("Öğrenci numaranızı giriniz:")
    sifre <- input("Şifrenizi giriniz:")

    ogrenci <- ogrenciBilgisiGetir(ogrNo, sifre)
    if ogrenci == NULL then
        yaz("❌ Geçersiz numara veya şifre.")
        tekrarDeneme <- input("Tekrar denemek ister misiniz? (E/H): ")
        if tekrarDeneme == "E" or tekrarDeneme == "e" then
            git loginTekrar
        else
            bitir
        end if
    else
        yaz("👋 Hoş geldiniz, " + ogrenci.ad + " " + ogrenci.soyad)
    end if

// Değişkenler
secilenDersler <- []       // liste
toplamKredi <- 0
maxKredi <- 35

// Ders listesini görüntüleme döngüsü
tumDersler <- dersListesiGetir(ogrenci.bolum)
yaz("Mevcut dersler:")

for her ders in tumDersler:
    yaz(ders.kod + " - " + ders.ad + " (" + ders.kredi + " kredi, kontenjan: " + ders.kontenjan + ")")
end for

// Ders ekleme / çıkarma döngüsü
dersIslem:
    yaz("1 - Ders Ekle")
    yaz("2 - Ders Çıkar")
    yaz("3 - Kayıt Özeti Görüntüle / Onayla")
    yaz("0 - Çıkış")
    secim <- input("Seçiminiz: ")

    if secim == 1 then
        dersKod <- input("Eklemek istediğiniz dersin kodunu giriniz: ")
        ders <- dersBul(dersKod)

        if ders == NULL then
            yaz("Geçersiz ders kodu.")
            git dersIslem
        end if

        // --- KONTENJAN KONTROLÜ ---
        if ders.kontenjan <= 0 then
            yaz("❌ Bu derste kontenjan dolu.")
            git dersIslem
        end if

        // --- ÖNKOŞUL DERS KONTROLÜ ---
        if ders.onKosul != NULL then
            if ders.onKosul not in ogrenci.gecilenDersler then
                yaz("❌ Önkoşul dersi (" + ders.onKosul + ") alınmamış.")
                git dersIslem
            end if
        end if

        // --- ZAMAN ÇAKIŞMASI KONTROLÜ ---
        cakisma <- FALSE
        for her d in secilenDersler:
            if d.zaman == ders.zaman then
                cakisma <- TRUE
            end if
        end for
        if cakisma == TRUE then
            yaz("❌ Zaman çakışması var: " + ders.zaman)
            git dersIslem
        end if

        // --- KREDİ LİMİTİ KONTROLÜ ---
        if toplamKredi + ders.kredi > maxKredi then
            yaz("❌ Toplam kredi limiti aşılıyor! (35 max)")
            git dersIslem
        end if

        // Ders ekleme
        secilenDersler.append(ders)
        toplamKredi <- toplamKredi + ders.kredi
        ders.kontenjan <- ders.kontenjan - 1
        yaz("✅ " + ders.ad + " dersi eklendi.")
        git dersIslem

    else if secim == 2 then
        if secilenDersler boş then
            yaz("Henüz seçilmiş ders yok.")
            git dersIslem
        end if

        yaz("Seçili dersler:")
        for her d in secilenDersler:
            yaz("- " + d.kod + ": " + d.ad)
        end for

        silKod <- input("Silmek istediğiniz dersin kodunu giriniz: ")
        dersSil <- dersBul(silKod)

        if dersSil not in secilenDersler then
            yaz("Bu ders seçili değil.")
        else
            secilenDersler.remove(dersSil)
            toplamKredi <- toplamKredi - dersSil.kredi
            dersSil.kontenjan <- dersSil.kontenjan + 1
            yaz("🗑️ " + dersSil.ad + " dersi çıkarıldı.")
        end if
        git dersIslem

    else if secim == 3 then
        // --- DANIŞMAN ONAYI KONTROLÜ ---
        yaz("📋 Kayıt Özeti:")
        for her d in secilenDersler:
            yaz("- " + d.kod + ": " + d.ad + " (" + d.kredi + " kredi)")
        end for
        yaz("Toplam kredi: " + toplamKredi)

        if ogrenci.GPA < 2.5 then
            yaz("⚠️ Danışman onayı gerekli (GPA < 2.5)")
            onay <- input("Danışman onayı verildi mi? (E/H): ")
            if onay != "E" and onay != "e" then
                yaz("Kayıt tamamlanamadı. Danışman onayı bekleniyor.")
                git dersIslem
            end if
        end if

        // Kayıt onayı
        kayitOnay <- input("Kayıt onaylansın mı? (E/H): ")
        if kayitOnay == "E" or kayitOnay == "e" then
            kaydetKayıt(ogrenci, secilenDersler)
            yaz("✅ Kayıt başarıyla tamamlandı.")
            bitir
        else
            yaz("Kayıt onaylanmadı, değişiklik yapabilirsiniz.")
            git dersIslem
        end if

    else if secim == 0 then
        yaz("Sistemden çıkılıyor...")
        bitir

    else
        yaz("Geçersiz seçim!")
        git dersIslem
    end if

bitir
