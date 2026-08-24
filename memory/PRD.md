# PRD — artirdim.com (Laravel 12 + Inertia.js + Vue 3)

## Origin
GitHub repo: https://github.com/fgvcdz/sasd  → real Laravel app at `laravel_project/project/`.
This workspace was a fresh Emergent scaffold (Python/React) with the Laravel project missing.
Brought the project in, installed the full stack, and got it running on preview.

## Stack
- Backend: Laravel 12 (PHP 8.2), MariaDB, Redis
- Frontend: Inertia.js + Vue 3 (Vite build), Metronic (KeenThemes) Bootstrap theme (Tailwind NOT used)
- Realtime (planned): Laravel Reverb (WebSocket) — currently BROADCAST_CONNECTION=log
- Search: Scout/Meilisearch (optional), Horizon (queues)

## Domain
Online auction / bidding marketplace ("açık artırma"). Roles: admin, seller, buyer.
Features: auctions, bids, live-state polling, orders, payments (demo balance), messages/chat,
stories (24h), notifications, support tickets, seller panel, admin panel.

## Conversion status (Blade → Inertia/Vue) — from CONVERSION_PROGRESS.md
Nearly complete. Remaining conversion item: **Seller Live Stream / WebRTC page**
(`BroadcastController@show` → `auctions.blade.php`, route `seller.auctions.broadcast`).

## Work log
- 2026-08 (this session):
  - Set up full Laravel env in Emergent pod (PHP8.2, Composer, MariaDB, Redis), migrate+seed, yarn build.
  - Supervisor: added `laravel` (:3000), `laravel-queue`, `mariadb`, `redis`; stopped scaffold `frontend/backend/mongodb`.
  - Created `.env` (preview) + committable `.env.example`.
  - **Scroll fix (Aşama 6):** `resources/js/app.js` — forward navigation scrolls to top; back/forward + preserveScroll preserved.
  - **Scroll fix DOĞRULANDI (testing agent %100, iteration_5):** Gerçek scroller `document.body` (id=kt_app_body) çıktı. İleri→üst, geri→pozisyon korunuyor. Masaüstü+mobil.

## Known environment constraints
- Preview ingress only routes `/` → :3000 and `/api` → :8001. Reverb WS (:8080) is NOT exposed
  through the preview URL → real-time WebSocket (Reverb) needs a plan for live streaming/chat.
- `.env`/`.env.*` are gitignored (incl. `.env.example`) — template won't push unless .gitignore adjusted.

## Backlog / Next
- P1: Convert remaining seller live-stream Blade page to Vue.  ✅ DONE (Seller/Broadcast.vue)
- P1: Live streaming (WebRTC) + realtime chat.  ✅ DONE & verified (iteration_7, %100)
- P1: Deployment docs.  ✅ CANLI_YAYIN_KURULUM.md eklendi (+ mevcut KURULUM.md)
- P2: Clean leftover Emergent scaffold (`/app/backend`, `/app/frontend`, `/app/tests`, `test_result.md`) — kullanıcı onayı bekliyor.
- P2: Fix seed image 404s (storage/auctions/*.jpg).
- P3: (Opsiyonel) Chat'i production'da Reverb push'a yükselt (şu an polling).

## Mobil teklif çubuğu düzeltmeleri (2026-08, this session)
- Boyut küçültüldü (padding/font/min-height azaltıldı, safe-area eklendi).
- Sidebar (Metronic drawer) açıkken bar gizleniyor (z-index 90 + body:has(.drawer-overlay) → display:none) — artık üst üste binmiyor.
- Hızlı çipler artık CANLI minimumdan (input.min, auction-show.js canlı günceller) hesaplanıyor → "En az ... girmelisiniz" bayat-değer hatası bitti.
- Çip artık OTOMATİK GÖNDERMİYOR; sadece inputa yazıp "Teklif Ver"i vurguluyor, kullanıcı basınca gönderiliyor.
- Doğrulama: testing agent %100 (iteration_10). İlan aktif.

## Live streaming UX pass 2 (2026-08, this session)
- Bug: "Could not start video source" + yayın kendi kendine kopuyor → KÖK NEDEN: goLive'da çift kamera açılışı (önizleme+probe+LiveKit). Düzeltme: tek açılış, önizleme serbest bırakma+gecikme, NotReadableError için net mesaj, kamera açılamazsa sesli devam (kopmaz). toggleCam/Mic hata korumalı.
- Feature: İzleyici videosunda tam ekran (#fs-btn ⛶) canlıyken gösteriliyor.
- Feature: Mobil alt teklif çubuğuna tek dokunuşluk hızlı teklif çipleri (a.quick) + quickBidMobile().
- Doğrulama: testing agent %100 (iteration_9). İlan aktif.

## Live streaming UX pass (2026-08, this session)
- Bug #1 (yayın başlatılamıyor): goLive izin yönetimi sağlamlaştırıldı — güvenli bağlam + kamera/mik izni ön kontrolü, net Türkçe hatalar. Gerçek sebep çoğunlukla önizleme iframe'inde kamera izni; yeni sekmede/production'da çalışır.
- #2 Satıcı yayın sayfası: "Kamerayı Önizle" (yayına başlamadan kontrol) + "İzleyici Linki" kopyala + daha net durum/overlay.
- #3 İzleyici canlı paneli: video altına SATICI ŞERİDİ (avatar, ad, puan, "Profil" + "Satıcıya Sor") — mobilde de görünür; "Satıcıya Sor" chat'e kaydırır.
- Doğrulama: testing agent %100 (iteration_8, 15/15). İlan aktif bırakıldı.
- LiveKit Cloud (WebRTC SFU) entegre: `LiveKitTokenController` + `POST /livekit/token`, `config/services.php`.
- `resources/js/composables/useLiveKit.js` (token + connect/subscribe).
- `Seller/Broadcast.vue` (kamera/mik aç-kapa, Yayını Başlat/Bitir, teklifler+satış, canlı chat) — Blade→Vue dönüşümü tamam.
- `Auctions/Show.vue`: izleyici LiveKit abonesi + canlıyken "Canlı İzle" sekmesi otomatik açılıyor.
- Chat: mevcut `ChatController` (polling 3sn, spam korumalı). Production'da Reverb'e yükseltilebilir.
- Testing agent %100 (iteration_6/7): misafir izleyici gerçek yayını alıyor (readyState=4, 640x360), çift yönlü chat.
- .env: LIVEKIT_URL/API_KEY/API_SECRET eklendi (kullanıcı LiveKit Cloud anahtarlarını verdi).
