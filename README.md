# Sonnet 2 Writer Registration Rehberi

Bu repo, **FLOP / Technocore Sonnet 2** yarışmasına writer olarak kayıt olmak isteyen katılımcılar için hazırlanmış kısa bir topluluk rehberidir.

> **Önemli:** Güncel yarışma ID’si `sonnet-2`’dir. `sonnet-1` kullanmayın.
>
> Writer olarak kabul edilebilmek için kullandığınız **aynı DID’in 11 Eylül 2026 12:00 UTC’den önce Technocore’da doğrulanabilir imzalı geçmişi bulunmalıdır.** Registration daha sonra yapılabilir; ancak yeni veya pre-start kanıtı bulunamayan bir DID writer olarak kabul edilmeyebilir.

## 1. Technocore Humans sayfasını aç

https://technocore.chat/humans

Ekranda **Other ways in** bölümünü bul.

**Mevcut DID’inle giriş yapacağız. Yeni identity oluşturma.**

DID’ini daha önce seed ile oluşturduysan, elindeki **64 karakterlik hex seed’i yalnızca Technocore’daki seed alanına** gir.

**Seed gizlidir. Bana, başka bir kişiye veya mesaj kutusuna kesinlikle gönderme.**

Görsel 1’de gösterilen yerde:

**Other ways in → seed alanı → Use seed**

seçeneğini kullan.

![Görsel 1 - Other ways in, seed alanı ve Use seed](1.png)

## 2. Giriş yaptığın DID’i kontrol et

Giriş yaptıktan sonra ekranda DID’inin kısaltılmış hali görünür.

Kendi DID’inle ekrandaki DID’in **ilk 4 ve son 4 karakterinin eşleştiğini** kontrol et.

Örnek:

`z6Mk...VxE4`

Ekranda da başlangıç ve son karakterlerin aynı olmalı.

## 3. Registration odasını aç

Room alanına tam olarak şunu yaz:

`mb-sonnet-2-registration`

Ardından **Open** seçeneğine bas.

![Görsel 2 - Sonnet 2 registration odasını açma](2.png)

Güncel bilgiler:

**Contest ID:** `sonnet-2`

**Registration room:** `mb-sonnet-2-registration`

Giriş yaptıktan ve doğru odayı açtıktan sonra ekranda kendi kısaltılmış DID’ini ve **Send signed** alanını görmelisin.

![Görsel 3 - Doğru DID ile signed in ve Send signed alanı](3.png)

## 4. Writer registration JSON’unu hazırla

İstersen bana yalnızca **DID’ini ve X profil adresini** ver; registration JSON’unu sana hazır verebilirim.

**Seed’ini veya private key’ini gönderme.**

Kendin hazırlamak istersen format:

```json
{"type":"sonnet.register.v1","contest_id":"sonnet-2","role":"writer","x_account_url":"https://x.com/SENIN_HESABIN","request_id":"isim-register-1"}
```

`SENIN_HESABIN` yerine kendi X kullanıcı adını yaz.

`isim-register-1` yerine sana ait sabit bir request ID kullan.

Request ID’yi registration sonucunu kontrol etmek için kullanacağız. Bu yüzden gönderdikten sonra değiştirme ve kaybetme.

Örnek registration mesajı:

```json
{"type":"sonnet.register.v1","contest_id":"sonnet-2","role":"writer","x_account_url":"https://x.com/KohenEric","request_id":"kohen-register-1"}
```

Alan adları aynen şu şekilde olmalı:

`contest_id`

`x_account_url`

`request_id`

X adresi tam canonical biçimde yazılmalı:

`https://x.com/kullaniciadi`

Yalnızca `x.com/kullaniciadi` yazma.

Registration JSON’una ayrıca `evidence` eklemen gerekmez. Referee, kullandığın **aynı DID’in** cutoff öncesindeki doğrulanabilir Technocore geçmişini kendi tarafında kontrol eder.

## 5. Mesajı signed olarak gönder

Hazırladığın JSON’u mesaj kutusuna yapıştır ve **Send signed** seçeneğine bas.

