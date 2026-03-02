# my-prompts — Kişisel Prompt Kütüphanesi Yol Haritası

> Prompt mühendisliği artık bir Frontend Developer'ın temel becerisi.  
> Bu repo, AI ile çalışırken test edilmiş, production'da kullanılmış prompt ve kuralların koleksiyonudur.

---

## Felsefe

Kod yazmak artık tek başına bir vasıf değil. Asıl beceri:
- AI'a doğru context vermek
- Kuralları önceden tanımlamak
- Her projede tutarlı çıktılar almak

Bu repo bunun için var.

---

## Repo Yapısı

```
my-prompts/
│
├── README.md
├── PHILOSOPHY.md
├── CHANGELOG.md
│
├── nextjs/
│   ├── README.md
│   ├── project-init-rules.md
│   ├── development-rules.md
│   ├── feature-rules.md
│   ├── db-rules.md
│   ├── api-rules.md
│   └── ui-rules.md
│
├── react-native/
│   ├── README.md
│   └── expo-setup-rules.md
│
├── styling/
│   ├── tailwind-rules.md
│   └── design-system-rules.md
│
├── code-quality/
│   ├── typescript-rules.md
│   ├── component-rules.md
│   ├── naming-rules.md
│   ├── code-review-rules.md
│   ├── refactor-rules.md
│   └── debug-rules.md
│
├── state-and-data/
│   ├── state-management-rules.md
│   ├── api-rules.md
│   └── data-fetching-rules.md
│
├── ai-integration/
│   ├── ollama-prompts.md
│   └── api-wrapper-rules.md
│
└── templates/
    └── PROMPT_TEMPLATE.md
```

---

## Her `.md` Dosyasının Anatomisi

Her prompt/rules dosyası bu yapıda yazılır:

```markdown
# [Başlık]

## 🎯 Purpose
Bu dosyanın ne işe yaradığı (1-2 cümle)

## 📋 Context
Claude'a verilmesi gereken ön bilgiler

## 💬 The Prompt / Rules
​```
Asıl prompt veya kurallar buraya gelir.
​```

## ⚙️ Variables
| Variable        | Description     | Example     |
|-----------------|-----------------|-------------|
| `{project_name}` | Projenin adı   | `my-app`    |

## 📝 Example Output
Beklenen çıktının kısa örneği

## 🔄 Variations
Farklı kullanım senaryoları
```

---

## Dosya Kategorileri ve Öncelik Sırası

### 🔴 Önce Bunlar (Hemen İşe Yararlar)

| Dosya | Neden Önce |
|-------|------------|
| `nextjs/development-rules.md` | Her Next.js projesinde kullanılır |
| `code-quality/component-rules.md` | Framework bağımsız, evrensel |
| `nextjs/feature-rules.md` | Yeni feature açarken zaman kazandırır |

### 🟡 Sonra Bunlar (Zamanla Birikir)

| Dosya | Notlar |
|-------|--------|
| `code-quality/typescript-rules.md` | Type disiplini için |
| `state-and-data/api-rules.md` | Error handling standartları |
| `code-quality/debug-rules.md` | Bug çözerken context şablonu |

### 🟢 Bunlar Gelişir (Projelerden Damıtılır)

| Dosya | Notlar |
|-------|--------|
| `styling/tailwind-rules.md` | Projeler büyüdükçe netleşir |
| `react-native/expo-setup-rules.md` | Expo workflow'una göre şekillenir |
| `ai-integration/ollama-prompts.md` | Kendi local AI workflow'un |

---

## CLAUDE.md Olarak Kullanım

Bu dosyalar iki şekilde çalışır:

**1. Projeye CLAUDE.md olarak ekle**
```
my-project/
└── CLAUDE.md   ← nextjs/development-rules.md içeriği buraya
```
Claude Code, proje açıldığında bu dosyayı otomatik okur. Her sohbette context vermene gerek kalmaz.

**2. Sohbet başında yapıştır**
Yeni bir Claude sohbeti açtığında ilgili rules dosyasını kopyalayıp yapıştır. Claude o proje boyunca kurallara göre davranır.

---

## Örnek: `nextjs/development-rules.md` İçeriği

```markdown
You are a Senior Next.js 14+ developer. Follow these rules strictly:

**Architecture**
- App Router only, no Pages Router
- Server Components by default, Client only when needed
- Co-locate components with their routes

**Code Style**
- TypeScript strict mode
- Functional components only
- Named exports for components, default export for pages

**Styling**
- Tailwind CSS utility-first
- No inline styles
- Mobile-first breakpoints

**State Management**
- Zustand for global state
- React Query / TanStack Query for server state
- useState only for local UI state

**File Naming**
- Components: PascalCase → `UserCard.tsx`
- Hooks: camelCase with use prefix → `useUserData.ts`
- Utils: camelCase → `formatDate.ts`
```

---

## Repo'yu Öne Çıkaracak Detaylar

- **GitHub Topics:** `prompt-engineering` `nextjs` `claude` `ai` `developer-tools` `frontend`
- **CHANGELOG.md** — Hangi promptu ne zaman, neden güncelledin
- **Her dosyada tarih** — `Last updated: YYYY-MM-DD`
- **Gerçek projelerden gelsin** — Sadece theory değil, production'da test edilmiş olsun

---

## Başlangıç Planı

```
Hafta 1:
☐ Repo aç, README.md yaz
☐ nextjs/development-rules.md
☐ code-quality/component-rules.md
☐ templates/PROMPT_TEMPLATE.md

Hafta 2:
☐ nextjs/feature-rules.md
☐ code-quality/typescript-rules.md
☐ state-and-data/api-rules.md

Sonrası:
☐ Yeni projede bir şey öğrenince → rules dosyasına ekle
☐ Bir prompt çok işe yarayınca → repoya koy
```

---

*Bu döküman bir konuşmadan damıtıldı. Yaşayan bir döküman — projeler büyüdükçe güncellenir.*
