<div align="center">

# 🎓 SUPINFO Mentor AI

<img width="1056" height="992" alt="image" src="https://github.com/user-attachments/assets/0fb6f87b-1408-49b1-a323-db7d4654c6e9" />


**Assistant IA académique intelligent pour SUPINFO**

[![Next.js](https://img.shields.io/badge/Next.js-14.2-black?logo=next.js)](https://nextjs.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.115-green?logo=fastapi)](https://fastapi.tiangolo.com/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.3-blue?logo=typescript)](https://www.typescriptlang.org/)
[![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-Academic-purple)](LICENSE)

*Assistant IA alimenté par RAG (Retrieval-Augmented Generation) pour répondre aux questions académiques SUPINFO*

[🚀 Démarrage rapide](#-démarrage-rapide) • [📚 Documentation](#-documentation) • [🏗️ Architecture](#️-architecture) • [🔧 Configuration](#-configuration)

</div>

---

## ✨ Fonctionnalités

### 🎯 Core Features

- **💬 Chat intelligent** : Interface de conversation moderne avec support multi-thèmes
- **🔍 RAG avancé** : Recherche sémantique dans 99+ documents SUPINFO
- **🎨 Design system SUPINFO** : Interface respectant l'identité visuelle de l'école
- **👤 Gestion d'utilisateurs** : Authentification OAuth (Google, GitHub, Microsoft) + inscription manuelle
- **📁 Organisation** : Système de dossiers et bibliothèque de prompts
- **🤖 Avatars IA** : Personnalités d'assistant configurables
- **📊 Dashboard admin** : Interface d'administration complète
- **🔔 Notifications** : Système de notifications pour les utilisateurs

### 🚀 Fonctionnalités avancées

- **📄 Parsing de fichiers** : Support PDF, DOCX, TXT, MD, CSV
- **💾 Gestion de conversations** : Archivage, duplication, fusion, export
- **🔐 Réinitialisation de mot de passe** : Système complet avec tokens sécurisés
- **🌐 Mode invité** : Accès limité sans authentification
- **📱 Responsive** : Interface optimisée mobile et desktop
- **🌙 Thèmes multiples** : Light, Dark, Pastel, Girl, Boy, Cyber

---

## 🏗️ Architecture

### Vue d'ensemble

```
┌─────────────────┐         ┌─────────────────┐
│   Frontend      │         │    Backend      │
│   (Next.js)     │◄───────►│   (FastAPI)     │
│   Vercel        │   API   │   Railway       │
└─────────────────┘         └─────────────────┘
                                      │
                                      ▼
                            ┌─────────────────┐
                            │   ChromaDB      │
                            │   (Vectors)     │
                            └─────────────────┘
                                      │
                                      ▼
                            ┌─────────────────┐
                            │   MySQL/SQLite  │
                            │   (Database)    │
                            └─────────────────┘
```

### Stack technique

**Frontend**
- **Framework** : Next.js 14 (App Router)
- **Language** : TypeScript
- **Styling** : TailwindCSS + Design System SUPINFO
- **State** : Zustand
- **Auth** : NextAuth.js
- **UI Components** : Radix UI + Custom Components

**Backend**
- **Framework** : FastAPI
- **Language** : Python 3.10+
- **RAG** : LangChain + ChromaDB
- **LLM** : OpenAI GPT-4 / GPT-3.5
- **Database** : MySQL (production) / SQLite (dev)
- **Auth** : bcrypt + JWT

---

## 🚀 Démarrage rapide

### Prérequis

- **Node.js** 18+ et npm/pnpm
- **Python** 3.10+
- **Git**

### Installation

#### 1. Cloner le repository

```bash
git clone https://github.com/MEVENGUE/SUPINFO-Mentor-AI.git
cd SUPINFO-Mentor-AI
```

#### 2. Configuration Backend

```bash
cd backend
python -m venv venv

# Windows
venv\Scripts\activate

# Linux/Mac
source venv/bin/activate

pip install -r requirements.txt
```

Créer un fichier `.env` dans `backend/` :

```env
OPENAI_API_KEY=your_openai_api_key
DATABASE_URL=sqlite:///./supinfo_mentor.db
ALLOWED_ORIGINS=http://localhost:3000
```

#### 3. Configuration Frontend

```bash
cd frontend
npm install
```

Créer un fichier `.env.local` dans `frontend/` :

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your_secret_key_here

# OAuth (optionnel)
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret
AZURE_AD_CLIENT_ID=your_azure_client_id
AZURE_AD_CLIENT_SECRET=your_azure_client_secret
AZURE_AD_TENANT_ID=your_azure_tenant_id
```

#### 4. Lancer l'application

**Terminal 1 - Backend :**
```bash
cd backend
python main.py
```

**Terminal 2 - Frontend :**
```bash
cd frontend
npm run dev
```

L'application sera accessible sur :
- **Frontend** : http://localhost:3000
- **Backend API** : http://localhost:8000
- **API Docs** : http://localhost:8000/docs

---

## 📚 Documentation

### 📖 Documentation complète

- **[📘 Documentation Backend](./docs/backend.md)** : Architecture, API, services, base de données
- **[📗 Documentation Frontend](./docs/frontend.md)** : Composants, stores, routing, authentification

### 🎯 Guides rapides

- **[🔐 Configuration OAuth](./docs/GOOGLE_OAUTH_SETUP.md)** : Google, GitHub, Microsoft
- **[🚢 Déploiement](./docs/DEPLOYMENT.md)** : Vercel + Railway
- **[🗄️ Configuration MySQL](./backend/MYSQL_SETUP.md)** : Migration SQLite → MySQL

---

## 🔧 Configuration

### Variables d'environnement

#### Backend (`.env`)

| Variable | Description | Requis |
|----------|-------------|--------|
| `OPENAI_API_KEY` | Clé API OpenAI | ✅ |
| `DATABASE_URL` | URL de connexion DB | ✅ |
| `ALLOWED_ORIGINS` | Origines CORS autorisées | ✅ |
| `RAG_CHUNK_SIZE` | Taille des chunks RAG | ❌ |
| `RAG_CHUNK_OVERLAP` | Chevauchement des chunks | ❌ |

#### Frontend (`.env.local`)

| Variable | Description | Requis |
|----------|-------------|--------|
| `NEXT_PUBLIC_API_URL` | URL du backend | ✅ |
| `NEXTAUTH_URL` | URL de l'application | ✅ |
| `NEXTAUTH_SECRET` | Secret NextAuth | ✅ |
| `GOOGLE_CLIENT_ID` | OAuth Google | ❌ |
| `GITHUB_CLIENT_ID` | OAuth GitHub | ❌ |
| `AZURE_AD_CLIENT_ID` | OAuth Microsoft | ❌ |

---

## 📁 Structure du projet

```
SUPINFO-Mentor-AI/
├── 📂 frontend/              # Application Next.js
│   ├── app/                  # Pages et routes
│   ├── components/           # Composants React
│   │   ├── chat/            # Composants de chat
│   │   ├── auth/            # Authentification
│   │   ├── folders/         # Gestion de dossiers
│   │   └── ui/              # Composants UI
│   ├── lib/                 # Utilitaires et API client
│   ├── store/               # State management (Zustand)
│   └── public/              # Assets statiques
│
├── 📂 backend/               # API FastAPI
│   ├── services/            # Services métier
│   │   ├── rag_service.py   # Service RAG
│   │   ├── intent_analyzer.py
│   │   └── ...
│   ├── data/                # Documents SUPINFO
│   │   └── supinfo_docs/    # 99+ documents Markdown
│   ├── database.py          # Modèles SQLAlchemy
│   └── main.py              # Point d'entrée API
│
└── 📂 docs/                  # Documentation
    ├── backend.md           # Documentation backend
    └── frontend.md          # Documentation frontend
```

---

## 🎨 Design System

### Couleurs SUPINFO

| Couleur | Hex | Usage |
|---------|-----|-------|
| Primaire | `#4B2E83` | Boutons, liens, accents |
| Secondaire | `#1E1B3A` | Textes importants |
| Fond | `#F9FAFB` | Arrière-plans |
| Accent | `#6D5BD0` | Highlights |

### Thèmes disponibles

- **Light** : Thème clair par défaut
- **Dark** : Thème sombre
- **Pastel** : Palette pastel douce
- **Girl** : Thème féminin
- **Boy** : Thème masculin
- **Cyber** : Thème cyberpunk

---

## 🚢 Déploiement

### Frontend (Vercel)

1. Connecter le repository GitHub à Vercel
2. Configurer :
   - **Root Directory** : `frontend`
   - **Build Command** : `npm run build`
   - **Output Directory** : `.next`
3. Ajouter les variables d'environnement

### Backend (Railway)

1. Créer un nouveau projet Railway
2. Connecter le repository
3. Configurer :
   - **Root Directory** : `backend`
   - **Start Command** : `uvicorn main:app --host 0.0.0.0 --port $PORT`
4. Ajouter les variables d'environnement

📖 **[Guide de déploiement complet](./docs/DEPLOYMENT.md)**

---

## 🧪 Tests

### Backend

```bash
cd backend
pytest tests/
```

### Frontend

```bash
cd frontend
npm run test
```

---

## 🤝 Contribution

Ce projet est développé dans le cadre d'un projet académique SUPINFO.

### Workflow

1. Fork le projet
2. Créer une branche (`git checkout -b feature/AmazingFeature`)
3. Commit les changements (`git commit -m 'Add AmazingFeature'`)
4. Push vers la branche (`git push origin feature/AmazingFeature`)
5. Ouvrir une Pull Request

---

## 📄 Licence

Ce projet est un projet académique pour SUPINFO.

---

## 👥 Auteurs

- **MEVENGUE Franck** - *Développeur principal*

---

## 🙏 Remerciements

- SUPINFO pour l'accès aux données académiques
- La communauté open-source pour les outils utilisés
- Tous les contributeurs du projet

---

<div align="center">

**Développé avec ❤️ pour SUPINFO**

[⬆ Retour en haut](#-supinfo-mentor-ai)

</div>