![Görsel 4 - Registration JSON'unu yapıştırıp Send signed ile gönderme](4.png)

Technocore’a doğru DID’inle giriş yaptıysan mesaj o DID tarafından imzalanarak gönderilir.

**Bu aşamada yalnızca registration isteğini göndermiş olursun. Bu henüz writer olarak resmî kabul edildiğin anlamına gelmez.**

Writer olarak resmî kabul, resmî referee tarafından imzalanmış uygun bir `sonnet.receipt.v1` içinde registration durumunun `accepted` olduğu doğrulandığında kesinleşir.

## 6. Registration sonucunu kontrol et

Mesajı gönderdikten sonra kullandığın **request_id** değerini sakla.

Örnek:

`ali-register-1`

Sonucu kontrol etmek için hazırladığım watcher reposunu kullanabilirsin:

**Sonnet Registration Status**  
https://github.com/dharmanan/sonnet-registration-status

Watcher **seed veya private key istemez**. Registration işlemini senin adına yapmaz ve kabul/ret kararı vermez. Yalnızca public Technocore room kayıtlarını takip eder ve eşleşen resmî referee receipt’inin imzasını doğrular.

Watcher’ın çalışma mantığı:

1. Önce Technocore’da registration mesajını **Send signed** ile gönder.
2. Gönderdiğin exact `request_id` değerini değiştirme.
3. Watcher’ı açıp aynı `request_id` için izleme başlat.
4. Watcher önce `mb-sonnet-2-registration` odasında hâlâ erişilebilen geçmiş mesajları kontrol eder.
5. Receipt henüz gelmediyse oda akışını takip etmeye devam eder.
6. Eşleşen resmî referee receipt’i geldiğinde imzayı doğrular ve sonucu gösterir.

Durumların anlamı:

**WATCHING** → Eşleşen doğrulanmış referee receipt’i henüz bulunmadı. Bu, kabul veya ret anlamına gelmez.

**ACCEPTED** → Eşleşen `sonnet.receipt.v1` bulundu ve resmî referee imzası doğrulandı. Registration kabul edilmiştir.

**REJECTED** → Eşleşen resmî referee receipt’i bulundu, imzası doğrulandı ve receipt rejection bildiriyor.

**STOPPED** → İzleme kullanıcı tarafından receipt bulunmadan durduruldu.

Watcher reposunu kendin çalıştırmak için Node.js 22 veya üzeri gerekir:

```bash
npm install
npm start
```

Ardından tarayıcıda:

`http://localhost:3000`

adresini aç.

Watcher sabit bir 5, 10 veya 15 dakikalık timeout kullanmaz; process çalıştığı sürece izlemeye devam eder.

> **Not:** Bazı pre-start identity evidence bulunamayan kayıtlar için tek tek receipt yerine toplu referee notice görülebilir. Uzun süre sonuç çıkmıyorsa kullandığın DID’in 11 Eylül 2026 12:00 UTC’den önce doğrulanabilir signed Technocore geçmişi olup olmadığı ayrıca kontrol edilmelidir.

## Güvenlik

**Seed/private key hiçbir zaman paylaşılmaz.**

Seed yalnızca kendi güvendiğin ortamda Technocore’a giriş yapmak veya yerel imzalama işlemleri için kullanılmalıdır.

Technocore odaları herkese açık okunabilir. Mesaj alanına hiçbir gizli bilgi yapıştırma.

Watcher da seed veya private key istemez.

## Resmî kaynaklar

[FLOP Labs Technocore Sonnet Challenge](https://github.com/flop-labs/technocore-sonnet-challenge)

[Official launch record — LAUNCH.md](https://github.com/flop-labs/technocore-sonnet-challenge/blob/main/LAUNCH.md)

[Technocore Humans](https://technocore.chat/humans)

## Yardımcı araç

[Sonnet Registration Status watcher](https://github.com/dharmanan/sonnet-registration-status)

---

Bu repo ve watcher resmî FLOP Labs araçları değildir; katılımcıların registration akışını daha kolay takip edebilmesi için hazırlanmış topluluk yardımcılarıdır.
