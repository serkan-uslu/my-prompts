# 📝 SemanticNotes — Proje Spesifikasyonu

> Notion benzeri, block tabanlı, AI-agent destekli, tamamen local çalışan not uygulaması.

---

## 🎯 Vizyon

Tüm veriler yerel makinede kalır. İnternet bağlantısı gerekmez. Ollama üzerinden local LLM'ler kullanılır. Kullanıcı, arayüzden kendi AI agent'larını tanımlayabilir ve bu agent'lar notlar üzerinde okuma, yazma, analiz gibi işlemler yapabilir.

---

## 🌍 Çoklu Dil Desteği (i18n)

**Varsayılan dil:** İngilizce (`en`)
**Mevcut diller:** İngilizce (`en`), Türkçe (`tr`)
**Kütüphane:** `i18next` + `react-i18next`

Yeni bir dil eklemek tek bir JSON dosyası eklemek anlamına gelir — kod değişikliği gerekmez.

---

### Dosya Yapısı

```
apps/web/src/
└── i18n/
    ├── index.ts              # i18next konfigürasyonu
    └── locales/
        ├── en/
        │   ├── common.json   # genel: butonlar, mesajlar, tarihler
        │   ├── editor.json   # editor: block tipleri, slash menu
        │   ├── agent.json    # agent: panel, run durumları, araçlar
        │   └── settings.json # ayarlar sayfası
        └── tr/
            ├── common.json
            ├── editor.json
            ├── agent.json
            └── settings.json
```

---

### Konfigürasyon

```typescript
// i18n/index.ts
import i18n from 'i18next'
import { initReactI18next } from 'react-i18next'
import LanguageDetector from 'i18next-browser-languagedetector'

i18n
  .use(LanguageDetector)       // sistem dilini otomatik algılar
  .use(initReactI18next)
  .init({
    fallbackLng: 'en',         // bilinmeyen dil → İngilizce
    supportedLngs: ['en', 'tr'],
    defaultNS: 'common',
    ns: ['common', 'editor', 'agent', 'settings'],
    interpolation: { escapeValue: false },
    resources: {
      en: {
        common:   enCommon,
        editor:   enEditor,
        agent:    enAgent,
        settings: enSettings,
      },
      tr: {
        common:   trCommon,
        editor:   trEditor,
        agent:    trAgent,
        settings: trSettings,
      },
    },
  })
```

---

### Örnek Çeviri Dosyaları

```json
// locales/en/common.json
{
  "app": {
    "name": "SemanticNotes",
    "tagline": "Your local AI-powered knowledge base"
  },
  "actions": {
    "save": "Save",
    "cancel": "Cancel",
    "delete": "Delete",
    "confirm": "Confirm",
    "accept": "Accept",
    "reject": "Reject",
    "retry": "Retry Again",
    "markAllRead": "Mark all as read"
  },
  "nav": {
    "newPage": "New page",
    "trash": "Trash",
    "settings": "Settings",
    "search": "Search"
  },
  "notifications": {
    "agentDone": "{{agentName}} completed",
    "agentFailed": "{{agentName}} failed",
    "agentRunning": "Step {{current}}/{{total}}",
    "noNotifications": "No notifications"
  },
  "time": {
    "justNow": "Just now",
    "minutesAgo": "{{count}} min ago",
    "hoursAgo": "{{count}} hr ago"
  }
}

// locales/tr/common.json
{
  "app": {
    "name": "SemanticNotes",
    "tagline": "Yerel AI destekli bilgi tabanın"
  },
  "actions": {
    "save": "Kaydet",
    "cancel": "İptal",
    "delete": "Sil",
    "confirm": "Onayla",
    "accept": "Kabul Et",
    "reject": "Reddet",
    "retry": "Tekrar Dene",
    "markAllRead": "Tümünü oku"
  },
  "nav": {
    "newPage": "Yeni sayfa",
    "trash": "Çöp kutusu",
    "settings": "Ayarlar",
    "search": "Ara"
  },
  "notifications": {
    "agentDone": "{{agentName}} tamamlandı",
    "agentFailed": "{{agentName}} başarısız oldu",
    "agentRunning": "Adım {{current}}/{{total}}",
    "noNotifications": "Bildirim yok"
  },
  "time": {
    "justNow": "Az önce",
    "minutesAgo": "{{count}} dk önce",
    "hoursAgo": "{{count}} sa önce"
  }
}
```

---

### Kullanım Kuralları

```typescript
// ✅ Her zaman t() fonksiyonu kullan, hardcode string yasak
const { t } = useTranslation('common')
<button>{t('actions.save')}</button>

// ✅ Namespace belirt
const { t: tAgent } = useTranslation('agent')
<span>{tAgent('run.status.running')}</span>

// ✅ Interpolation — değişkenler JSON'da kalır
t('notifications.agentDone', { agentName: agent.name })

// ❌ Hardcode string yasak
<button>Save</button>

// ❌ Inline Türkçe/İngilizce yasak
<span>{isRunning ? "Çalışıyor" : "Bekliyor"}</span>
```

---

### Dil Değiştirme (Settings)

```typescript
// Settings → Appearance → Language
// Seçenekler: English / Türkçe
// Seçim → uiStore'a yazar → localStorage'a persist → sayfa reload gerekmez

const changeLanguage = (lang: 'en' | 'tr') => {
  i18n.changeLanguage(lang)
  uiStore.setLanguage(lang)
}
```

**Ayarlar sayfasında:**
```
Language / Dil
  ○ English (default)
  ● Türkçe
```

---

### Yeni Dil Eklemek (Gelecek)

```
1. locales/de/ klasörü oluştur
2. 4 JSON dosyasını çevir (common, editor, agent, settings)
3. i18n/index.ts → supportedLngs dizisine 'de' ekle
4. resources objesine de: { ... } ekle
→ Başka hiçbir dosyaya dokunma
```

---



### Frontend
| Katman | Teknoloji | Açıklama |
|---|---|---|
| Framework | **Vite + React 18** | Hızlı HMR, sıfır config |
| Dil | **TypeScript** | Tip güvenliği, portfolio kalitesi |
| Stil | **Tailwind CSS v3** | Utility-first, hızlı UI |
| UI Bileşenler | **shadcn/ui** | Radix tabanlı, erişilebilir |
| Editor (Block) | **BlockNote** | Notion-like block editor, React uyumlu |
| State | **Zustand** | Minimal, boilerplate yok |
| Router | **React Router v6** | SPA navigasyon |
| İkonlar | **Lucide React** | Tutarlı ikon seti |
| i18n | **i18next + react-i18next** | Çoklu dil desteği |

### Backend (Local Server)
| Katman | Teknoloji | Açıklama |
|---|---|---|
| Runtime | **Node.js + Express** | `localhost:3001` |
| Dil | **TypeScript (tsx)** | Frontend ile aynı dil |
| DB Driver | **better-sqlite3** | Senkron, hızlı SQLite driver |
| ORM/Query | **Drizzle ORM** | Type-safe, migration desteği |
| Validasyon | **Zod** | Schema validasyon, frontend ile paylaşım |

### Veritabanları
| DB | Teknoloji | Kullanım |
|---|---|---|
| Ana DB | **SQLite** (`better-sqlite3`) | Notlar, bloklar, agent tanımları, ayarlar |
| Vektör DB | **ChromaDB** | Embedding'ler, semantic search — en popüler local vektör DB |

### AI / ML Katmanı
| Bileşen | Teknoloji | Açıklama |
|---|---|---|
| LLM | **Ollama** (`localhost:11434`) | Chat, completion, agent reasoning |
| Embedding | **Ollama** → `nomic-embed-text` | Metin vektörleştirme |
| Browser ML | **Transformers.js** (`@xenova/transformers`) | Offline embedding fallback, NLP görevler |
| Agent Orchestration | **Custom** (TypeScript) | Tool-calling loop, ReAct pattern |

### Geliştirici Araçları
| Araç | Teknoloji |
|---|---|
| Monorepo | pnpm workspaces |
| Linter | ESLint + Prettier |
| Tip Kontrol | TypeScript strict mode |
| API Test | Bruno (local, git-friendly) |

---

## 🎨 Tasarım Sistemi & Tema

