<div align="center">

# 🪙 CommonFund

**Kurayı da kasayı da şirket değil, kod yönetsin.**
<img width="1835" height="892" alt="image" src="https://github.com/user-attachments/assets/0f7bb8b8-f27a-4c6e-8194-2a833e34c2d5" />
<img width="1856" height="870" alt="image" src="https://github.com/user-attachments/assets/baf74cc6-cdac-456b-9213-b59d37781b2e" />
<img width="1832" height="888" alt="image" src="https://github.com/user-attachments/assets/2142be64-5392-4dce-9d15-a1764b23f5b3" />



Arkadaş grupları için faizsiz, şeffaf ve yapay zekâ hakemli ortak birikim sistemi.
Monad üzerinde çalışır.

![Monad](https://img.shields.io/badge/Monad-testnet-6E54FF)
![Solidity](https://img.shields.io/badge/Solidity-0.8.x-363636)
![Next.js](https://img.shields.io/badge/Next.js-TypeScript-000000)
![Status](https://img.shields.io/badge/durum-hackathon%20demosu-4F46E5)

[🌐 Canlı demo](https://commonfundlive.vercel.app) · [📜 Kontrat](https://testnet.monadexplorer.com/address/0xC219dcE9A1EBc700a292f4b8EF03b1C0b5264FEb) · [🎥 Demo videosu](<VIDEO_LINKI>)

<img src="docs/cover.png" alt="CommonFund ekran görüntüsü" width="720">

</div>

---

## 💡 Sorun

Ortak birikim sistemlerinde parayı bir kişi ya da kurum tutar. Kura sonucu, teminat kesintisi ve ödeme kararları insan iradesine bağlıdır, yani herkes birine güvenmek zorundadır.

- **Parayı kim tutacak?** Havuzu tutan kişiye ya da kuruma güvenmek zorundasın.
- **Biri ödemeyi bırakırsa ne olacak?** Parayı alan kaçarsa kalanlar mağdur olur.
- **Kura gerçekten adil mi?** Sonucu herkes göremez, itiraz edemez.

## ✅ Çözüm

CommonFund’da havuz, teminat kilidi, kura ve teminat kesintisi Monad üzerindeki akıllı kontratta çalışır. Sistemi üç yapay zekâ agent’ı işletir: Muhtar mazeretleri okuyup “süre ver” ya da “teminatı kes” kararını verir, Kâtip doğal dildeki isteği kontrat parametrelerine çevirir, Bekçi gecikmeleri izler. Agent’ların kendi cüzdanı var, ama yetkilerinin sınırını kontrat çizer ve hiçbiri havuzdan para çekemez.

1. Üyeler gruba katılırken **teminat** kilitler.
2. Her ay herkes **eşit taksit** yatırır.
3. Her ay havuzu **kura ile bir üye** alır (daha önce almamış olanlar arasından).
4. Parayı alıp ödemeyi bırakanın **teminatı otomatik kesilir**.
5. Üç yapay zekâ agent'ı sistemi yönetir, ama **parayı hiçbiri çekemez**.

## ⚙️ Nasıl çalışır?

```mermaid
flowchart LR
    A[Grup kurulur] --> B[Üyeler teminat yatırır]
    B --> C[Aylık taksit ödenir]
    C --> D{Herkes ödedi mi?}
    D -- Evet --> E[Kura çekilir]
    D -- Hayır --> F[Mazeret bildirilir]
    F --> G[Muhtar karar verir]
    G -- Süre ver --> C
    G -- Teminatı kes --> E
    E --> H[Kazanan havuzu alır]
    H --> C
```

## 🤖 Agent'lar

| Agent | Rolü | Neyi yapamaz? |
|---|---|---|
| **Muhtar** | Hakem. Mazereti okur, "süre ver" veya "teminatı kes" kararını verir. | Zamanında ödeyen birine ceza veremez, havuzdan para çekemez. |
| **Kâtip** | Doğal dildeki isteği (ör. "5 kişi, ayda 10 token") kontrat parametrelerine çevirir, durumu özetler. | Hiçbir işlemi kendisi imzalayamaz. |
| **Bekçi** | Gecikenleri izler ve uyarır. | Yalnızca okur ve bildirir. |

> Agent'lara karar yetkisi veriyoruz, ama **sınırları kontrat çiziyor**.

## 🔗 Neden blockchain olmasa olmazdı?

| Geleneksel sistem | CommonFund |
|---|---|
| Parayı bir kişi veya şirket tutar | Parayı kontrat tutar, kimse tek başına çekemez |
| Kura sonucuna güvenmek gerekir | Sonuç zincirde herkesin görebildiği bir işlemdir |
| Teminat kesintisi insan kararına bağlıdır | Kesinti kod tarafından, kurala göre uygulanır |
| Yapay zekâya hesap açılamaz | Agent'ın kendi cüzdanı var, yetkisi kodda yazılı |

## 🧱 Mimari

```
┌──────────────┐     ┌────────────────┐     ┌───────────────────┐
│  Next.js UI  │ ──► │  Agent API     │ ──► │  Monad testnet    │
│  (wagmi)     │     │  (Muhtar,      │     │  CommonFund.sol   │
│              │ ◄── │   Kâtip,Bekçi) │ ◄── │                   │
└──────────────┘     └────────────────┘     └───────────────────┘
```

## 🛠️ Teknoloji

- **Ağ:** Monad testnet (chain id `10143`)
- **Kontrat:** Solidity 0.8.x
- **Arayüz:** Next.js, TypeScript, wagmi, viem
- **Agent'lar:** <KULLANDIĞIN MODEL/API>

## 🧪 Jüri için: sistemi dene

1. [Canlı demoyu](<DEMO_LINKI>) aç.
2. **"Demoyu dene"** düğmesine bas. Hazır bir grup açılır (5 demo cüzdanı).
3. Cüzdanlar arasında geçiş yap ("Ayşe olarak devam et" gibi), taksit öde, kura çek.
4. Bir üye ile ödemeyi bırakıp **mazeret yaz**, Muhtar'ın kararını izle.
5. **Doğrulama** sayfasından kontrat adresini ve işlemleri explorer'da kontrol et.

> Demo için dönem süresi kısaltılmıştır. Demo cüzdanları yalnızca testnet içindir, gerçek varlık taşımaz.

## 🚀 Yerelde çalıştırma

```bash
git clone <REPO_LINKI>
cd <KLASÖR_ADI>
npm install
cp .env.example .env.local   # değerleri doldur
npm run dev
```

`.env.local` içinde şunlar gerekir:

| Değişken | Açıklama |
|---|---|
| `NEXT_PUBLIC_CONTRACT_ADDRESS` | Deploy edilen kontrat adresi |
| `AGENT_API_KEY` | Agent'lar için model API anahtarı |
| `ARBITER_PRIVATE_KEY` | Muhtar'ın testnet cüzdan anahtarı (**gerçek varlık koyma**) |

## ⚠️ Bilinen sınırlar

Dürüst olmak gerekirse bu bir hackathon prototipi:

- Kura rastgeleliği demo için basit bir yöntemle üretiliyor. Gerçek ürün için doğrulanabilir rastgelelik gerekir.
- Muhtar kararları bugün tek bir hakem adresine bağlı. Sonraki adım itiraz ve çoklu hakem mekanizması.
- Yalnızca testnet'te çalışır, gerçek para veya konut finansmanı vaat etmez.


## 👥 Takım

| Berat Varlık | Full Stack Developer|
|---|---|
| <İsim> | <Rol> |

