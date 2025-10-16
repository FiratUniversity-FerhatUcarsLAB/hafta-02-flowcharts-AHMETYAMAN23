// E-Ticaret sipariş akışı - Sözde Kod

// Başlangıç değişkenleri
userLoggedIn <- FALSE
categories <- [kategori1, kategori2, ...]
cart <- []                     // her öğe: {productId, name, price, qty}
total <- 0.0
discountCode <- NULL
discountAmount <- 0.0
shippingFee <- 0.0
paymentMethod <- NULL
MIN_ORDER_AMOUNT <- 50.0
FREE_SHIPPING_THRESHOLD <- 200.0

// Yardımcı fonksiyonlar
function checkLogin():
    if userLoggedIn == TRUE:
        return TRUE
    else:
        // Kullanıcıya giriş yapması istenir
        prompt "Lütfen giriş yapın veya kayıt olun."
        if kullanıcı giriş yaparsa:
            userLoggedIn <- TRUE
            return TRUE
        else:
            return FALSE

function listCategories():
    for each category in categories:
        display category
    // Kullanıcı bir kategori seçer veya çıkış yapar
    selectedCategory <- user seçimi
    return selectedCategory

function listProducts(category):
    products <- fetchProducts(category)   // ürün listesi: {id, name, price, stock}
    for each p in products:
        display p.name, p.price, p.stock
    return products

function addToCart(productId, qty):
    product <- getProductById(productId)
    if product == NULL:
        display "Ürün bulunamadı."
        return
    // Stok kontrolü
    if product.stock >= qty:
        existing <- find cart item with productId
        if existing != NULL:
            existing.qty <- existing.qty + qty
        else:
            cart.append({productId, product.name, product.price, qty})
        product.stock <- product.stock - qty      // stok güncellemesi (geçici)
        display qty + " adet eklendi: " + product.name
    else:
        display "Yetersiz stok. Mevcut stok: " + product.stock

function calculateCartTotal():
    total <- 0
    for each item in cart:
        total <- total + (item.price * item.qty)
    return total

function applyDiscount(code):
    if code == NULL or code == "":
        display "İndirim kodu girilmedi."
        return 0.0
    if validateDiscountCode(code):      // kodu doğrulayan iş kuralı
        amount <- computeDiscountAmount(code, total) // sabit veya % bazlı
        display "İndirim uygulandı: " + format(amount) + " TL"
        return amount
    else:
        display "Geçersiz indirim kodu."
        return 0.0

function calculateShipping(totalAfterDiscount):
    if totalAfterDiscount >= FREE_SHIPPING_THRESHOLD:
        return 0.0
    else:
        // Basit kargo hesaplama örneği
        return 10.0 + (totalAfterDiscount * 0.02)   // sabit + yüzde

function selectPaymentMethod():
    methods <- ["Kredi Kartı", "Havale/EFT", "Kapıda Ödeme"]
    display methods
    choice <- user seçimi
    if choice in methods:
        return choice
    else:
        display "Geçersiz ödeme yöntemi."
        return NULL

function confirmOrder(orderSummary):
    display orderSummary
    prompt "Siparişi onaylıyor musunuz? (E/H)"
    if user onay == "E":
        createOrderInSystem(orderSummary)
        display "Sipariş başarılı! Sipariş numarası: " + orderId
        return TRUE
    else:
        display "Sipariş iptal edildi."
        return FALSE

// Ana akış
if not checkLogin():
    exit program  // kullanıcı giriş yapmadıysa işlem sonlandırılır

// Ürün gezinme ve sepete ekleme döngüsü
while TRUE:
    category <- listCategories()
    if category == "Çıkış" or category == NULL:
        break
    products <- listProducts(category)

    // Kullanıcı ürün seçer veya geri döner
    userAction <- prompt "Ürün ID girin veya 'geri'/'sepet'/'çıktı' yazın"
    if userAction == "çıktı":
        break
    else if userAction == "sepet":
        // Sepeti görüntüle / düzenle
        // Sepet görüntüleme ve düzenleme döngüsü
        while TRUE:
            displayCart(cart)
            display "1: Ürün miktarını değiştir, 2: Ürün sil, 3: Devam et, 4: Çık"
            choice <- user seçimi
            if choice == 1:
                pid <- prompt "Ürün ID:"
                newQty <- prompt "Yeni miktar:"
                item <- find cart item with pid
                product <- getProductById(pid)
                if newQty <= 0:
                    display "Geçersiz miktar."
                else if product.stock + item.qty >= newQty: // önceki rezerv dahil
                    // stok ayarlaması: önceki qty'yi iade et, sonra yeni qty düş
                    product.stock <- product.stock + item.qty - newQty
                    item.qty <- newQty
                    display "Miktar güncellendi."
                else:
                    display "Yetersiz stok."
            else if choice == 2:
                pid <- prompt "Silinecek Ürün ID:"
                item <- find cart item with pid
                if item != NULL:
                    product <- getProductById(p