### Felsefe

Notion'ın tasarım dili baz alınır: **beyaz alan, tipografi hiyerarşisi, minimal gürültü.** Renk UI'yı dekore etmez — anlam taşır. Her piksel bir amaca hizmet eder.

> "Design is not just what it looks like and feels like. Design is how it works." — Jobs

---

### Renk Paleti

#### Light Mode
```css
--bg-primary:       #FFFFFF;   /* Ana arka plan */
--bg-secondary:     #F7F7F5;   /* Sidebar, panel arka planı */
--bg-hover:         #EFEFEF;   /* Hover state */
--bg-active:        #E8E8E5;   /* Seçili item */
--bg-code:          #F1F1EF;   /* Kod bloğu arka planı */

--text-primary:     #1A1A1A;   /* Ana içerik metni */
--text-secondary:   #6B6B6B;   /* Yardımcı metin, placeholder */
--text-tertiary:    #999999;   /* Çok soluk: tarih, meta */
--text-disabled:    #C7C7C7;

--border:           #E8E8E5;   /* Çizgiler, separator */
--border-focus:     #1A1A1A;   /* Focus ring */

--accent:           #1A1A1A;   /* Primary buton, link */
--accent-hover:     #000000;
--accent-subtle:    #F0F0F0;   /* Ghost buton bg */

--destructive:      #DC2626;   /* Sil, hata */
--success:          #16A34A;   /* Başarı */
--warning:          #D97706;   /* Uyarı */

/* Agent / AI vurgu rengi — tek renkli aksan */
--ai-accent:        #6E56CF;   /* Mor — AI işlemlerini belirtir */
--ai-accent-subtle: #F4F0FF;
```

#### Dark Mode
```css
--bg-primary:       #191919;   /* Notion dark tam aynı */
--bg-secondary:     #1F1F1F;
--bg-hover:         #2A2A2A;
--bg-active:        #2F2F2F;
--bg-code:          #252525;

--text-primary:     #EFEFEF;
--text-secondary:   #9B9B9B;
--text-tertiary:    #666666;
--text-disabled:    #444444;

--border:           #2D2D2D;
--border-focus:     #EFEFEF;

--accent:           #EFEFEF;
--accent-hover:     #FFFFFF;
--accent-subtle:    #2A2A2A;

--destructive:      #F87171;
--success:          #4ADE80;
--warning:          #FCD34D;

--ai-accent:        #9D7FF0;
--ai-accent-subtle: #2A2040;
```

---

### Tipografi

```css
/* Font Stack — sistem fontları, sıfır yükleme */
font-family: 
  'Inter',           /* önce Inter (installed ise) */
  ui-sans-serif, 
  -apple-system, 
  BlinkMacSystemFont, 
  'Segoe UI', 
  sans-serif;

/* Kod fontları */
font-family-mono:
  'JetBrains Mono',
  'Fira Code',
  ui-monospace,
  'Cascadia Code',
  monospace;
```

#### Tip Ölçeği
```
text-xs:   11px / line-height: 1.4   → meta, tarih, badge
text-sm:   13px / line-height: 1.5   → sidebar item, label
text-base: 15px / line-height: 1.6   → paragraf (Notion varsayılanı)
text-lg:   18px / line-height: 1.5   → H3
text-xl:   22px / line-height: 1.4   → H2  
text-2xl:  28px / line-height: 1.3   → H1
text-3xl:  36px / line-height: 1.2   → Sayfa başlığı
text-4xl:  48px / line-height: 1.1   → Hero başlık (nadiren)

font-weight:
  normal:    400   → paragraf
  medium:    500   → label, nav item
  semibold:  600   → başlık, önemli
  bold:      700   → H1, sayfa başlığı (max burada)
```

---

### Spacing & Layout

```
/* 4px grid — her şey 4'ün katı */
space-1:  4px
space-2:  8px
space-3:  12px
space-4:  16px
space-5:  20px
space-6:  24px
space-8:  32px
space-10: 40px
space-12: 48px
space-16: 64px

/* Editor içerik genişliği — Notion gibi */
max-width-content: 720px    /* normal sayfa */
max-width-wide:    960px    /* geniş mod (toggle edilebilir) */

/* Panel genişlikleri */
sidebar-width:     240px
agent-panel-width: 280px
```

---

### Border Radius

```
radius-sm:   4px    → buton, input, badge
radius-md:   6px    → kart, dropdown
radius-lg:   8px    → modal, panel
radius-xl:   12px   → komut paleti, büyük kart
radius-full: 9999px → avatar, chip
```

---

### Gölge (Minimalist)

```css
/* Light mode — çok hafif */
shadow-sm:  0 1px 2px rgba(0,0,0,0.06);
shadow-md:  0 4px 12px rgba(0,0,0,0.08);
shadow-lg:  0 8px 24px rgba(0,0,0,0.10);
shadow-xl:  0 16px 48px rgba(0,0,0,0.12);  /* komut paleti */

/* Dark mode — biraz daha belirgin */
shadow-sm:  0 1px 2px rgba(0,0,0,0.20);
shadow-md:  0 4px 12px rgba(0,0,0,0.30);
```

---

### Animasyon & Geçiş

```css
/* Süre ölçeği — hız öncelikli */
duration-fast:    100ms   → hover, focus ring
duration-normal:  150ms   → dropdown aç, tooltip
duration-slow:    200ms   → modal, panel slide
duration-slower:  300ms   → sayfa geçişi

/* Easing */
ease-default: cubic-bezier(0.16, 1, 0.3, 1)   /* snappy, doğal */
ease-in:      cubic-bezier(0.4, 0, 1, 1)
ease-out:     cubic-bezier(0, 0, 0.2, 1)

/* Kurallar */
/* ❌ transform dışında animasyon yok (layout thrashing) */
/* ✅ transform + opacity → GPU accelerated */
/* ❌ 300ms üzeri animasyon (ağır hissettirir) */
/* ❌ bounce, spring, overshoot — business app değil oyun */
```

---

### Bileşen Stil Kuralları

#### Butonlar
```
Primary:   bg-accent, text-white, hover: bg-accent-hover
Secondary: bg-accent-subtle, text-primary, border: border
Ghost:     bg-transparent, hover: bg-hover
Danger:    bg-destructive, text-white
Boyut:     h-7 (sm) / h-8 (default) / h-9 (lg)
Padding:   px-3 (sm) / px-4 (default)
```

#### Input
```
bg-transparent
border: 1px solid var(--border)
focus: border-color → var(--border-focus), ring: none
border-radius: radius-sm
height: 32px (compact, Notion gibi)
```

#### Sidebar
```
bg: var(--bg-secondary)
item height: 28px
item padding: px-2
item border-radius: radius-sm
active item: bg-active, font-medium
hover: bg-hover
icon size: 16px, opacity: 0.7
collapse/expand: chevron, 150ms rotate
```

#### Block Editor
```
cursor text bölgesi: max-w-content, mx-auto, px-16 (desktop)
block hover → sol kenarda drag handle belirir (opacity 0 → 1, 100ms)
block seçili → hafif bg-hover highlight
slash menu → shadow-xl, border, radius-lg, max-h-64, scroll
inline toolbar → shadow-md, border, radius-md, bg-primary
```

#### Komut Paleti (Cmd+K)
```
overlay: bg-black/40 backdrop-blur-sm
panel: max-w-xl, shadow-xl, border, radius-xl
input: text-lg, no-border, no-ring
result item: h-9, px-3, radius-sm
keyboard hint: text-xs, text-tertiary, ml-auto
```

#### Modal / Dialog
```
overlay: bg-black/30 backdrop-blur-[2px]
panel: max-w-md (default) / max-w-lg (büyük)
shadow-xl, border, radius-xl
header: font-semibold, text-base
footer: border-top, pt-4, flex justify-end gap-2
```

---

### Dark/Light Mode Uygulama

