# 📘 Documentation Backend - SUPINFO Mentor AI

## 📋 Table des matières

1. [Vue d'ensemble](#vue-densemble)
2. [Architecture](#architecture)
3. [Services](#services)
4. [API Endpoints](#api-endpoints)
5. [Base de données](#base-de-données)
6. [Configuration](#configuration)
7. [Déploiement](#déploiement)

---

## 🎯 Vue d'ensemble


![Images](./images/cycle%20de%20vie%20SUPINFO%20MentorGPT.png)  


Le backend de SUPINFO Mentor AI est une API REST construite avec **FastAPI** qui fournit :

- **RAG (Retrieval-Augmented Generation)** : Recherche sémantique dans les documents SUPINFO
- **Gestion des conversations** : Stockage et récupération des conversations utilisateur
- **Authentification** : Gestion des utilisateurs et sessions
- **Parsing de fichiers** : Support PDF, DOCX, TXT, MD, CSV
- **Gestion avancée** : Avatars, prompts, dossiers, notifications

### Stack technique

- **Framework** : FastAPI 0.115.0
- **Language** : Python 3.10+
- **RAG** : LangChain 0.3.0 + ChromaDB 0.5.0
- **LLM** : OpenAI GPT-4 / GPT-3.5
- **Database** : MySQL (production) / SQLite (développement)
- **ORM** : SQLAlchemy 2.0.36
- **Auth** : bcrypt 4.1.2

---

## 🏗️ Architecture

### Structure des fichiers

```
backend/
├── main.py                 # Point d'entrée FastAPI
├── database.py             # Modèles SQLAlchemy
├── requirements.txt        # Dépendances Python
│
├── services/               # Services métier
│   ├── rag_service.py     # Service RAG principal
│   ├── intent_analyzer.py  # Analyse d'intention
│   ├── academic_context.py # Contexte académique
│   ├── cache_service.py    # Cache des réponses
│   ├── file_parser.py      # Parsing de fichiers
│   └── title_generator.py  # Génération de titres
│
├── data/
│   └── supinfo_docs/       # Documents SUPINFO (99+ fichiers)
│
└── migrations/             # Scripts de migration DB
```

### Flux de traitement

```
Requête utilisateur
    │
    ▼
[Intent Analyzer] → Analyse l'intention
    │
    ▼
[Academic Context] → Enrichit avec contexte SUPINFO
    │
    ▼
[RAG Service] → Recherche dans ChromaDB
    │
    ▼
[LLM (OpenAI)] → Génération de réponse
    │
    ▼
[Cache Service] → Mise en cache
    │
    ▼
Réponse + Sources
```

---

## 🔧 Services

### 1. RAG Service (`rag_service.py`)

Service principal pour la recherche augmentée par génération.

**Fonctionnalités :**
- Chargement des documents depuis `data/supinfo_docs/`
- Création d'embeddings (OpenAI ou HuggingFace)
- Stockage vectoriel dans ChromaDB
- Recherche sémantique par similarité
- Génération de réponses contextuelles

**Méthodes principales :**
```python
async def initialize()  # Initialise ChromaDB et charge les documents
def search(query: str, k: int = 3)  # Recherche les k documents les plus pertinents
def generate_response(question, context_docs, intent, ai_mode, avatar_id)  # Génère la réponse
```

**Configuration :**
- `RAG_CHUNK_SIZE` : Taille des chunks (défaut: 1000)
- `RAG_CHUNK_OVERLAP` : Chevauchement (défaut: 200)
- `EMBEDDING_MODEL` : Modèle d'embedding (fallback HuggingFace)

### 2. Intent Analyzer (`intent_analyzer.py`)

Analyse l'intention de l'utilisateur pour enrichir la requête.

**Intentions détectées :**
- `programme` : Questions sur les programmes
- `admission` : Processus d'admission
- `pedagogie` : Méthodes pédagogiques
- `campus` : Campus et infrastructure
- `carriere` : Débouchés professionnels
- `handicap` : Accessibilité
- `faq` : Questions fréquentes

### 3. Academic Context Service (`academic_context.py`)

Enrichit les questions avec le contexte académique SUPINFO.

**Fonctionnalités :**
- Ajout de contexte selon l'intention
- Enrichissement avec termes académiques
- Optimisation pour la recherche RAG

### 4. Cache Service (`cache_service.py`)

Cache des réponses fréquentes pour améliorer les performances.

**Fonctionnalités :**
- Cache en mémoire (dictionnaire Python)
- Normalisation des questions
- Expiration automatique (configurable)

### 5. File Parser (`file_parser.py`)

Parse les fichiers uploadés par les utilisateurs.

**Formats supportés :**
- **PDF** : `pypdf`
- **DOCX** : `python-docx`
- **TXT/MD** : Lecture directe
- **CSV** : Parsing avec pandas

---

## 🌐 API Endpoints

### Chat

#### `POST /api/chat`

Endpoint principal pour le chat avec l'IA.

**Request :**
```json
{
  "message": "Quels sont les programmes proposés ?",
  "conversation_id": "optional-id",
  "user_id": "user-id",
  "user_email": "user@example.com",
  "ai_mode": "fast",
  "deep_search": false,
  "think_mode": false,
  "file_content": "optional-file-content",
  "avatar_id": "optional-avatar-id"
}
```

**Response :**
```json
{
  "response": "SUPINFO propose des programmes...",
  "conversation_id": "conv-123",
  "sources": [
    {
      "title": "Programmes Bachelor",
      "url": "https://..."
    }
  ],
  "intent": "programme",
  "conversation_title": "Programmes SUPINFO",
  "avatar_id": "avatar-123"
}
```

### Authentification

#### `POST /api/auth/register`

Inscription manuelle d'un utilisateur.

**Request :**
```json
{
  "email": "user@example.com",
  "password": "secure-password",
  "first_name": "John",
  "last_name": "Doe"
}
```

#### `POST /api/auth/login`

Connexion avec email/password.

**Request :**
```json
{
  "email": "user@example.com",
  "password": "secure-password"
}
```

#### `POST /api/auth/forgot-password`

Demande de réinitialisation de mot de passe.

#### `POST /api/auth/reset-password`

Réinitialisation du mot de passe avec token.

### Conversations

#### `GET /api/conversations`

Récupère toutes les conversations d'un utilisateur.

**Query params :**
- `user_id` : ID de l'utilisateur
- `archived` : Filtrer par statut (0/1)

#### `GET /api/conversations/{id}`

Récupère une conversation spécifique avec ses messages.

#### `POST /api/conversations`

Crée une nouvelle conversation.

#### `PUT /api/conversations/{id}/title`

Met à jour le titre d'une conversation.

#### `DELETE /api/conversations/{id}`

Supprime une conversation.

#### `PUT /api/conversations/{id}/archive`

Archive/désarchive une conversation.

#### `POST /api/conversations/{id}/duplicate`

Duplique une conversation.

#### `POST /api/conversations/merge`

Fusionne plusieurs conversations.

#### `POST /api/conversations/{id}/move-to-folder`

Déplace une conversation vers un dossier.

#### `POST /api/conversations/{id}/export`

Exporte une conversation (JSON/Markdown/TXT).

### Avatars

#### `GET /api/avatars`

Récupère tous les avatars d'un utilisateur.

#### `POST /api/avatars`

Crée un nouvel avatar.

#### `PUT /api/avatars/{id}`

Met à jour un avatar.

#### `DELETE /api/avatars/{id}`

Supprime un avatar.

### Prompts

#### `GET /api/prompts`

Récupère tous les prompts d'un utilisateur.

#### `POST /api/prompts`

Crée un nouveau prompt.

#### `PUT /api/prompts/{id}`

Met à jour un prompt.

#### `DELETE /api/prompts/{id}`

Supprime un prompt.

#### `POST /api/prompts/{id}/increment-usage`

Incrémente le compteur d'utilisation d'un prompt.

### Dossiers

#### `GET /api/folders`

Récupère tous les dossiers d'un utilisateur.

#### `POST /api/folders`

Crée un nouveau dossier.

#### `PUT /api/folders/{id}`

Met à jour un dossier.

#### `DELETE /api/folders/{id}`

Supprime un dossier.

#### `GET /api/folders/{id}/conversations`

Récupère les conversations d'un dossier.

#### `POST /api/folders/{id}/conversations`

Ajoute une conversation à un dossier.

#### `DELETE /api/folders/{id}/conversations/{conversation_id}`

Retire une conversation d'un dossier.

### Fichiers

#### `POST /api/uploadfile`

Upload et parse un fichier (PDF, DOCX, TXT, MD, CSV).

**Request :** `multipart/form-data` avec champ `file`

**Response :**
```json
{
  "filename": "document.pdf",
  "content": "Contenu parsé...",
  "message": "Fichier parsé avec succès"
}
```

### Admin

#### `GET /api/admin/users`

Récupère la liste des utilisateurs (admin uniquement).

#### `GET /api/admin/conversations`

Récupère toutes les conversations (admin uniquement).

#### `GET /api/admin/notifications`

Récupère toutes les notifications.

#### `POST /api/admin/notifications`

Crée une nouvelle notification.

#### `PUT /api/admin/notifications/{id}`

Met à jour une notification.

#### `DELETE /api/admin/notifications/{id}`

Supprime une notification.

---

## 🗄️ Base de données

### Modèles SQLAlchemy

#### User

```python
class User(Base):
    id: str (PK)
    email: str (unique)
    name: str
    first_name: str (nullable)
    last_name: str (nullable)
    password_hash: str (nullable)
    provider: str  # google, github, azure-ad, credentials
    reset_token: str (nullable)
    reset_token_expires: datetime (nullable)
    created_at: datetime
```

#### Conversation

```python
class Conversation(Base):
    id: str (PK)
    user_id: str (FK -> User)
    title: str
    archived: int (0/1)
    avatar_id: str (nullable)
    created_at: datetime
    updated_at: datetime
```

#### Message

```python
class Message(Base):
    id: str (PK)
    conversation_id: str (FK -> Conversation)
    content: str
    sender: str  # 'user' | 'ai'
    created_at: datetime
```

#### Folder

```python
class Folder(Base):
    id: str (PK)
    user_id: str (FK -> User)
    name: str
    color: str (nullable)
    icon: str (nullable)
    created_at: datetime
    updated_at: datetime
```

#### FolderConversation

```python
class FolderConversation(Base):
    id: str (PK)
    folder_id: str (FK -> Folder)
    conversation_id: str (FK -> Conversation)
    created_at: datetime
    # Unique constraint: (folder_id, conversation_id)
```

### Migration automatique

Le système détecte automatiquement les colonnes manquantes et les ajoute :

- `users.reset_token`
- `users.reset_token_expires`
- `conversations.archived`
- `conversations.avatar_id`
- Tables `folders` et `folder_conversations`

---

## ⚙️ Configuration

### Variables d'environnement

Créer un fichier `.env` dans `backend/` :

```env
# OpenAI
OPENAI_API_KEY=sk-...

# Database
DATABASE_URL=sqlite:///./supinfo_mentor.db
# Ou pour MySQL:
# DATABASE_URL=mysql+pymysql://user:password@host:port/database

# CORS
ALLOWED_ORIGINS=http://localhost:3000,https://your-frontend.vercel.app

# RAG
RAG_CHUNK_SIZE=1000
RAG_CHUNK_OVERLAP=200
EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2

# Admin
ADMIN_EMAILS=admin1@example.com,admin2@example.com
```

### Configuration CORS

Les origines autorisées sont nettoyées automatiquement :
- Espaces supprimés
- Slashes finaux supprimés
- Valeurs vides filtrées

---

## 🚀 Déploiement

### Railway

1. **Créer un projet Railway**
2. **Connecter le repository GitHub**
3. **Configurer :**
   - Root Directory : `backend`
   - Start Command : `uvicorn main:app --host 0.0.0.0 --port $PORT`
4. **Ajouter les variables d'environnement**

### Variables Railway requises

- `OPENAI_API_KEY`
- `DATABASE_URL` (MySQL)
- `ALLOWED_ORIGINS`
- `ADMIN_EMAILS`

### Health Check

```bash
curl https://your-backend.railway.app/health
```

---

## 📝 Logs et Debugging

### Logs FastAPI

Les logs apparaissent dans la console :
- `INFO` : Informations générales
- `WARNING` : Avertissements
- `ERROR` : Erreurs

### Debug RAG

Activer les logs détaillés :
```python
import logging
logging.getLogger("rag_service").setLevel(logging.DEBUG)
```

### Vérifier ChromaDB

```python
from services.rag_service import RAGService
rag = RAGService()
await rag.initialize()
# Vérifier le nombre de documents chargés
```

---

## 🔒 Sécurité

### Authentification

- **OAuth** : Google, GitHub, Microsoft
- **Credentials** : Email/password avec bcrypt
- **Tokens** : JWT via NextAuth

### Validation

- **Pydantic** : Validation des données d'entrée
- **EmailStr** : Validation des emails
- **Sanitization** : Nettoyage des inputs

### CORS

- Origines strictement contrôlées
- Headers autorisés configurés
- Credentials supportés

---

## 📚 Ressources

- [Documentation FastAPI](https://fastapi.tiangolo.com/)
- [Documentation LangChain](https://python.langchain.com/)
- [Documentation ChromaDB](https://docs.trychroma.com/)
- [Documentation SQLAlchemy](https://docs.sqlalchemy.org/)

---

<div align="center">

**Backend SUPINFO Mentor AI** • [Retour au README](../README.md)

</div>
