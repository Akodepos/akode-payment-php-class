# AKÖde POS PHP Payment Gateway SDK

## Kurulum

```
src/Gateway.php dosyasını projenize dahil etmeniz yeterlidir.
```

## Kullanım
API çağrıları için aşağıdaki metodları kullanabilirsiniz.

### 3D İşlem Başlatma

```php
<?php

include "src/Gateway.php";

//### Sanal POS Üye İşyeri Ayarları
/*
 * apiUser: SMS ile iletilen ApiUser bilgisi
 * clientId: SMS ile iletilen clientId bilgisi
 * apiPass: SMS ile iletilen apiPass bilgisi
 *
 * Environment:
 *
 *  ** "LIVE" = "https://api.akodepos.com/api/Payment/"
 *  ** "TEST" = "https://ent.akodepos.com/api/Payment/"
 */

$apiUser = "POS_ENT_Test_001"; // Api kullanıcı adınız
$clientId = "1000000494"; // Api müşteri numaranız
$apiPass = "POS_ENT_Test_001!*!*"; // Api şifreniz
$environment = "https://ent.akodepos.com/api/Payment/";

//### Sipariş Bilgileri
$orderId = ""; // Sipariş numarası her sipariş için tekil olmalıdır. Boş bırakıldığında sistem tarafından otomatik üretilir
$amount = 1090; // 10 TL 90 kuruş
$instalment = 0; // Taksit sayısı - Tek çekim için 0

//### API Gateway
$gateway = new Gateway($environment, $clientId, $apiUser, $apiPass);
try {
    $payment = $gateway->threeDPayment($callback_url, $amount, $instalment, $orderId);
} catch (Exception $e) {
    print_r($e);
}

$three_d_session_id = $payment->ThreeDSessionId;
$form_post_url = $gateway->getFormUrl();
```

### 3D Ödeme Tamamlama

3D İşlem başlatma adımında oluşturulan ThreeDSessionId değeri ile form post edilir.

```html
<form role="form" method="post" action="https://payment.testdgpf.dgpaysit.com/api/Payment/ProcessCardForm">

    <input type="hidden" name="ThreeDSessionId" value="XXXXXXXX">
    <input type="text" name="CardHolderName" placeholder="İsim Soyisim" required/>
    <input type="text" name="CardNo" placeholder="Kredi Kart Numarası" required autofocus/>
    <input type="text" name="ExpireDate" placeholder="AA/YY" required/>
    <input type="text" name="Cvv" placeholder="CVV" required/>

    <button type="submit">3D ile Ödemeyi Tamamla</button>


</form>
```

### Ortak Ödeme (iframe)

```php
<?php

include "src/Gateway.php";

//### Sanal POS Üye İşyeri Ayarları
/*
 * apiUser: SMS ile iletilen ApiUser bilgisi
 * clientId: SMS ile iletilen clientId bilgisi
 * apiPass: SMS ile iletilen apiPass bilgisi
 *
 * Environment:
 *
 *  ** "LIVE" = "https://api.akodepos.com/api/Payment/"
 *  ** "TEST" = "https://ent.akodepos.com/api/Payment/"
 */

$apiUser = "POS_ENT_Test_001"; // Api kullanıcı adınız
$clientId = "1000000494"; // Api müşteri numaranız
$apiPass = "POS_ENT_Test_001!*!*"; // Api şifreniz
$environment = "https://ent.akodepos.com/api/Payment/";

//### Sipariş Bilgileri
$orderId = ""; // Sipariş numarası her sipariş için tekil olmalıdır. Boş bırakıldığında sistem tarafından otomatik üretilir
$amount = 1090; // 10 TL 90 kuruş
$instalment = 0; // Taksit sayısı - Tek çekim için 0

//### API Gateway
$gateway = new Gateway($environment, $clientId, $apiUser, $apiPass);
try {
    $payment = $gateway->startPaymentThreeDSession($callback_url, $amount, $instalment, $orderId);
} catch (Exception $e) {
    print_r($e);
}

$iframe_url = $gateway->getFrameUrl($payment->ThreeDSessionId);
```