```typescript
// Tailwind config
module.exports = {
  darkMode: 'class',   // 'class' stratejisi — JS ile kontrol
  // ...
}

// HTML root'a class ekle
document.documentElement.classList.toggle('dark', isDark)

// Zustand uiStore'da
type Theme = 'light' | 'dark' | 'system'

const applyTheme = (theme: Theme) => {
  const isDark = theme === 'dark' ||
    (theme === 'system' && window.matchMedia('(prefers-color-scheme: dark)').matches)
  document.documentElement.classList.toggle('dark', isDark)
}

// System tema değişimini dinle
window.matchMedia('(prefers-color-scheme: dark)')
  .addEventListener('change', () => applyTheme(getTheme()))
```

```tsx
// Tailwind'de kullanım
<div className="bg-white dark:bg-[#191919] text-[#1A1A1A] dark:text-[#EFEFEF]">
```

---

### Notion'dan Birebir Alınan UX Detayları

| Detay | Açıklama |
|---|---|
| Sayfa başlığı | Çok büyük, bold, üst boşluklu — `text-4xl font-bold` |
| Başlık placeholder | `"Başlıksız"` gri renkte, focus gelince kaybolur |
| Block spacing | Bloklar arası `mb-1` (4px) — sıkışık değil, havadar da değil |
| Emoji ikon | Başlık solunda, tıklanabilir, emoji picker açar |
| Kapak fotoğrafı | Sayfanın tepesinde tam genişlik, yükseklik 200px |
| `+ Yeni sayfa` | Sidebar altında sabit, ghost buton stili |
| Çöp kutusu | Sidebar'da ayrı section, arşivlenmiş notlar |
| Breadcrumb | Header'da — `Ana Sayfa / Proje / Not Adı` |
| Sayfa genişliği | Toggle: `Normal (720px)` vs `Geniş (960px)` |
| Hover'da `...` | Her sidebar item sağında, tıklayınca context menu |

---



```
semantic-notes/
├── apps/
│   ├── web/                  # Vite + React frontend
│   │   ├── src/
│   │   │   ├── components/
│   │   │   │   ├── editor/   # BlockNote bileşenleri
│   │   │   │   ├── agents/   # Agent UI panelleri
│   │   │   │   └── ui/       # shadcn bileşenleri
│   │   │   ├── stores/       # Zustand store'ları
│   │   │   ├── hooks/        # Custom React hooks
│   │   │   └── lib/          # API client, utils
│   └── server/               # Express backend
│       ├── src/
│       │   ├── db/           # Drizzle schema + migrations
│       │   ├── routes/       # REST API route'ları
│       │   ├── agents/       # Agent engine
│       │   ├── tools/        # Agent tool tanımları
│       │   ├── ai/           # Ollama + Transformers.js
│       │   └── vector/       # ChromaDB client wrapper
├── packages/
│   └── shared/               # Zod schemas, tipler (her iki app paylaşır)
├── data/
│   ├── notes.db              # SQLite dosyası
│   └── chroma/               # ChromaDB kalıcı storage
└── package.json
```

---

## 🗃 Veritabanı Şeması

### `notes`
```sql
id          TEXT PRIMARY KEY  -- nanoid
title       TEXT
icon        TEXT              -- emoji veya lucide icon adı
cover_url   TEXT
is_archived BOOLEAN
parent_id   TEXT              -- hiyerarşi için self-ref
created_at  DATETIME
updated_at  DATETIME
```

### `blocks`
```sql
id          TEXT PRIMARY KEY
note_id     TEXT REFERENCES notes(id)
type        TEXT              -- paragraph | heading | todo | code | image | divider | callout | table | embed
content     TEXT              -- JSON (block içeriği)
order_index REAL              -- fractional indexing
parent_id   TEXT              -- nested block desteği
created_at  DATETIME
updated_at  DATETIME
```

### `embeddings`
```sql
id          TEXT PRIMARY KEY
source_id   TEXT              -- note_id veya block_id
source_type TEXT              -- 'note' | 'block'
model       TEXT              -- kullanılan embedding modeli
chunk_index INTEGER           -- büyük metinler için chunk sırası
created_at  DATETIME
-- Vektörler Vectra'da ayrı saklanır, buradan id ile referans edilir
```

### `agents`
```sql
id          TEXT PRIMARY KEY
name        TEXT
description TEXT
system_prompt TEXT            -- agent'ın sistem prompt'u
model       TEXT              -- Ollama model adı
tools       TEXT              -- JSON array: aktif tool listesi
trigger     TEXT              -- 'manual' | 'on_save' | 'scheduled' | 'on_select'
schedule    TEXT              -- cron string (trigger=scheduled ise)
is_active   BOOLEAN
created_at  DATETIME
updated_at  DATETIME
```

### `agent_runs`
```sql
id          TEXT PRIMARY KEY
agent_id    TEXT REFERENCES agents(id)
input       TEXT
output      TEXT
steps       TEXT              -- JSON: adım adım düşünce zinciri
status      TEXT              -- 'running' | 'success' | 'error'
duration_ms INTEGER
created_at  DATETIME
```

### `agent_tool_calls`
```sql
id          TEXT PRIMARY KEY
run_id      TEXT REFERENCES agent_runs(id)
tool_name   TEXT
input       TEXT              -- JSON
output      TEXT              -- JSON
created_at  DATETIME
```

### `settings`
```sql
key         TEXT PRIMARY KEY
value       TEXT              -- JSON
updated_at  DATETIME
```

---

## ✨ Feature Listesi

### 📄 Editor & Not Yönetimi

#### Temel
- [ ] **Sidebar** — hiyerarşik not listesi, sürükle-bırak ile sıralama
- [ ] **Sayfa oluştur/sil/arşivle** — soft delete, geri getirme
- [ ] **Sayfa ikonu & kapak fotoğrafı** — emoji picker + renk paleti
- [ ] **İç içe sayfalar** — alt sayfa desteği, breadcrumb navigasyon
- [ ] **Hızlı not aç** — `Cmd+K` komut paleti

#### Block Editor (BlockNote tabanlı)
- [ ] **Block tipleri:**
  - `paragraph` — zengin metin (bold, italic, underline, code, link)
  - `heading` — H1 / H2 / H3
  - `bulleted_list` / `numbered_list` / `check_list`
  - `code` — sözdizim vurgulama (highlight.js)
  - `callout` — icon + renk seçimi
  - `divider`
  - `image` — yerel dosyadan yükle veya URL
  - `table` — temel tablo desteği
  - `toggle` — açılır/kapanır içerik
  - `quote`
- [ ] **Slash command** (`/`) — block tipi seçimi
- [ ] **Block drag-and-drop** — sıra değiştirme
- [ ] **Block / mention** — `@` ile diğer notlara referans
- [ ] **Inline AI** — seçili metin üzerinde AI aksiyonu (`/ai`)
- [ ] **Otomatik kaydetme** — debounce ile SQLite'a yaz

---

### 🔍 Semantic Search

- [ ] **Otomatik embedding** — not kaydedilince arka planda embedding üret
- [ ] **Embedding pipeline:**
  1. Metin blokları chunk'lara bölünür
  2. Ollama `nomic-embed-text` veya Transformers.js `all-MiniLM-L6-v2` ile vektörleştirilir
  3. ChromaDB'ye (`localhost:8000`) HTTP API üzerinden yazılır
- [ ] **Semantic arama kutusu** — `Cmd+Shift+F` kısayolu
- [ ] **Sonuç sıralaması** — cosine similarity skoru
- [ ] **Snippet preview** — eşleşen paragrafı vurgula
- [ ] **Hibrit arama** — semantic + SQLite FTS5 full-text, sonuçlar birleştirilir
- [ ] **Transformers.js fallback** — Ollama kapalıysa browser-side embedding

---

### 🤖 AI Agent Sistemi

#### Agent Tanımlama (Arayüzden)
- [ ] **Agent oluştur/düzenle/sil** — Settings → Agents paneli
- [ ] **Sistem prompt editörü** — çok satırlı, değişken desteği: `{{note_title}}`, `{{selected_text}}`, `{{today}}`
- [ ] **Model seçimi** — Ollama'dan mevcut modeller çekilir, dropdown
- [ ] **Tool seçimi** — agent'a hangi araçların açık olduğunu checkbox ile belirle
- [ ] **Tetikleyici seçimi:**
  - `Manuel` — kullanıcı butona tıklar
  - `Kayıt sonrası` — not her kaydedildiğinde
  - `Seçim sonrası` — kullanıcı metin seçtiğinde
  - `Zamanlanmış` — cron ile (günlük özet, haftalık rapor vb.)

