# XenForoPostDownloader — Geliştirilmiş Fork

Bu repo, [SkyCloudDev/ForumPostDownloader](https://github.com/SkyCloudDev/ForumPostDownloader)'ın
fork'udur. Orijinal script SkyCloudDev'e aittir (WTFPL lisansı); buradaki sürüm üzerine
bug fix'ler ve yeni özellikler eklenmiştir. Emeği için orijinal geliştiriciye teşekkürler. 🙏

## Bu fork'ta neler var?

- **🡳 Thread Downloader (v2.2):** tek tıkla başlığın **tüm sayfalarını** indir;
  çoklu başlık kuyruğu, sayfa aralığı seçimi (`URL | 5-30`), kaldığı yerden devam
  (tarayıcı çökse bile), temiz durdurma
- **Imgur desteği:** direkt görseller + tekil sayfalar (`i.imgur.com/...`, `imgur.com/abc`)
- **4 bug fix:** DIRECT indirmede çökme (`h.extension`), jpg.church sabit şifre hatası,
  modern Firefox tespiti (FF128+), `h.unique` içindeki tanımsız değişken

Değişikliklerin tam listesi ve teknik detaylar için bu dosyanın devamına bak.

## Kurulum

1. Tarayıcıya [Tampermonkey](https://www.tampermonkey.net/) kur
2. Script'i kur: **[build.user.js — buraya tıkla](../../raw/main/dist/build.user.js)**
   (veya dosyayı açıp tamamını Tampermonkey'de yeni script olarak yapıştır)

Zaten orijinal script kuruluysa: Tampermonkey → script'i aç → Düzenle →
**tümünü sil → bu fork'taki `build.user.js`'nin tamamını yapıştır → kaydet.**

---

> ## ⚡ GÜNCEL DURUM (2026-06-09, v2)
> **`build.user.js` artık script'in TAM ve güncel hali** — aşağıdaki tüm değişiklikler
> (bug fix'ler + imgur + thread downloader v2) içinde uygulanmış durumda.
> Kurulum: Tampermonkey → script'i aç → Düzenle → **tümünü sil → build.user.js'nin
> tamamını yapıştır → kaydet.** Aşağıdaki bul-değiştir adımlarını tek tek uygulamana gerek YOK.
>
> ### Thread Downloader v2 — yenilikler
> - **Çoklu başlık kuyruğu:** butona basınca açılan kutuya satır başına bir thread URL'si
>   yapıştır; hepsi sırayla, tüm sayfalarıyla iner. İsteğe bağlı aralık: `URL | 5-30`
> - **20 sayfa parça limiti KALDIRILDI** — başlık kaç sayfaysa hepsini gezer
> - **Sayfalar arası 3-5 sn rastgele bekleme** (orijinal script davranışı), başlıklar arası 8 sn
> - **ZIP geri AÇIK:** her post `Başlık/Başlık #post.zip` olarak klasöre iner
>   (alt klasör yazılamazsa otomatik düz isme düşer — aşağıdaki Tampermonkey notuna bak)
> - **Durdurma:** indirme sürerken aynı butona bas → sıradaki post sınırında temiz durur
> - Takılan post 4 dk sonra beklenmez ama arka planda sürer; zip'i bitince yine de iner
> - **Kaldığı yerden devam (v2.1):** ilerleme her post/sayfa sınırında Tampermonkey
>   deposuna kaydedilir — tarayıcı çökse/kapansa bile kalır. Butona tekrar basınca
>   modalda **"▶ Devam et"** bandı çıkar: kuyruğun kaldığı başlık + sayfa + post'tan sürer.
>   "Durdur" da kaydı korur; iş bittiğinde kayıt otomatik silinir. 🗑 ile elle silinebilir.
> - **Devam bandı detaylı (v2.2, 2026-06-11):** "▶ Devam et" bandı artık devam edilecek
>   **başlığın adını + URL'sini + sayfasını** ve ardından sırada bekleyen başlıkları gösterir.
>   ÖNEMLİ: kayıt başlık başına değil GLOBALDİR — hangi sayfada olursan ol "Devam et"
>   her zaman en son yarım kalan kuyruğu sürdürür, o an açık olan başlıkla ilgisi yoktur.
> - **Başlat koruması (v2.2):** yarım kalan kayıt varken "Başlat"a basılırsa onay sorar
>   (yeni iş eski kaydı kalıcı siler — eskiden sessizce eziliyordu, tek başlık indiriyor sanılıyordu).
>
> ### ⚠️ Zen/Firefox OOM uyarısı (tarayıcının "kapanması")
> İndirme sırasında `/tmp` doluyordu — CachyOS'ta `/tmp` RAM tabanlı (tmpfs, ~31G).
> Tarayıcı indirmeleri RAM'i şişirip OOM killer'a yakalanabiliyor. Önlem:
> `about:config` → `browser.download.start_downloads_in_tmp_dir` → **false**
> (indirmeler /tmp yerine doğrudan hedef klasöre .part olarak yazılır).
>
> ### ⚠️ Klasör altına inmesi için Tampermonkey ayarı
> Tampermonkey → Ayarlar → Genel → Yapılandırma modu: **Gelişmiş** →
> İndirmeler **BETA** → İndirme Modu: **Tarayıcı API'si (Browser API)**.
> "Native" modda alt klasör yazılamaz; script o durumda zip'leri düz isimle
> (`Başlık #post.zip`) indirmeye devam eder, hiçbir şey kaybolmaz.

---

## 1) BUG FIX'ler (bul → değiştir)

### Fix 1 — `h.extension` diye fonksiyon yok (DIRECT indirmede çökme)
**BUL:**
```js
                            const ext2 = h.extension(basename);
```
**DEĞİŞTİR:**
```js
                            const ext2 = h.ext(basename);
```

### Fix 2 — jpg.church'te sabit `'ramona'` şifresi gerçek şifreleri eziyor
**BUL:**
```js
                let authenticated = false;

                spoilers = ['ramona'];

                for (const spoiler of spoilers) {
```
**DEĞİŞTİR:**
```js
                let authenticated = false;

                for (const spoiler of spoilers) {
```

### Fix 3 — modern Firefox'ta `isFF` bozuk (InstallTrigger FF128'de kaldırıldı)
**BUL:**
```js
window.isFF = typeof InstallTrigger !== 'undefined';
```
**DEĞİŞTİR:**
```js
window.isFF = /firefox|fxios/i.test(navigator.userAgent) || typeof InstallTrigger !== 'undefined';
```

### Fix 4 — `h.unique(items, cb)` içinde tanımsız `byKey`
**BUL:**
```js
    unique: (items, cb) => {
        if (cb) {
            return items.reduce((acc, item) => (!acc.find(i => i[byKey] === item[byKey]) ? acc.concat(item) : acc), []);
        }

        return items.reduce((acc, item) => (acc.indexOf(item) < 0 ? acc.concat(item) : acc), []);
    },
```
**DEĞİŞTİR:**
```js
    unique: (items, cb) => {
        if (cb) {
            const key = typeof cb === 'function' ? cb : (i => i[cb]);
            const seen = new Set();
            return items.reduce((acc, item) => {
                const k = key(item);
                if (seen.has(k)) return acc;
                seen.add(k);
                return acc.concat(item);
            }, []);
        }

        return items.reduce((acc, item) => (acc.indexOf(item) < 0 ? acc.concat(item) : acc), []);
    },
```

---

## 2) IMGUR desteği (örnek host ekleme)

### 2a) @connect satırları
Metadata bloğunda (`// @connect ...` satırlarının arasına) ekle:
```js
// @connect imgur.com
// @connect i.imgur.com
// @connect imgur.io
```

### 2b) `hosts` dizisine bir satır
`const hosts = [ ... ]` dizisinin içine (ör. `['Twitter:image', ...]` satırının altına) ekle:
```js
    ['Imgur:image', [/(i\.)?imgur\.(com|io)\/(?!a\/|gallery\/|t\/)[a-zA-Z0-9]{5,}(\.[a-z0-9]{2,4})?/]],
```
> Not: `a/` ve `gallery/` (albümler) bilerek dışarıda — imgur albümleri JS/API ister.
> Direkt görsel (`i.imgur.com/abc.jpg`) ve tekil sayfa (`imgur.com/abc`) desteklenir;
> forum postlarındaki imgur linklerinin %90'ı bu ikisidir.

### 2c) `resolvers` dizisine bir blok
`const resolvers = [ ... ]` dizisinin içine ekle (ör. twitter resolver'ının yanına):
```js
    [
        [/(i\.)?imgur\.(com|io)\//],
        async (url, http) => {
            url = String(url || '').replace(/^http:/i, 'https:');
            // Direkt CDN görseli -> olduğu gibi (query'yi at)
            if (/i\.imgur\.(com|io)\//i.test(url)) {
                return url.replace(/\?.*$/, '');
            }
            // imgur.com/<id> tekil sayfa -> og:image'dan gerçek görsel
            try {
                const { dom } = await http.get(url);
                const og =
                    dom?.querySelector('meta[property="og:image"]')?.getAttribute('content') ||
                    dom?.querySelector('link[rel="image_src"]')?.getAttribute('href');
                if (og) return og.replace(/\?.*$/, '').replace(/^http:/i, 'https:');
            } catch (e) {}
            return url;
        },
    ],
```

---

## 3) HOST ekleme/çıkarma mantığı (kendin eklemek için)

Mimari zaten modüler. Yeni host eklemek için **3 yer**:

1. **`// @connect <domain>`** — Tampermonkey'in o domaine istek atmasına izin (yoksa engellenir).
2. **`hosts` dizisi** — postta o host'un linkini bulan regex:
   ```js
   ['Görünenİsim:kategori', [tekil_regex /*, albüm_regex (opsiyonel) */]]
   ```
   - `kategori` `image` ise dosyalar `Images/`, `video` ise `Videos/` altına gider.
3. **`resolvers` dizisi** — o linki gerçek indirme URL'sine çeviren fonksiyon:
   ```js
   [[eşleşme_regex], async (url, http) => /* direkt url döndür */ url]
   ```
   - En basiti: linki olduğu gibi döndüren passthrough (imgur direkt linki gibi).
   - `:!` öneki "bu kalıba UYMAsın" demek (albümü tekil resolver'dan ayırmak için).

**Çıkarmak için:** `hosts`'tan ilgili satırı sil (resolver kalsa da zararsız).

Kolayca eklenebilecek bilindik host'lar (hepsi passthrough/og:image mantığıyla):
`catbox.moe`, `files.catbox.moe`, `litterbox`, `imgchest.com`, `saint`, `dropbox` (dl=1), `streamable`.
İstersen bunları da yazarım.

---

## 4) THREAD DOWNLOADER (tüm sayfalar, 20'şer parça)

### 4a) Fonksiyon tanımları
Aşağıdaki bloğu, script'in sonlarındaki **`const parsedPosts = [];`** satırının
**HEMEN ÜSTÜNE** yapıştır (orada `addDownloadPageButton`, `registerPostReaction`,
`downloadPost`, `parsers`, `hosts`, `resolvers`, `h`, `ui` zaten tanımlı = kapsam doğru):

```js
/* =========================================================================
 *  THREAD DOWNLOADER — tüm sayfalar, 20'şer parça (chunked)
 * ========================================================================= */
const XFPD_THREAD_CHUNK = 20;            // tek seferde en fazla sayfa
const XFPD_THREAD_PAGE_DELAY_MS = 1800;  // sayfalar arası bekleme (CF/rate-limit'e karşı)

const addDownloadThreadButton = () => {
    const btn = document.createElement('a');
    btn.setAttribute('id', 'download-thread');
    btn.setAttribute('href', '#');
    btn.setAttribute('class', 'button--link button rippleButton');

    const span = document.createElement('span');
    span.setAttribute('class', 'button-text');
    span.innerText = '🡳 Download Thread';
    btn.appendChild(span);

    const buttonGroup = h.element('.buttonGroup');
    if (buttonGroup) buttonGroup.prepend(btn);
    else (document.querySelector('.p-body-header') || document.body).prepend(btn);
    return btn;
};

const makeThreadStatusUI = () => {
    const { el: statusEl } = ui.labels.status.createStatusLabel();
    const filePB = ui.pBars.createFileProgressBar();
    const totalPB = ui.pBars.createTotalProgressBar();

    const wrap = document.createElement('div');
    wrap.style.margin = '8px 0';
    wrap.appendChild(statusEl);
    wrap.appendChild(filePB);
    wrap.appendChild(totalPB);

    const anchor = h.element('.buttonGroup');
    if (anchor && anchor.parentNode) anchor.parentNode.insertBefore(wrap, anchor);
    else (document.querySelector('.p-body-main') || document.body).prepend(wrap);

    h.hide(statusEl); h.hide(filePB); h.hide(totalPB);
    return { status: statusEl, filePB, totalPB };
};

const xfpdDetectLastPage = (dom = document) => {
    let last = 1;
    try {
        const links = [...dom.querySelectorAll('.pageNav-main a, .pageNav a, a.pageNav-page, .pageNavSimple a')];
        for (const a of links) {
            const m = /\/page-(\d+)/i.exec(a.getAttribute('href') || '');
            if (m) last = Math.max(last, Number(m[1]));
        }
    } catch (e) {}
    return last;
};

const downloadThread = async (statusUI) => {
    // Başlığın kök URL'i (mevcut /page-N'i temizle)
    const threadBase = location.href
        .split('#')[0].split('?')[0]
        .replace(/\/page-\d+\/?$/i, '')
        .replace(/\/$/, '');

    const lastPage = xfpdDetectLastPage(document);

    const def = `1-${Math.min(XFPD_THREAD_CHUNK, lastPage)}`;
    const input = window.prompt(
        `Bu başlık ~${lastPage} sayfa.\nİndirilecek sayfa aralığı (tek seferde en fazla ${XFPD_THREAD_CHUNK} sayfa):`,
        def,
    );
    if (!input) return;

    let a, b;
    const mm = /^\s*(\d+)\s*-\s*(\d+)\s*$/.exec(input);
    if (mm) { a = Number(mm[1]); b = Number(mm[2]); }
    else if (/^\s*\d+\s*$/.test(input)) { a = b = Number(input.trim()); }
    else { alert('Geçersiz aralık. Örnek: 1-20'); return; }

    if (a < 1) a = 1;
    if (b < a) { alert('Geçersiz aralık (bitiş < başlangıç).'); return; }
    if (b - a + 1 > XFPD_THREAD_CHUNK) {
        b = a + XFPD_THREAD_CHUNK - 1;
        alert(`Tek seferde en fazla ${XFPD_THREAD_CHUNK} sayfa. Aralık ${a}-${b} olarak sınırlandı.`);
    }

    // Her post kendi ZIP'i olarak iner (bellek güvenli). Tümü tek ZIP YAPMA.
    const threadSettings = {
        zipped: true,
        flatten: false,
        generateLinks: false,
        generateLog: false,
        skipDuplicates: true,
        skipDownload: false,
        verifyBunkrLinks: false,
        output: [],
    };
    const getEnabledHostsCB = hs => hs.filter(x => x.enabled);

    h.show(statusUI.status);
    let donePosts = 0, skippedPages = 0;

    for (let p = a; p <= b; p++) {
        const pageUrl = p === 1 ? threadBase : `${threadBase}/page-${p}`;

        h.ui.setElProps(statusUI.status, { color: '#469cf3', fontWeight: 'bold' });
        h.ui.setText(statusUI.status, `📄 Sayfa ${p}/${b} çekiliyor...`);

        // Sayfayı arka planda çek (tarayıcı navigasyonu YOK, GM_xmlhttpRequest)
        let dom = null;
        try { ({ dom } = await h.http.get(pageUrl)); } catch (e) { dom = null; }
        if (!dom) {
            skippedPages++;
            log.write('thread', `[THREAD] Sayfa cekilemedi: ${pageUrl}`, 'ERROR');
            if (p < b) await h.delayedResolve(XFPD_THREAD_PAGE_DELAY_MS);
            continue;
        }

        const postNodes = [...dom.querySelectorAll('.message-attribution-opposite')];

        for (const node of postNodes) {
            let parsedPost, parsedHosts;
            try {
                parsedPost = parsers.thread.parsePost(node);
                parsedHosts = parsers.hosts.parseHosts(parsedPost.content);
            } catch (e) { continue; }

            if (!parsedHosts.length) continue;

            h.ui.setElProps(statusUI.status, { color: '#469cf3', fontWeight: 'bold' });
            h.ui.setText(statusUI.status, `📄 ${p}/${b} · Post #${parsedPost.postNumber} indiriliyor...`);

            try {
                // Sıralı: bir postu bitirmeden diğerine geçme (host'ları yormaz)
                await downloadPost(
                    parsedPost,
                    parsedHosts,
                    getEnabledHostsCB,
                    resolvers,
                    () => threadSettings,
                    statusUI,
                    {},
                );
                donePosts++;
            } catch (e) {
                log.write('thread', `[THREAD] Post #${parsedPost.postNumber} hata: ${e?.message || e}`, 'ERROR');
            }
        }

        if (p < b) await h.delayedResolve(XFPD_THREAD_PAGE_DELAY_MS);
    }

    h.ui.setElProps(statusUI.status, { color: '#2d9053', fontWeight: 'bold' });
    h.show(statusUI.status);
    const next = `${b + 1}-${Math.min(b + XFPD_THREAD_CHUNK, lastPage)}`;
    h.ui.setText(
        statusUI.status,
        `✓ Bitti — ${a}-${b}. sayfa, ${donePosts} post${skippedPages ? `, ${skippedPages} sayfa atlandı` : ''}. ` +
        (b < lastPage ? `Sonraki parça: ${next}` : `Başlık tamamlandı.`),
    );
};
```

### 4b) Butonu sayfaya bağla
`document.addEventListener('DOMContentLoaded', async () => { ... })` callback'inin
İÇİNDE, mevcut **`if (parsedPosts.filter(p => p.parsedHosts.length).length > 0) { ... }`**
bloğunun **HEMEN ALTINA** (callback kapanmadan önce) ekle:

```js
        // === Thread (tüm sayfalar, 20'şer parça) butonu ===
        try {
            const btnThread = addDownloadThreadButton();
            const threadStatusUI = makeThreadStatusUI();
            let threadRunning = false;
            btnThread.addEventListener('click', async e => {
                e.preventDefault();
                if (threadRunning) { alert('Thread indirme zaten çalışıyor.'); return; }
                threadRunning = true;
                try { await downloadThread(threadStatusUI); }
                catch (err) { console.error(err); }
                finally { threadRunning = false; }
            });
        } catch (e) { console.error('Thread button init failed', e); }
```

### Nasıl çalışır
- Başlığın herhangi bir sayfasında üstteki **🡳 Download Thread** butonuna bas.
- Açılan kutuya aralık yaz: `1-20`, sonra `21-40`, `41-60`... (20'şerlik parçalar).
- Tarayıcı **hiç sayfa açmaz** — her sayfayı arka planda çeker, postları parse eder,
  her postu sırayla indirir. Sayfalar arası ~1.8 sn bekler (CF/rate-limit'e karşı).
- Her post ayrı ZIP olur (`Başlık #1234.zip`). Bitince "sonraki parça"yı söyler.

### ⚠️ Pratik notlar
- **Tampermonkey ayarı:** Çok dosya ineceği için "her indirmede sor"u kapat,
  sabit bir indirme klasörü ver (Tampermonkey → Settings → Downloads / "without asking").
- 20 sayfa × ~20 post = ~400 ZIP olabilir; parça parça gitmen tam da bunun için iyi.
- Bunkr ağırlıklı başlıklarda gerekirse `XFPD_THREAD_PAGE_DELAY_MS`'yi artır (örn. 3000).
- Yarıda kalırsa kaldığın parçadan (`21-40` gibi) devam et.
