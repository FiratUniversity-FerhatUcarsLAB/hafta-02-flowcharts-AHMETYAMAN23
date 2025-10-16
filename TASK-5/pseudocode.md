// =========================================================
//  AKILLI EV GÜVENLİK SİSTEMİ (Sonsuz Döngü Yaklaşımı)
// =========================================================

başla

// 1️⃣ Sistem başlatma
yaz("🔐 Akıllı Ev Güvenlik Sistemi başlatılıyor...")

// Gerekli sensörleri tanımla
hareketSensörü <- "Kapı / Pencere Hareket Sensörü"
isikSensörü <- "Gece / Gündüz Sensörü"
dumanSensörü <- "Yangın / Duman Sensörü"
sicaklikSensörü <- "Isı Sensörü"
kamera <- "Görüntü Tanıma Sensörü"

// Başlangıç durumları
alarmDurumu <- "KAPALI"
tehditSeviyesi <- 0
bildirimGonderildi <- YANLIŞ
alarmReset <- YANLIŞ

yaz("✅ Sistem aktif ve izleme modunda.")

// 2️⃣ ANA DÖNGÜ – SÜREKLİ ÇALIŞIR
// “DOĞRU” koşulu ile sonsuza kadar sürer
while DOĞRU do

    // --- Sensör verilerini oku ---
    hareket <- sensörOku(hareketSensörü)
    isik <- sensörOku(isikSensörü)
    duman <- sensörOku(dumanSensörü)
    sicaklik <- sensörOku(sicaklikSensörü)
    kameraDurumu <- sensörOku(kamera)

    // --- Tehdit algılama süreci ---
    tehditSeviyesi <- 0  // her döngü başında sıfırla

    if hareket == "VAR" then
        tehditSeviyesi <- tehditSeviyesi + 1
    end if

    if duman == "VAR" or sicaklik > 50 then
        tehditSeviyesi <- tehditSeviyesi + 2
    end if

    if kameraDurumu == "Yabancı Kişi" then
        tehditSeviyesi <- tehditSeviyesi + 3
    end if

    // --- Tehdit seviyesi değerlendirme ---
    if tehditSeviyesi == 0 then
        yaz("🌙 Güvenli durumda, sistem izliyor...")
        alarmDurumu <- "KAPALI"
        bildirimGonderildi <- YANLIŞ

    else if tehditSeviyesi == 1 then
        yaz("⚠️ Düşük seviye tehdit algılandı! Kapı/Pencere kontrol ediliyor.")
        alarmDurumu <- "HAZIR"

    else if tehditSeviyesi == 2 then
        yaz("🚨 Orta seviye tehdit (Duman veya yüksek ısı).")
        alarmDurumu <- "AÇIK"
        alarmCalistir()
        if bildirimGonderildi == YANLIŞ then
            bildirimGonder("Ev sahibine uyarı: Duman/Yüksek sıcaklık tespit edildi!")
            bildirimGonderildi <- DOĞRU
        end if

    else if tehditSeviyesi >= 3 then
        yaz("🚨🚨 Yüksek seviye tehdit (Yabancı kişi algılandı)!")
        alarmDurumu <- "AÇIK"
        alarmCalistir()
        kameraKaydet()
        if bildirimGonderildi == YANLIŞ then
            bildirimGonder("Güvenlik ihlali! Yetkisiz kişi tespit edildi!")
            bildirimGonderildi <- DOĞRU
        end if
    end if

    // --- Alarm sıfırlama kontrolü ---
    if alarmDurumu == "AÇIK" then
        alarmReset <- input("Alarmı sıfırlamak için 'SIFIRLA' yazın (veya boş bırakın): ")

        if alarmReset == "SIFIRLA" then
            alarmDurumu <- "KAPALI"
            tehditSeviyesi <- 0
            yaz("🔕 Alarm sıfırlandı, sistem tekrar izleme moduna geçti.")
        else
            yaz("⏳ Alarm aktif durumda, izleme devam ediyor...")
        end if
    end if

    // --- Döngü gecikmesi ---
    bekle(5 saniye)  // sensörleri her 5 saniyede bir kontrol et

end while

bitir