#### Agent Tool'ları (Dahili)
| Tool | Açıklama |
|---|---|
| `read_note` | Bir notu başlık veya ID ile oku |
| `search_notes` | Semantic veya keyword arama yap |
| `list_notes` | Tüm notları veya filtrelenmiş listeyi getir |
| `create_note` | Yeni not oluştur, blokları yaz |
| `update_note` | Mevcut notu güncelle (başlık, blok ekle/değiştir) |
| `append_blocks` | Notun sonuna yeni bloklar ekle |
| `delete_blocks` | Belirtilen blokları sil |
| `search_semantic` | Vektör DB üzerinden anlamsal arama |
| `get_settings` | Uygulama ayarlarını oku |
| `summarize_note` | Notu özetle ve özet bloğu ekle |
| `tag_note` | Notun başlığına veya ilk satırına otomatik etiket ekle |
| `link_related` | Semantik olarak benzer notları bul ve linkle |

#### Agent Çalışma Motoru (ReAct Pattern)
```
User Input / Trigger
      ↓
System Prompt + Tools Schema → Ollama LLM
      ↓
Thought → Action (tool_call) → Observation
      ↓ (loop, max N adım)
Final Answer / DB Mutations
```

- [ ] **Adım adım log** — her agent run'ı için düşünce zinciri kaydedilir
- [ ] **Run geçmişi** — agent_runs tablosu, UI'dan görüntülenebilir
- [ ] **Hata yakalama** — tool hatası olursa agent retry veya graceful fail
- [ ] **Max step limit** — sonsuz döngü önlemi

#### Agent UI Paneli
- [ ] **Agent sidebar** — sağ panelde mevcut agent'lar listesi
- [ ] **Manuel çalıştır** — not açıkken seçili agent'ı tetikle
- [ ] **Run detayı modal** — adım adım düşünce + tool çağrıları görüntüle
- [ ] **Sonuç önizleme** — agent notu değiştirdiyse diff göster, onayla/reddet

---

### ⚙️ Ayarlar & Konfigürasyon

- [ ] **Ollama bağlantı ayarı** — host URL (default: `http://localhost:11434`)
- [ ] **Embedding model seçimi** — Ollama'daki mevcut modeller listelenir
- [ ] **LLM model seçimi** — varsayılan chat modeli
- [ ] **Transformers.js toggle** — browser-side embedding aç/kapat
- [ ] **Veri klasörü seçimi** — SQLite ve Vectra dosyalarının yeri
- [ ] **Otomatik embedding** — kayıt sonrası embedding üret (aç/kapat)
- [ ] **Tema** — light / dark / system

---

### 🎨 UX / Arayüz

- [ ] **3 panel layout** — Sol sidebar (notlar) + Orta (editor) + Sağ (agent panel, toggle)
- [ ] **Komut paleti** — `Cmd+K`: not aç, agent çalıştır, ayarlara git
- [ ] **Keyboard shortcuts:**
  - `Cmd+K` — komut paleti
  - `Cmd+Shift+F` — semantic arama
  - `Cmd+N` — yeni not
  - `Cmd+S` — manuel kaydet
  - `/` — block menüsü
- [ ] **Toast bildirimleri** — kayıt, agent tamamlandı, hata
- [ ] **Skeleton loading** — DB'den veri gelirken
- [ ] **Responsive** (minimum 1280px, desktop-first)

---

## 🔄 Veri Akışı — Embedding Pipeline

```
Not kaydedilir (otomatik/manuel)
        ↓
Backend: bloklar text'e dönüştürülür
        ↓
Chunk'lara bölünür (max 512 token)
        ↓
Ollama /api/embeddings → nomic-embed-text
    (Ollama kapalıysa → Transformers.js fallback)
        ↓
Vektörler ChromaDB'ye yazılır
SQLite embeddings tablosuna metadata yazılır
        ↓
Semantic arama sorgusunda:
Query → embed → ChromaDB.query() → top-k sonuç → UI
```

---

## 🔄 Veri Akışı — Agent Çalışması

```
Tetikleyici (manual / on_save / scheduled)
        ↓
Agent config DB'den çekilir
        ↓
System prompt + tool schema hazırlanır
        ↓
Ollama chat/completions (tool_call destekli model)
        ↓
Tool çağrısı → SQLite / ChromaDB işlemi
        ↓
Sonuç LLM'e geri beslenir (observation)
        ↓ (ReAct loop)
Final output → DB'ye yaz → UI'ya bildir
agent_runs + agent_tool_calls tablolarına log
```

---

## 📦 Temel Bağımlılıklar

```json
{
  "frontend": {
    "i18next": "^23.x",
    "react-i18next": "^14.x",
    "@xenova/transformers": "^2.x",
    "zustand": "^4.x",
    "react-router-dom": "^6.x",
    "tailwindcss": "^3.x",
    "shadcn-ui": "latest",
    "lucide-react": "latest",
    "zod": "^3.x",
    "nanoid": "^5.x"
  },
  "backend": {
    "express": "^4.x",
    "better-sqlite3": "^9.x",
    "drizzle-orm": "^0.x",
    "chromadb": "^1.x",
    "zod": "^3.x",
    "nanoid": "^5.x",
    "node-cron": "^3.x",
    "tsx": "^4.x"
  }
}
```

---

## 📐 Kodlama Kuralları & Mimari İlkeler

### 1. SOLID Prensipleri

#### S — Single Responsibility (Tek Sorumluluk)
Her dosya, bileşen ve fonksiyon **yalnızca bir iş** yapar.

```
❌ NoteEditor.tsx  →  not yükler + kaydeder + embed üretir + agent tetikler
✅ NoteEditor.tsx  →  sadece editor UI render'lar
✅ useNoteAutoSave.ts  →  sadece debounce + kayıt mantığı
✅ useEmbedding.ts  →  sadece embedding tetikleme
```

#### O — Open/Closed (Açık/Kapalı)
Yeni özellik eklemek mevcut kodu **değiştirmeyi gerektirmemeli**, genişletmeyi gerektirmeli.

```typescript
// ✅ Yeni block tipi eklemek için sadece yeni dosya:
// blocks/types/MyNewBlock.tsx
// blocks/registry.ts → kayıt yeterli, editor'a dokunma

// ❌ Her yeni block için editor switch-case'e dokunmak
```

#### L — Liskov Substitution
Interface'leri uygulayan her şey birbirinin yerine geçebilmeli.

```typescript
// Tüm agent tool'ları aynı interface'i implemente eder
interface AgentTool {
  name: string
  description: string
  schema: ZodSchema
  execute(input: unknown): Promise<ToolResult>
}
// Yeni tool eklemek = yeni dosya, engine'e dokunmak yok
```

#### I — Interface Segregation (Arayüz Ayrımı)
Büyük interface yerine küçük, odaklı tipler.

```typescript
// ❌ Tek dev interface
interface NoteService { create, read, update, delete, search, embed, export }

// ✅ Odaklı servisler
interface NoteRepository { findById, save, delete, listAll }
interface NoteSearchService { searchFullText, searchSemantic }
interface NoteEmbedService { embed, reEmbed, deleteEmbedding }
```

#### D — Dependency Inversion (Bağımlılık Tersine Çevirme)
Üst katmanlar somut implementasyona değil **abstraction'a** bağlı olmalı.

```typescript
// ✅ Agent engine, Ollama'ya direkt bağlı değil
interface EmbeddingProvider {
  embed(text: string): Promise<number[]>
}
class OllamaEmbedding implements EmbeddingProvider { ... }
class TransformersEmbedding implements EmbeddingProvider { ... }
// Hangisi aktif → settings'ten gelir, engine bilmez
```

---

### 2. Atomic Component Mimarisi

Bileşenler 4 katmana ayrılır. **Yukarı doğru import yasaktır.**