```html
<iframe src="<?php echo $iframe_url ?>" style=" width:100%; height: 550px;position: relative;" frameborder="0" allowfullscreen></iframe>
```


### Ödeme Sorgulama

```php
<?php

include "src/Gateway.php";

//### Sanal POS Üye İşyeri Ayarları
/*
 * apiUser: SMS ile iletilen ApiUser bilgisi
 * clientId: SMS ile iletilen clientId bilgisi
 * apiPass: SMS ile iletilen apiPass bilgisi
 *
 * Environment:
 *
 *  ** "LIVE" = "https://api.akodepos.com/api/Payment/"
 *  ** "TEST" = "https://ent.akodepos.com/api/Payment/"
 */

$apiUser = "POS_ENT_Test_001"; // Api kullanıcı adınız
$clientId = "1000000494"; // Api müşteri numaranız
$apiPass = "POS_ENT_Test_001!*!*"; // Api şifreniz
$environment = "https://ent.akodepos.com/api/Payment/";

//### Sipariş Bilgileri
$orderId = "20221011999"; // Sipariş numarası

//### API Gateway
$gateway = new Gateway($environment, $clientId, $apiUser, $apiPass);
$paymentCheck = $gateway->inquiry($orderId);

print_r($paymentCheck);
```

### İşlem Listeleme

```php
<?php

include "src/Gateway.php";

//### Sanal POS Üye İşyeri Ayarları
/*
 * apiUser: SMS ile iletilen ApiUser bilgisi
 * clientId: SMS ile iletilen clientId bilgisi
 * apiPass: SMS ile iletilen apiPass bilgisi
 *
 * Environment:
 *
 *  ** "LIVE" = "https://api.akodepos.com/api/Payment/"
 *  ** "TEST" = "https://ent.akodepos.com/api/Payment/"
 */

$apiUser = "POS_ENT_Test_001"; // Api kullanıcı adınız
$clientId = "1000000494"; // Api müşteri numaranız
$apiPass = "POS_ENT_Test_001!*!*"; // Api şifreniz
$environment = "https://ent.akodepos.com/api/Payment/";

//### API Gateway
$gateway = new Gateway($environment, $clientId, $apiUser, $apiPass);

/*
 * Sipariş numarası dolu gönderildiğinde o siparişe ait tüm işlemleri listeler.
 * Sipariş numarası boş gönderildiğinde o tarihteki tüm siparişleri listeler
*/
$orderId = "20221011999";

/*
 * Sipariş tarihi integer olmalıdır.
 * Listesi istenen tarih YYYYAAGG formatında yazılmalıdır.
*/
$date = 20221011;

try {
    $paymentCheck = $gateway->history($date, $orderId, $page = 1, $pageSize = 10);
} catch (Exception $e) {
    print_r($e);
}

print_r($paymentCheck);
```

### İptal

```php
<?php

include "src/Gateway.php";

//### Sanal POS Üye İşyeri Ayarları
/*
 * apiUser: SMS ile iletilen ApiUser bilgisi
 * clientId: SMS ile iletilen clientId bilgisi
 * apiPass: SMS ile iletilen apiPass bilgisi
 *
 * Environment:
 *
 *  ** "LIVE" = "https://api.akodepos.com/api/Payment/"
 *  ** "TEST" = "https://ent.akodepos.com/api/Payment/"
 */

$apiUser = "POS_ENT_Test_001"; // Api kullanıcı adınız
$clientId = "1000000494"; // Api müşteri numaranız
$apiPass = "POS_ENT_Test_001!*!*"; // Api şifreniz
$environment = "https://ent.akodepos.com/api/Payment/";

//### Sipariş Bilgileri
$orderId = "20221011999"; // Sipariş numarası

//### API Gateway
$gateway = new Gateway($environment, $clientId, $apiUser, $apiPass);
$paymentCheck = $gateway->void($orderId);

print_r($paymentCheck);
```

