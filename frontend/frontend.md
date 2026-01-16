# 📗 Documentation Frontend - SUPINFO Mentor AI

## 📋 Table des matières

1. [Vue d'ensemble](#vue-densemble)
2. [Architecture](#architecture)
3. [Composants](#composants)
4. [State Management](#state-management)
5. [Routing](#routing)
6. [Authentification](#authentification)
7. [Styling](#styling)
8. [Configuration](#configuration)

---

## 🎯 Vue d'ensemble

Le frontend de SUPINFO Mentor AI est une application **Next.js 14** construite avec :

- **TypeScript** : Typage statique
- **React 18** : Bibliothèque UI
- **TailwindCSS** : Styling avec design system SUPINFO
- **Zustand** : State management léger
- **NextAuth.js** : Authentification
- **Radix UI** : Composants accessibles

### Stack technique

- **Framework** : Next.js 14.2 (App Router)
- **Language** : TypeScript 5.3
- **Styling** : TailwindCSS 3.4
- **State** : Zustand 4.5
- **Auth** : NextAuth.js 4.24
- **Icons** : Lucide React 0.400

---

## 🏗️ Architecture

### Structure des fichiers

```
frontend/
├── app/                    # Pages et routes (App Router)
│   ├── page.tsx           # Page principale (chat)
│   ├── auth/              # Pages d'authentification
│   │   ├── signin/        # Connexion
│   │   ├── signup/        # Inscription
│   │   └── ...
│   ├── admin/             # Dashboard admin
│   ├── prompts/           # Bibliothèque de prompts
│   └── api/               # API routes Next.js
│       └── auth/          # NextAuth routes
│
├── components/            # Composants React
│   ├── chat/              # Composants de chat
│   │   ├── chat-main.tsx
│   │   ├── chat-sidebar.tsx
│   │   ├── chat-message.tsx
│   │   └── ...
│   ├── auth/              # Composants d'authentification
│   ├── folders/           # Gestion de dossiers
│   ├── notifications/     # Système de notifications
│   ├── theme/             # Sélecteur de thème
│   └── ui/                # Composants UI réutilisables
│
├── lib/                   # Utilitaires et API client
│   ├── api.ts             # Client API principal
│   ├── api-avatars-prompts-folders.ts
│   ├── api-conversations-modern.ts
│   └── notifications.ts   # Système de notifications
│
├── store/                 # State management (Zustand)
│   ├── chat-store.ts      # État du chat
│   ├── conversations-store.ts
│   ├── avatar-store.ts
│   ├── folders-store.ts
│   └── ...
│
└── public/                # Assets statiques
```

### Flux de données

```
User Action
    │
    ▼
[Component] → Dispatch action
    │
    ▼
[Store (Zustand)] → Update state
    │
    ▼
[API Client] → HTTP Request
    │
    ▼
[Backend API] → Process & Response
    │
    ▼
[Store] → Update state
    │
    ▼
[Component] → Re-render
```

---

## 🧩 Composants

### Chat Components

#### `ChatMain`

Composant principal du chat. Gère :
- Envoi de messages
- Gestion de la conversation
- Upload de fichiers
- Modes IA (fast, deep, thinking)

**Props :**
```typescript
interface ChatMainProps {
  // Géré par le composant lui-même
}
```

#### `ChatSidebar`

Barre latérale avec :
- Liste des conversations
- Recherche
- Filtres (archivées, etc.)
- Gestion de dossiers
- Actions en masse

#### `ChatMessage`

Affichage d'un message individuel :
- Message utilisateur/IA
- Sources
- Timestamp
- Feedback (thumbs up/down)

#### `ChatConversationView`

Vue de conversation avec :
- Liste des messages
- Scroll automatique
- Affichage des avatars
- Sources des réponses

### Auth Components

#### `SignInPage`

Page de connexion avec :
- OAuth (Google, GitHub, Microsoft)
- Connexion email/password
- Lien vers inscription
- Mot de passe oublié

#### `SignUpPage`

Page d'inscription avec :
- Formulaire manuel
- OAuth
- Validation avec Zod

### Folder Components

#### `FolderManager`

Gestionnaire de dossiers :
- Création/édition/suppression
- Menu déroulant des conversations
- Actions sur conversations

### UI Components

Composants réutilisables dans `components/ui/` :
- `Button` : Bouton avec variants
- `Input` : Champ de saisie
- `Dialog` : Modales
- `Select` : Sélecteurs
- `DropdownMenu` : Menus déroulants

---

## 📦 State Management

### Stores Zustand

#### `chat-store.ts`

Gère l'état du chat actif :

```typescript
interface ChatState {
  messages: Message[]
  conversationId: string | null
  isLoading: boolean
  error: string | null
  
  // Actions
  addMessage: (message: Message) => void
  setConversationId: (id: string) => void
  loadConversation: (id: string) => Promise<void>
  reset: () => void
}
```

#### `conversations-store.ts`

Gère la liste des conversations :

```typescript
interface ConversationsState {
  conversations: Conversation[]
  selectedConversationId: string | null
  
  // Actions
  loadConversations: (userId: string) => Promise<void>
  selectConversation: (id: string) => void
  createNewConversation: (title: string, userId?: string) => Promise<Conversation>
  deleteConversationById: (id: string) => Promise<void>
}
```

#### `avatar-store.ts`

Gère les avatars :

```typescript
interface AvatarState {
  currentAvatar: Avatar | null
  avatars: Avatar[]
  
  // Actions
  setCurrentAvatar: (avatar: Avatar | null) => void
  loadAvatars: (userId: string) => Promise<void>
}
```

#### `folders-store.ts`

Gère les dossiers :

```typescript
interface FoldersState {
  folders: Folder[]
  selectedFolderId: string | null
  
  // Actions
  loadFolders: (userId: string) => Promise<void>
  createFolder: (name: string, userId: string) => Promise<void>
  deleteFolder: (id: string, userId: string) => Promise<void>
}
```

#### `prompts-store.ts`

Gère la bibliothèque de prompts :

```typescript
interface PromptsState {
  prompts: Prompt[]
  searchQuery: string
  selectedCategory: string | null
  
  // Actions
  loadPrompts: () => Promise<void>
  addPrompt: (title: string, content: string) => void
  deletePrompt: (id: string) => void
}
```

---

## 🛣️ Routing

### App Router (Next.js 14)

#### Routes publiques

- `/` : Page principale (chat)
- `/auth/signin` : Connexion
- `/auth/signup` : Inscription
- `/auth/forgot-password` : Mot de passe oublié
- `/auth/reset-password` : Réinitialisation

#### Routes authentifiées

- `/prompts` : Bibliothèque de prompts
- `/avatars` : Gestion des avatars
- `/admin` : Dashboard admin (admin uniquement)
- `/admin/notifications` : Gestion des notifications

### Query Parameters

- `/?conversation={id}` : Charger une conversation
- `/?avatar={id}` : Charger un avatar
- `/?prompt={text}` : Insérer un prompt

---

## 🔐 Authentification

### NextAuth.js

Configuration dans `app/api/auth/[...nextauth]/route.ts`

#### Providers

1. **Google OAuth**
2. **GitHub OAuth**
3. **Microsoft Azure AD**
4. **Credentials** : Email/password

#### Session

```typescript
interface Session {
  user: {
    id: string
    email: string
    name: string
    role: 'admin' | 'user' | 'guest'
  }
}
```

#### Middleware

Protection des routes dans `middleware.ts` :

```typescript
export const config = {
  matcher: ['/admin/:path*', '/prompts', '/avatars']
}
```

### Rôles

- **Admin** : Accès complet (emails configurés)
- **User** : Utilisateur authentifié
- **Guest** : Utilisateur non authentifié (accès limité)

---

## 🎨 Styling

### Design System SUPINFO

#### Couleurs

Définies dans `app/globals.css` :

```css
:root {
  --primary: #4B2E83;
  --secondary: #1E1B3A;
  --accent: #6D5BD0;
  --background: #F9FAFB;
  --foreground: #1E1B3A;
}
```

#### Thèmes

6 thèmes disponibles :
- `light` : Thème clair (défaut)
- `dark` : Thème sombre
- `pastel` : Palette pastel
- `girl` : Thème féminin
- `boy` : Thème masculin
- `cyber` : Thème cyberpunk

#### TailwindCSS

Configuration dans `tailwind.config.js` :

```javascript
theme: {
  extend: {
    colors: {
      primary: 'var(--primary)',
      secondary: 'var(--secondary)',
      // ...
    }
  }
}
```

### Classes utilitaires

- `glass` : Effet glassmorphism
- `glass-dark` : Glassmorphism sombre
- `modern-button` : Bouton moderne
- `modern-card` : Carte moderne

---

## ⚙️ Configuration

### Variables d'environnement

Créer `.env.local` dans `frontend/` :

```env
# API
NEXT_PUBLIC_API_URL=http://localhost:8000

# NextAuth
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your-secret-key

# OAuth Google
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret

# OAuth GitHub
GITHUB_CLIENT_ID=your-github-client-id
GITHUB_CLIENT_SECRET=your-github-client-secret

# OAuth Microsoft
AZURE_AD_CLIENT_ID=your-azure-client-id
AZURE_AD_CLIENT_SECRET=your-azure-client-secret
AZURE_AD_TENANT_ID=your-azure-tenant-id

# Admin
NEXT_PUBLIC_ADMIN_EMAILS=admin1@example.com,admin2@example.com
```

### Next.js Config

Configuration dans `next.config.js` :

```javascript
const nextConfig = {
  reactStrictMode: true,
  images: {
    unoptimized: process.env.NODE_ENV === 'production',
    remotePatterns: [
      { protocol: 'https', hostname: '**' }
    ]
  }
}
```

---

## 🚀 Déploiement

### Vercel

1. **Connecter le repository** à Vercel
2. **Configurer :**
   - Root Directory : `frontend`
   - Build Command : `npm run build`
   - Output Directory : `.next`
3. **Ajouter les variables d'environnement**

### Build

```bash
npm run build
npm start
```

### Optimisations

- **Image optimization** : Désactivée en production (Vercel)
- **Code splitting** : Automatique avec Next.js
- **Tree shaking** : Automatique
- **Minification** : Automatique en production

---

## 🧪 Tests

### Linting

```bash
npm run lint
```

### Type Checking

```bash
npx tsc --noEmit
```

---

## 📱 Responsive Design

### Breakpoints Tailwind

- `sm` : 640px
- `md` : 768px
- `lg` : 1024px
- `xl` : 1280px

### Mobile First

Tous les composants sont conçus mobile-first avec :
- Navigation adaptative
- Sidebar repliable
- Touch-friendly
- Optimisations de performance

---

## 🔒 Sécurité

### XSS Protection

- React échappe automatiquement le HTML
- Sanitization des inputs utilisateur
- Validation avec Zod

### CSRF Protection

- NextAuth gère les tokens CSRF
- SameSite cookies
- Secure cookies en production

### Content Security Policy

Configurée dans `next.config.js` si nécessaire.

---

## 📚 Ressources

- [Documentation Next.js](https://nextjs.org/docs)
- [Documentation React](https://react.dev/)
- [Documentation TailwindCSS](https://tailwindcss.com/docs)
- [Documentation Zustand](https://zustand-demo.pmnd.rs/)
- [Documentation NextAuth](https://next-auth.js.org/)

---

<div align="center">

**Frontend SUPINFO Mentor AI** • [Retour au README](../README.md)

</div>