```
atoms/          → En küçük UI parçaları, state yok
  Button.tsx
  Input.tsx
  Badge.tsx
  Spinner.tsx
  Avatar.tsx

molecules/      → 2+ atom bir araya gelir, minimal logic
  SearchBar.tsx       (Input + Icon atom)
  BlockTypeMenu.tsx   (Button + Icon listesi)
  NoteListItem.tsx    (Avatar + Badge + text atomları)
  AgentStatusBadge.tsx

organisms/      → Bağımsız, kendi state'i olabilir
  Sidebar.tsx         (NoteTree + SearchBar + UserMenu)
  BlockEditor.tsx     (BlockNote wrapper + toolbar)
  AgentPanel.tsx      (AgentList + RunHistory)
  CommandPalette.tsx  (SearchBar + ResultList)

templates/      → Layout'lar, slot tabanlı
  ThreePanelLayout.tsx
  SettingsLayout.tsx

pages/          → Route'a karşılık gelir, sadece compose
  NotePage.tsx
  SettingsPage.tsx
  AgentsPage.tsx
```

**Kurallar:**
- `atoms` → dışarıdan hiçbir şey import etmez (sadece tip)
- `molecules` → sadece `atoms` import eder
- `organisms` → `atoms` + `molecules` + `hooks` + `stores`
- `pages` → `templates` + `organisms`, hiç logic yok
- Her bileşen kendi klasöründe: `Button/Button.tsx`, `Button/index.ts`, `Button/Button.test.tsx`

---

### 3. Dosya & Klasör Yapısı Kuralları

```
src/
├── components/
│   ├── atoms/
│   ├── molecules/
│   ├── organisms/
│   ├── templates/
│   └── pages/
├── hooks/               # Custom React hooks
│   ├── useNote.ts
│   ├── useAgent.ts
│   └── useSemantic.ts
├── stores/              # Zustand store'ları (domain bazlı)
│   ├── noteStore.ts
│   ├── agentStore.ts
│   └── uiStore.ts
├── services/            # API çağrıları (axios/fetch wrapper)
│   ├── noteService.ts
│   ├── agentService.ts
│   └── ollamaService.ts
├── lib/
│   ├── api.ts           # Base API client
│   ├── constants.ts     # Magic number yok, hepsi burada
│   └── utils.ts         # Pure utility fonksiyonları
└── types/               # Global tip tanımları
    ├── note.ts
    ├── agent.ts
    └── block.ts
```

**Dosya isimlendirme:**
```
PascalCase   → Bileşenler:  NoteListItem.tsx
camelCase    → Hooks:        useNoteEditor.ts
camelCase    → Servisler:    noteService.ts
camelCase    → Store:        noteStore.ts
kebab-case   → Config:       tailwind.config.ts
UPPER_SNAKE  → Sabitler:     MAX_BLOCK_DEPTH = 3
```

---

### 4. Hook Kuralları

Her hook **tek bir endişeyi** yönetir. Component'ler logic içermez.

```typescript
// ✅ Doğru — logic hook'ta
function NotePage() {
  const { note, isLoading, save } = useNote(noteId)
  const { run, isRunning } = useAgent(agentId)
  return <NoteEditor note={note} onSave={save} />
}

// ❌ Yanlış — logic component'te
function NotePage() {
  const [note, setNote] = useState(null)
  useEffect(() => { fetch('/api/notes/'+id).then(...) }, [])
  // ...50 satır logic
}
```

**Hook kategorileri:**
```
useNote(id)           → CRUD + optimistic update
useNoteList()         → sidebar için liste + search
useAgent(id)          → agent çalıştır + run takip
useEmbedding()        → embed tetikle + durum
useCommandPalette()   → açık/kapalı + arama
useBlockDragDrop()    → sürükle-bırak state + handler
useOllamaStatus()     → Ollama online mi kontrol
```

---

### 5. State Yönetimi Kuralları (Zustand)

**Store domain'e göre bölünür, tek büyük store yasak.**

```typescript
// noteStore.ts — sadece not verisi
interface NoteStore {
  notes: Note[]
  activeNoteId: string | null
  setActiveNote: (id: string) => void
  addNote: (note: Note) => void
  updateNote: (id: string, patch: Partial<Note>) => void
}

// uiStore.ts — sadece UI state
interface UIStore {
  sidebarOpen: boolean
  agentPanelOpen: boolean
  commandPaletteOpen: boolean
  theme: 'light' | 'dark' | 'system'
}

// agentStore.ts — sadece agent verisi
interface AgentStore {
  agents: Agent[]
  activeRunId: string | null
  runs: AgentRun[]
}
```

