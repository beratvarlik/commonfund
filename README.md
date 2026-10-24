# CommonFund

Arkadaş grupları için ortak kasa. Her tur herkes eşit taksit yatırır, kurayla biri havuzu alır; herkes bir kez alana kadar sürer. Para Monad deneme ağındaki bir akıllı sözleşmede durur.

> Kurayı da kasayı da şirket değil, kod yönetsin.

## Klasörler

```
commonfund/
├─ contracts/   Kasa sözleşmesi (Solidity + Hardhat) ve testleri
└─ web/         Web uygulaması (Next.js) ve yardımcılar (Kâtip, Bekçi, Muhtar)
```

## Yardımcılar

| Yardımcı | Ne yapar | Zincirdeki yetkisi |
|---|---|---|
| **Kâtip** | Sohbetten grup kurallarını çıkarır, özeti onaya sunar. | Yok. Grubu kullanıcı kendi cüzdanıyla kurar. |
| **Bekçi** | Son ödeme günü geçince gecikmeyi işaretler, mesaj bırakır, mazereti Muhtar'a iletir. Süre dolunca herkesin çağırabildiği kuralı çalıştırır. | Yalnızca `flagLate`. Paraya dokunamaz. |
| **Muhtar** | Mazereti okur; süre verir ya da teminattan bir taksit keser. Gerekçesini zincire yazar. | Yalnızca `grantExtension` ve `slashCollateral`; yalnızca mazeret yazmış ve ödememiş üye için. |

Yardımcılar ücretsiz bir yapay zekâ modeliyle çalışır (varsayılan: Groq, `llama-3.3-70b-versatile`). Anahtar girilmezse ya da model cevap veremezse kurallı yedek cevaplar devreye girer; demo yine akar.

## Kurulum

Gerekenler: Node.js 20+, MetaMask (ya da başka bir tarayıcı cüzdanı), deneme ağı MON'u.
Deneme ağı bilgileri ve musluk (faucet): https://docs.monad.xyz/developer-essentials/testnets

### 1. Sözleşme

```bash
cd contracts
npm install
copy .env.example .env        # macOS/Linux: cp .env.example .env
```

`.env` dosyasını doldur:

- `DEPLOYER_PRIVATE_KEY`: sözleşmeyi kuracak cüzdanın özel anahtarı
- `MUHTAR_ADDRESS`, `BEKCI_ADDRESS`: yardımcıların cüzdan adresleri (boş bırakırsan kuran cüzdan kullanılır)

```bash
npm test                # 14 test
npm run deploy:monad    # adres web/src/lib/deployment.json dosyasına yazılır
```

Sözleşmeyi değiştirirsen ABI'yi yeniden üret: `npm run compile && npm run export-abi`

### 2. Web uygulaması

```bash
cd web
npm install
copy .env.example .env.local   # macOS/Linux: cp .env.example .env.local
```

`.env.local` dosyasını doldur:

- `MUHTAR_PRIVATE_KEY`, `BEKCI_PRIVATE_KEY`: 1. adımdaki `MUHTAR_ADDRESS` ve `BEKCI_ADDRESS` adreslerine ait anahtarlar. İkisinde de işlem ücreti için biraz MON olmalı.
- `LLM_API_KEY`: ücretsiz Groq anahtarı (https://console.groq.com/keys). OpenRouter ya da Gemini kullanmak için `.env.example` içindeki alternatiflere bak.

```bash
npm run dev     # http://localhost:3000
```

## Demo akışı (yaklaşık 15 dakika)

MetaMask'ta 3 hesap aç, her birine biraz MON koy. Kâtip'e "3 kişi, ayda 0,01 MON" yaz ve **Demo hızı** takvimini seç (1 tur = 10 dakika, son ödeme 5 dakika, Muhtar en fazla 3 dakika süre verebilir).

1. **Grup kur**: Kâtip özeti çıkarır, "Onayla ve grubu kur" ile teminat yatırılır. Davet kodunu kopyala.
2. **Katıl**: diğer iki hesapla `/katil?kod=…` bağlantısından teminatı yatırıp katıl. Üçüncü katılımla 1. tur başlar.
3. **Taksit**: iki hesap taksidini öder, biri ödemez.
4. **Gecikme**: 5 dakika sonra panel açıkken Bekçi gecikmeyi işaretler ve mesaj bırakır.
5. **Mazeret ve karar**: geciken hesapla Mazeret sayfasında mazeret yaz. Muhtar okur, karar verir, gerekçesi zincire yazılır.
6. **Kura**: herkesin taksiti gelince Kura sayfasında "Kura çek". Halka döner, havuz kazanana akar.
7. **Doğrulama**: kasa adresi, Muhtar'ın yetkileri ve tüm işlem kayıtları explorer bağlantılarıyla.

## Kurallar (sözleşmede sabit)

- Taksit ve teminat MON olarak yatırılır; tutarlar ve tarihler grup kurulunca değişmez.
- Herkesin taksiti gelmeden kura çekilemez. Kurayı herkes çağırabilir; havuz daha önce almamış üyelerden birine gider.
- Son ödeme günü geçerse üye mazeret yazabilir. Muhtar en fazla `maxExtension` kadar süre verebilir ya da teminattan bir taksit tutarı kesebilir.
- Mazeret gelmezse ya da verilen süre dolarsa taksit kural gereği teminattan karşılanır (herkes çağırabilir; Bekçi bunu otomatik yapar).
- Grup bitince kalan teminat sahibine geri döner.

## Bilinen sınırlar

- Kura, deneme ağı için `block.prevrandao` ile çekilir. Gerçek parayla çalışacak bir sürümde doğrulanabilir rastgelelik (VRF) kullanılmalı.
- Bekçi şimdilik bir grup paneli açıkken çalışır (sayfa 25 saniyede bir `/api/bekci` çağırır). Yayına alırken bunu zamanlanmış bir göreve (ör. Vercel Cron) bağlamak yeterli.
- Muhtar ve Bekçi anahtarları sunucuda durur; `.env.local` dosyasını paylaşma.