### İade
```php
<?php

include "src/Gateway.php";

//### Sanal POS Üye İşyeri Ayarları
/*
 * apiUser: SMS ile iletilen ApiUser bilgisi
 * clientId: SMS ile iletilen clientId bilgisi
 * apiPass: SMS ile iletilen apiPass bilgisi
 *
 * Environment:
 *
 *  ** "LIVE" = "https://api.akodepos.com/api/Payment/"
 *  ** "TEST" = "https://ent.akodepos.com/api/Payment/"
 */

$apiUser = "POS_ENT_Test_001"; // Api kullanıcı adınız
$clientId = "1000000494"; // Api müşteri numaranız
$apiPass = "POS_ENT_Test_001!*!*"; // Api şifreniz
$environment = "https://ent.akodepos.com/api/Payment/";

//### Sipariş Bilgileri
$orderId = "202210109"; // Sipariş numarası
$amount=125; // İade edilecek tutar. 1 TL 25 Kuruş

//### API Gateway
$gateway = new Gateway($environment, $clientId, $apiUser, $apiPass);
$paymentCheck = $gateway->refund($orderId, $amount);

print_r($paymentCheck);
```

### 3D PreAuth
```php
<?php

include "src/Gateway.php";

//### Sanal POS Üye İşyeri Ayarları
/*
 * apiUser: SMS ile iletilen ApiUser bilgisi
 * clientId: SMS ile iletilen clientId bilgisi
 * apiPass: SMS ile iletilen apiPass bilgisi
 *
 * Environment:
 *
 *  ** "LIVE" = "https://api.akodepos.com/api/Payment/"
 *  ** "TEST" = "https://ent.akodepos.com/api/Payment/"
 */

$apiUser = "POS_ENT_Test_001"; // Api kullanıcı adınız
$clientId = "1000000494"; // Api müşteri numaranız
$apiPass = "POS_ENT_Test_001!*!*"; // Api şifreniz
$environment = "https://ent.akodepos.com/api/Payment/";

//### API Gateway
$gateway = new Gateway($environment, $clientId, $apiUser, $apiPass);

//### Sipariş Bilgileri
$amount=24990; // 249 TL 90 Kuruş
$installment=0; // Tek çekim
$orderId=""; // Sipariş numarası - Boş gönderildiğinde sistem tarafından otomatik üretilir
$description=""; // Opsiyonel sipariş açıklaması
$callback_url = "https://www.orneksitesadresiniz.com/payment-response.php";

try {
    $preAuth = $gateway->threeDPreAuth($callbackUrl, $amount, $installment, $orderId, $description);
    $ThreeDSessionId = $preAuth->ThreeDSessionId;

    print_r($preAuth);

} catch (Exception $e) {
    print_r($e);
}
```

### 3D PostAuth
```php
<?php

include "src/Gateway.php";

//### Sanal POS Üye İşyeri Ayarları
/*
 * apiUser: SMS ile iletilen ApiUser bilgisi
 * clientId: SMS ile iletilen clientId bilgisi
 * apiPass: SMS ile iletilen apiPass bilgisi
 *
 * Environment:
 *
 *  ** "LIVE" = "https://api.akodepos.com/api/Payment/"
 *  ** "TEST" = "https://ent.akodepos.com/api/Payment/"
 */

$apiUser = "POS_ENT_Test_001"; // Api kullanıcı adınız
$clientId = "1000000494"; // Api müşteri numaranız
$apiPass = "POS_ENT_Test_001!*!*"; // Api şifreniz
$environment = "https://ent.akodepos.com/api/Payment/";

//### API Gateway
$gateway = new Gateway($environment, $clientId, $apiUser, $apiPass);

//### Sipariş Bilgileri
$amount=14990; // Otorizasyondan tahsil edilcek tutar. 149 TL 90 Kuruş
$orderId=""; // Sipariş numarası

try {
    $postAuth = $gateway->threeDPostAuth( $orderId, $amount );
    print_r($postAuth);

} catch (Exception $e) {
    print_r($e);
}
```