**Kurallar:**
- Store'larda **async logic yok** — servis katmanı halleder
- Server state için store kullanma → `useQuery` pattern (manuel fetch + loading state hook'ta)
- Derived state için `useMemo`, store'a yazma

---

### 6. Servis Katmanı Kuralları

Tüm API çağrıları `services/` klasöründe, bileşen içinde `fetch` yasak.

```typescript
// services/noteService.ts
export const noteService = {
  async getAll(): Promise<Note[]> { ... },
  async getById(id: string): Promise<Note> { ... },
  async create(data: CreateNoteInput): Promise<Note> { ... },
  async update(id: string, data: UpdateNoteInput): Promise<Note> { ... },
  async delete(id: string): Promise<void> { ... },
  async searchSemantic(query: string): Promise<SearchResult[]> { ... },
}

// Kullanım: sadece hook'larda
function useNote(id: string) {
  const [note, setNote] = useState<Note | null>(null)
  useEffect(() => {
    noteService.getById(id).then(setNote)
  }, [id])
  return note
}
```

---

### 7. Drag & Drop Mimarisi

**Sidebar (not ağacı) ve Block editor için ayrı DnD context.**

```
Kütüphane: @dnd-kit/core + @dnd-kit/sortable
```

```typescript
// Sidebar — not hiyerarşi sıralama
<DndContext onDragEnd={handleNoteReorder}>
  <SortableContext items={noteIds}>
    {notes.map(note => <SortableNoteItem key={note.id} note={note} />)}
  </SortableContext>
</DndContext>

// Editor — block sıralama (BlockNote built-in, override edilebilir)
// BlockNote kendi DnD'sini yönetir, sadece callback'leri dinle
editor.onBlockMoved = (blockId, newIndex) => {
  blockService.reorder(noteId, blockId, newIndex)
}
```

**DnD Kuralları:**
- Drag handle her zaman görünür (hover'da opacity: 1, normal: 0.3)
- Drag sırasında placeholder göster (ghost item)
- Drop sonrası optimistic update yap, backend'e kaydet
- Nested not'larda max derinlik: 5 seviye
- Fractional indexing kullan (`order_index REAL`) — tüm listeyi yeniden yazmak yok

```typescript
// Fractional indexing örneği
// A: 0.5, B: 1.0 arasına C eklemek → C: 0.75
function getBetween(a: number, b: number): number {
  return (a + b) / 2
}
```

---

### 8. TypeScript Kuralları

```typescript
// strict: true — tsconfig.json'da zorunlu
// any yasak — unknown kullan, sonra type-guard
// as casting minimal — sadece zorunluysa ve yorumla

// ✅ Tip güvenli event handler
function handleBlockChange(block: Block): void { ... }

// ❌ any ile kaçmak
function handleBlockChange(block: any) { ... }

// ✅ Discriminated union — block tipleri için
type Block =
  | { type: 'paragraph'; content: ParagraphContent }
  | { type: 'heading'; content: HeadingContent; level: 1 | 2 | 3 }
  | { type: 'code'; content: CodeContent; language: string }
  | { type: 'todo'; content: TodoContent; checked: boolean }

// ✅ Zod ile runtime validasyon (API cevapları için)
const NoteSchema = z.object({
  id: z.string(),
  title: z.string(),
  blocks: z.array(BlockSchema),
})
type Note = z.infer<typeof NoteSchema>
```

---

### 9. Error Handling Kuralları

```typescript
// ✅ Result pattern — exception fırlatma
type Result<T, E = Error> =
  | { ok: true; data: T }
  | { ok: false; error: E }

async function embedNote(id: string): Promise<Result<void>> {
  try {
    await ollamaService.embed(id)
    return { ok: true, data: undefined }
  } catch (e) {
    return { ok: false, error: e as Error }
  }
}

// UI katmanında
const result = await embedNote(noteId)
if (!result.ok) {
  toast.error('Embedding başarısız: ' + result.error.message)
  return
}
```

**Katmanlı hata yönetimi:**
```
API hatası     → servis katmanı yakalar, Result döner
İş mantığı hatası → hook loglar, UI'ya bildirir
UI hatası      → ErrorBoundary yakalar, fallback gösterir
Agent hatası   → agent_runs tablosuna yaz, UI'da göster
```

---

### 10. Performans Kuralları

```typescript
// Büyük listeler → virtualization
import { useVirtualizer } from '@tanstack/react-virtual'
// 100+ not listesi için zorunlu

// Debounce — otomatik kaydetme
const debouncedSave = useMemo(
  () => debounce((blocks: Block[]) => noteService.update(id, { blocks }), 800),
  [id]
)

// Memo — pahalı hesaplamalar
const sortedNotes = useMemo(() =>
  notes.sort((a, b) => a.order_index - b.order_index),
  [notes]
)

// Lazy loading — agent paneli başlangıçta yüklenmesin
const AgentPanel = lazy(() => import('./organisms/AgentPanel'))

// Image — yerel dosya → object URL, temizle
useEffect(() => {
  const url = URL.createObjectURL(file)
  return () => URL.revokeObjectURL(url)
}, [file])
```

---

### 11. Backend Katman Kuralları

```
routes/      → sadece HTTP: request parse, response format
services/    → iş mantığı, orchestration
repositories/→ sadece DB sorguları
```

```typescript
// ✅ Route — ince
router.post('/notes', async (req, res) => {
  const input = CreateNoteSchema.parse(req.body)      // validasyon
  const note = await noteService.create(input)         // iş mantığı
  res.json({ data: note })                             // response
})

// ✅ Service — iş mantığı
async function create(input: CreateNoteInput): Promise<Note> {
  const note = noteRepository.insert(input)            // DB
  await embedService.scheduleEmbed(note.id)            // yan etki
  return note
}

// ✅ Repository — sadece SQL
function insert(data: CreateNoteInput): Note {
  return db.prepare('INSERT INTO notes ...').get(data)
}
```

---

### 12. Kod Kalitesi Kontrol Listesi

Her PR / commit öncesi kontrol:

- [ ] Bileşen 150 satırı geçiyor mu? → Parçala
- [ ] Hook 100 satırı geçiyor mu? → Parçala
- [ ] `any` tipi var mı? → Kaldır
- [ ] Doğrudan `fetch` bileşen içinde mi? → Servise taşı
- [ ] Magic number var mı? (`300`, `5`, `0.75`) → `constants.ts`'e al
- [ ] Console.log production'a gidecek mi? → Kaldır
- [ ] Error handle edilmemiş async var mı? → Try-catch ekle
- [ ] Yeni block tipi tek bir dosyada mı tanımlandı? → ✅
- [ ] Yeni agent tool tek bir dosyada mı tanımlandı? → ✅

---

### 13. Notion Benzeri Temel Davranışlar (Zorunlu)

Notion'ın temel UX kuralları birebir uygulanmalı:

| Davranış | Uygulama |
|---|---|
| Her blok seçilebilir | Click → focus, `::before` handle görünür |
| `/` her yerde çalışır | Boş satırda veya satır başında |
| `Enter` → yeni paragraf | Aynı tip devam eder |
| `Tab` → indent (nested) | Max 3 seviye iç içe |
| `Shift+Tab` → de-indent | |
| `Backspace` boş blok → sil | Bir önceki bloğa focus |
| Blok drag handle | Hover'da sol tarafta `⠿` ikonu |
| Blok seçimi | Tıklayıp sürükle → çoklu seçim |
| Inline format toolbar | Metin seç → floating toolbar: B I U S Code Link |
| `Cmd+Z` / `Cmd+Y` | Tam undo/redo (BlockNote built-in) |
| Sayfa başlığı focus | Sayfa açıldığında başlık boşsa otomatik focus |

---

## 🤖 Agent Davranış Sözleşmesi (Agent Contract)

> Bu bölüm bir UI özelliği değil, **çalışma zamanı kuralıdır.**
> Sistem içindeki tüm agent'lar — kullanıcı tanımlı olanlar dahil — bu kurallara tabidir.
> Kod düzeyinde enforce edilir; kullanıcı veya agent tarafından override edilemez.

---

### 1. Temel Felsefe: Bilişsel Asistan, Yazar Değil

```
SemanticNotes kullanıcıya ait bir bilgi sistemidir.

Agent'lar bilişimi destekler — yazarlığın yerini alamaz.
Bilgi yaratma ve değiştirme konusundaki nihai otorite
her zaman kullanıcıdır.
```

Bu prensip tüm diğer kuralların temelidir. Agent bir notu ne kadar "iyi" yazabilirse yazsın, **kullanıcı onayı olmadan kalıcı hale gelemez.**

---

### 2. Data Authority Model (Veri Otoritesi)

Agent'ların bilgiyle ilişkisini tanımlar.

```
SOURCE OF TRUTH:  Kullanıcının yazdığı notlar
REASONING SPACE:  Agent'ın geçici çalışma alanı (kalıcı değil)
PERSISTENCE:      Yalnızca onaylanmış output → DB'ye yazılır
```

**Kesin kurallar:**

```typescript
// ✅ Agent sadece mevcut notu okuyabilir
// ✅ Agent açıkça yönlendirildiğinde yeni içerik üretebilir
// ❌ Agent var olmayan bilgiyi "hatırladım" diyerek üretemez
// ❌ Agent başka notlardaki bilgiyi kaynak göstermeden kullanamaz
// ❌ Agent belirsiz durumda varsayım yapıp kaydedemez
```

**Traceability zorunluluğu** — agent'ın ürettiği her içerik şunlardan birine dayanmalıdır:

```
1. Kullanıcı inputu (prompt, seçili metin, komut)
2. Mevcut notların içeriği (read_note / search_notes tool)
3. Açık reasoning zinciri (agent_runs.steps alanında loglanmış)
```

Bunların dışında üretilen her şey → **hallucination**, kayıt edilemez.

---

### 3. Agent Permission Boundary (Yetki Sınırları)

Her operasyon tipinin yetki seviyesi kod düzeyinde tanımlıdır.

```typescript
type PermissionLevel = 'auto' | 'confirm' | 'diff_approve' | 'explicit_consent'

const TOOL_PERMISSIONS: Record<ToolName, PermissionLevel> = {
  // READ — otomatik, onay gerekmez
  read_note:       'auto',
  list_notes:      'auto',
  search_notes:    'auto',
  search_semantic: 'auto',
  get_settings:    'auto',

  // WRITE — kullanıcıya toast bildirimi + confirm dialog
  append_blocks:   'confirm',
  create_note:     'confirm',
  tag_note:        'confirm',
  summarize_note:  'confirm',

  // STRUCTURAL — değişiklik diff olarak gösterilir, onayla/reddet
  update_note:     'diff_approve',
  link_related:    'diff_approve',

  // DESTRUCTIVE — açık yazılı onay gerekir
  delete_blocks:   'explicit_consent',
}
```

**UI enforcement kuralları:**
- `confirm` → sağ alt köşede toast: `"[Agent adı] X notu güncelledi. Onayla / Geri al"`
- `diff_approve` → modal açılır, eski/yeni içerik yan yana, `Kabul Et` / `Reddet`
- `explicit_consent` → `"Bu işlem geri alınamaz. Devam etmek için 'sil' yazın."` prompt'u
- Agent pipeline'da permission check başarısız olursa → run durur, `status: 'blocked'` yazılır

---

### 4. Semantic Indexing Policy (Embedding Güvenliği)

ChromaDB'ye ne gireceğini tanımlar.

```
Embedding index'e YALNIZCA şunlar girer:
  ✅ Kullanıcının doğrudan yazdığı içerik
  ✅ Kullanıcının 'diff_approve' ile onayladığı agent output'u
  ✅ Kullanıcının 'confirm' ile onayladığı yeni bloklar

Embedding index'e KESİNLİKLE şunlar GİRMEZ:
  ❌ Agent'ın ara reasoning adımları (agent_runs.steps)
  ❌ Reddedilen agent output'ları
  ❌ Sistem promptları ve tool şemaları
  ❌ Onay bekleyen (pending) içerik
```

**Kod düzeyinde:** `embedService.embed()` fonksiyonu yalnızca `approved: true` bayrağı taşıyan içeriği kabul eder.

```typescript
interface EmbedRequest {
  sourceId: string
  content: string
  approved: boolean   // false ise → throw EmbeddingNotAllowedError
  approvedAt?: Date
  approvedBy: 'user' | 'system'  // 'agent' tipi yok
}
```

Bu kural olmadan: agent'ın ara düşünceleri semantic search'e girer → yanlış sonuçlar → güven kaybı.

---

### 5. Agent Cognitive Limits (Bilişsel Sınırlar)

Sonsuz döngü ve kaynak tüketimi önlemi.

```typescript
interface AgentRunLimits {
  maxSteps: number          // default: 10  — max reasoning adımı
  maxToolCalls: number      // default: 15  — max tool çağrısı
  maxOutputTokens: number   // default: 2000 — max üretilen metin
  timeoutMs: number         // default: 60_000 — 60 saniye
  confidenceThreshold: number // default: 0.7 — altında → kullanıcıya sor
}

// Kullanıcı arayüzden her agent için özelleştirebilir
// Minimum değerlerin altına inemez (sistem limiti)
const SYSTEM_MIN_LIMITS = {
  maxSteps: 3,
  maxToolCalls: 5,
  timeoutMs: 10_000,
}
```

**Zorunlu davranışlar:**

```
❌ Agent kendini tekrar çağıramaz (recursive self-invocation yasak)
❌ Agent başka bir agent'ı tetikleyemez (agent chaining — kullanıcı onayı gerekir)
❌ Limit aşıldığında sessizce devam edemez → status: 'limit_exceeded' yaz, dur
✅ Confidence düşükse → tool call yerine clarification_needed döndür
✅ Her adım agent_runs.steps'e loglanır (kullanıcı görebilir)
```

**Clarification mekanizması:**

```typescript
// Agent emin değilse yazmak yerine sorar
type AgentOutput =
  | { type: 'result'; content: string }
  | { type: 'clarification_needed'; question: string; context: string }
  | { type: 'blocked'; reason: string }   // permission yetmedi
  | { type: 'limit_exceeded'; lastStep: string }
```

---

### 6. Local-First Trust Model (Ağ Güvenliği)

Agent'ların erişebileceği kaynakları tanımlar.

```
SemanticNotes sıfır dışarı güven modeli üzerine çalışır.

✅ İzin verilen:
   localhost:3001  (Express API)
   localhost:11434 (Ollama)
   localhost:8000  (ChromaDB)

❌ Kesinlikle yasak:
   Herhangi bir dış URL'e fetch/request
   DNS sorgusu (external)
   Dosya sistemi erişimi (data/ klasörü dışı)
   Environment variable okuma (API key arama)
```

**Kod düzeyinde:** Agent tool'ları `fetch` yapamaz. Tüm dış erişim `services/` katmanından geçer, bu katman whitelist dışı host'ları reddeder.

```typescript
// agentToolExecutor.ts
const ALLOWED_HOSTS = ['localhost', '127.0.0.1']

async function executeToolCall(tool: AgentTool, input: unknown) {
  // Tool'lar doğrudan fetch yapamaz, sadece internal service'leri çağırır
  return tool.execute(input, { allowedHosts: ALLOWED_HOSTS })
}
```

---

### 7. Agent Contract — Özet Tablosu

| Kural | Değer | Override Edilebilir Mi? |
|---|---|---|
| Hallucination yasağı | Kesin | ❌ Hayır |
| READ → auto | Her zaman | ❌ Hayır |
| WRITE → confirm | Her zaman | ❌ Hayır |
| DESTRUCTIVE → explicit | Her zaman | ❌ Hayır |
| Ara reasoning embed edilmez | Kesin | ❌ Hayır |
| Max steps | 10 (default) | ✅ Kullanıcı artırabilir |
| Max tool calls | 15 (default) | ✅ Kullanıcı artırabilir |
| Timeout | 60s (default) | ✅ Kullanıcı artırabilir |
| Dış ağ erişimi | Yasak | ❌ Hayır |
| Agent → agent tetikleme | Kullanıcı onayıyla | ⚠️ Onay gerekir |

---

## ⚙️ Background Agent Sistemi

> Agent'lar **fire-and-forget** modeliyle çalışır.
> Kullanıcı çalıştır der, uygulama yanıt verir, agent arka planda işini bitirir.
> Tamamlandığında notification center'da bildirim belirir, çıktı DB'de hazırdır.

---

### Genel Akış

```
Kullanıcı "Agent'ı Çalıştır" butonuna tıklar
        ↓
POST /api/agent-jobs  →  job_queue tablosuna yazılır
        ↓
API anında { jobId, status: 'queued' } döner  ← Kullanıcı beklemez
        ↓
Backend Worker Loop (her 2 saniyede poll eder)
        ↓
Job alınır → status: 'running' → ReAct loop başlar
        ↓
Tamamlanır → status: 'done' → agent_runs'a sonuç yazılır
        ↓
SSE push → Frontend'e bildirim gönderilir
        ↓
Notification Center'da bildirim belirir
Kullanıcı tıklar → sonucu inceler, onaylar veya reddeder
```

---

### 1. Job Queue — Veritabanı Şeması

```sql
CREATE TABLE job_queue (
  id            TEXT PRIMARY KEY,
  agent_id      TEXT REFERENCES agents(id),
  status        TEXT DEFAULT 'queued',  -- queued|running|done|failed|cancelled
  priority      INTEGER DEFAULT 5,      -- 1 yüksek → 10 düşük
  input         TEXT,                   -- JSON: { noteId?, selectedText?, userPrompt? }
  result_run_id TEXT,                   -- tamamlanınca agent_runs.id
  error         TEXT,
  queued_at     DATETIME DEFAULT NOW(),
  started_at    DATETIME,
  completed_at  DATETIME,
  notify_user   BOOLEAN DEFAULT TRUE
);

CREATE TABLE notifications (
  id          TEXT PRIMARY KEY,
  type        TEXT,   -- agent_done | agent_failed | agent_blocked | system
  title       TEXT,
  body        TEXT,
  job_id      TEXT REFERENCES job_queue(id),
  run_id      TEXT REFERENCES agent_runs(id),
  is_read     BOOLEAN DEFAULT FALSE,
  created_at  DATETIME DEFAULT NOW()
);
```

---

### 2. Worker Loop (Backend)

```typescript
// server/src/worker/agentWorker.ts
// Sunucu başladığında tek sefer başlatılır, sürekli çalışır

const POLL_INTERVAL_MS = 2000
const MAX_CONCURRENT   = 3      // aynı anda max 3 agent

class AgentWorker {
  private running = new Set<string>()

  start() {
    setInterval(() => this.tick(), POLL_INTERVAL_MS)
  }

  private async tick() {
    if (this.running.size >= MAX_CONCURRENT) return

    const job = jobQueueRepository.dequeue()   // priority sırasıyla al
    if (!job) return

    this.running.add(job.id)
    jobQueueRepository.setStatus(job.id, 'running')
    this.executeJob(job).finally(() => this.running.delete(job.id))
  }

  private async executeJob(job: Job) {
    try {
      const run = await agentEngine.run(job)
      jobQueueRepository.setStatus(job.id, 'done', run.id)

      if (job.notify_user) {
        await notificationService.create({
          type: 'agent_done',
          title: `${job.agentName} tamamlandı`,
          body: run.summary,
          jobId: job.id,
          runId: run.id,
        })
        sseService.push('notification', { jobId: job.id })
      }
    } catch (err) {
      jobQueueRepository.setStatus(job.id, 'failed')
      await notificationService.create({
        type: 'agent_failed',
        title: `${job.agentName} başarısız oldu`,
        body: err.message,
        jobId: job.id,
      })
      sseService.push('notification', { jobId: job.id })
    }
  }
}
```

---

### 3. SSE — Gerçek Zamanlı Bildirim

WebSocket yerine **SSE (Server-Sent Events)** — tek yönlü push yeterli, library gerekmez.

```typescript
// server/src/realtime/sseService.ts
class SSEService {
  private clients = new Map<string, Response>()

  register(clientId: string, res: Response) {
    res.setHeader('Content-Type', 'text/event-stream')
    res.setHeader('Cache-Control', 'no-cache')
    res.setHeader('Connection', 'keep-alive')
    this.clients.set(clientId, res)
    res.on('close', () => this.clients.delete(clientId))
  }

  push(event: string, data: unknown) {
    const payload = `event: ${event}\ndata: ${JSON.stringify(data)}\n\n`
    this.clients.forEach(res => res.write(payload))
  }
}

// Frontend dinleme
const es = new EventSource('http://localhost:3001/api/sse')
es.addEventListener('notification', (e) => {
  const { jobId } = JSON.parse(e.data)
  notificationStore.fetchLatest()
})

// Çalışan job'da her ReAct adımı push'lanır
sseService.push('agent_step', {
  jobId,
  step: { thought, action, observation },
  stepIndex: 3,
  maxSteps: 10,
})
```

---

### 4. Notification Center UI

**Konum:** Header sağ üst — zil ikonu, Notion'daki gibi, okunmamış badge.

```
┌──────────────────────────────────────┐
│  🔔  Bildirimler        [Tümünü oku] │
├──────────────────────────────────────┤
│ ✅ Fikir Analizörü tamamlandı        │
│    "15 AI fikrin analiz edildi"      │
│    [Sonucu Gör]            2 dk önce │
├──────────────────────────────────────┤
│ ⏳ Market Araştırma Agent çalışıyor  │
│    ████████░░  Adım 4/10             │
│    [İptal Et]                        │
├──────────────────────────────────────┤
│ ❌ Özet Agent başarısız oldu         │
│    "Timeout: 60s aşıldı"             │
│    [Tekrar Dene]           5 dk önce │
└──────────────────────────────────────┘
```

**Tıklama davranışları:**
- `agent_done` → sağ panelde run detayı + diff önizleme + Kabul Et / Reddet
- `agent_failed` → hata mesajı + Tekrar Dene butonu
- `agent_blocked` → hangi permission adımında durduğu + Onayla butonu

---

### 5. İnternet Erişim Katmanı (Opt-in Web Tools)

> Varsayılan olarak **kapalı**. Kullanıcı agent tanımlarken açıkça "İnternet Erişimi" toggle'ını aktif eder.
> Her internet erişimli run → log'da 🌐 işareti ile işaretlenir.

#### Araç Seti

| Tool | Teknoloji | Açıklama |
|---|---|---|
| `web_search` | **SearXNG** (self-hosted) | Privacy-first, local arama motoru |
| `fetch_page` | **Playwright** (headless) | URL'den sayfa içeriği çeker, JS render eder |
| `extract_content` | **Readability.js** | Ham HTML'den makale metnini ayıklar |

**SearXNG neden?** — `localhost:8080`'de çalışır, Google/Bing/DDG'yi proxy'ler, API key yok, `docker run searxng/searxng` ile kurulur.

```typescript
// server/src/tools/webSearchTool.ts
const webSearchTool: AgentTool = {
  name: 'web_search',
  requiresPermission: 'internet',    // bu flag yoksa tool yüklenemez
  schema: z.object({
    query: z.string(),
    maxResults: z.number().default(5),
  }),
  async execute({ query, maxResults }) {
    const res = await fetch(
      `http://localhost:8080/search?q=${encodeURIComponent(query)}&format=json`
    )
    const { results } = await res.json()
    return results.slice(0, maxResults).map(r => ({
      title: r.title, url: r.url, snippet: r.content,
    }))
  }
}

// server/src/tools/fetchPageTool.ts
const fetchPageTool: AgentTool = {
  name: 'fetch_page',
  requiresPermission: 'internet',
  schema: z.object({ url: z.string().url() }),
  async execute({ url }) {
    const browser = await chromium.launch()
    const page = await browser.newPage()
    await page.goto(url, { waitUntil: 'domcontentloaded' })
    const html = await page.content()
    await browser.close()
    const article = new Readability(new JSDOM(html).window.document).parse()
    return { url, content: article?.content ?? '' }
  }
}
```

#### Örnek Akış — "Fikirlerimi analiz et, market araştırması yap"

```
[queued]  Job kuyruğa eklendi → kullanıcı çalışmaya devam eder

[auto]    read_note       → "AI Fikirleri" notunu oku
[auto]    search_semantic → semantik olarak ilgili diğer notları bul
[auto]    web_search      → "AI market trends 2025" (SearXNG)
[auto]    web_search      → "AI startup opportunities 2025"
[auto]    fetch_page      → ilk 3 sonucun içeriği (Playwright)
[auto]    extract_content → ham HTML → temiz metin
[confirm] create_note     → "AI Fikir Analizi — 2025-03-02" notu oluştur
[diff]    update_note     → analiz, bulgular, öneriler ekle

~3-5 dakika sonra...

🔔 Notification: "Fikir Analizörü tamamlandı"
→ Kullanıcı tıklar → diff gösterilir → Kabul Et
```

---

### 6. Güncel Port Haritası

```
localhost:5173  → Vite (Frontend)
localhost:3001  → Express (Backend API + SSE endpoint)
localhost:8000  → ChromaDB (Vector DB)
localhost:11434 → Ollama (LLM + Embedding)
localhost:8080  → SearXNG (Web Search — opsiyonel, Docker)
```

---

### 7. Agent Contract — İnternet Ek Kuralları

```
✅ İnternet izni agent tanımında açıkça aktif edilmiş olmalı
✅ Her web isteği agent_tool_calls tablosuna URL ile loglanır
✅ Ziyaret edilen tüm URL'ler run detayında kullanıcıya gösterilir
✅ Web'den çekilen içerik embed edilmeden önce kullanıcı onayı alınır
❌ Kullanıcı onayı olmadan web içeriği veritabanına yazılamaz
❌ Login, kimlik doğrulama veya çerez gerektiren sayfalar yasak
❌ Dosya indirme, form gönderme, POST isteği yasak
```

---

## 🚀 Geliştirme Aşamaları

### Faz 1 — Temel (2-3 hafta)
1. Monorepo setup (pnpm workspaces)
2. Express server + SQLite + Drizzle schema
3. Vite + React + BlockNote entegrasyonu
4. CRUD: not oluştur, sil, listele, düzenle
5. Otomatik kaydetme

### Faz 2 — AI Altyapı (1-2 hafta)
6. Ollama bağlantısı + model listesi
7. Embedding pipeline + Vectra setup
8. Transformers.js fallback
9. Semantic arama UI

### Faz 3 — Agent Sistemi (2-3 hafta)
10. Agent CRUD (arayüzden tanımlama)
11. Tool engine (read/write/search)
12. ReAct loop + run logging
13. Agent UI paneli + run detayı

### Faz 4 — Cila (1 hafta)
14. Komut paleti
15. Keyboard shortcuts
16. Settings sayfası
17. Dark/light tema

### Faz 5 — Background Agent & İnternet (1-2 hafta)
18. Job queue tablosu + worker loop
19. SSE endpoint + frontend dinleyici
20. Notification Center UI
21. SearXNG Docker setup + web_search tool
22. Playwright + fetch_page + extract_content tool
23. Agent tanımına "İnternet Erişimi" toggle'ı
24. Çalışan job'larda canlı adım progress

---

- **Offline-first:** Uygulama Ollama olmadan da açılır, embedding/AI özellikleri devre dışı kalır.
- **Transformers.js stratejisi:** Ollama çevrimdışıysa `all-MiniLM-L6-v2` WASM modeli browser'da çalışır — kullanıcı fark etmez.
- **Port yönetimi:** `3001` Express, `8000` ChromaDB, `11434` Ollama, `5173` Vite dev server.
- **ChromaDB kurulum:** `pip install chromadb && chroma run` — Python gerekir ama tek seferlik kurulum.
- **Güvenlik:** Express ve ChromaDB sadece `localhost`'a bağlı, dışarıya açık değil.
- **Portfolio değeri:** Tüm stack TypeScript, agent sistemi custom-built, Transformers.js entegrasyonu özgün.
