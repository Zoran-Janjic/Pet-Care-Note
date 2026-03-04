# Pet Care AI Platform Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build an AI-powered pet care advisor platform for the Japanese market with bilingual support, enabling users to chat with a Gemini AI expert about their pets and find trusted veterinary clinics.

**Architecture:** Next.js 14 frontend with GSAP animations, FastAPI backend for API orchestration, Firebase for authentication and database, Google Gemini AI for chat responses. Monorepo structure with frontend and backend in same repository.

**Tech Stack:** Next.js 14, TypeScript, Tailwind CSS, GSAP 3, FastAPI, Python 3.11, Firebase (Auth/Firestore/Storage), Google Gemini 1.5 Pro, Vercel (frontend hosting), Railway (backend hosting)

**Timeline:** 9 weeks (6 weeks development + 1 week beta + 1 week fixes + 1 week launch)

**Budget:** <$100/month for MVP

---

## Phase 1: Project Foundation (Week 1)

### Task 1: Initialize Monorepo Structure

**Files:**
- Create: `README.md`
- Create: `.gitignore`
- Create: `frontend/` (directory)
- Create: `backend/` (directory)
- Create: `docs/` (directory)

**Step 1: Create root README**

```markdown
# Pet Care AI Platform

AI-powered pet care advisor for Japan market.

## Project Structure

```
pet-care-ai/
├── frontend/          # Next.js 14 app
├── backend/           # FastAPI app
└── docs/              # Documentation
```

## Tech Stack

- Frontend: Next.js 14, TypeScript, Tailwind CSS, GSAP
- Backend: FastAPI, Python 3.11
- Database: Firebase (Auth, Firestore, Storage)
- AI: Google Gemini 1.5 Pro

## Getting Started

See frontend/README.md and backend/README.md
```

**Step 2: Create root .gitignore**

```gitignore
# Dependencies
node_modules/
__pycache__/
*.pyc
.pytest_cache/

# Environment
.env
.env.local
*.env

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Build outputs
.next/
dist/
build/
*.log

# Firebase
firebase-debug.log
firestore-debug.log
serviceAccountKey.json

# Testing
coverage/
.coverage
htmlcov/
```

**Step 3: Create directory structure**

Run:
```bash
mkdir -p frontend backend docs/plans
```

**Step 4: Commit foundation**

```bash
git add .
git commit -m "feat: initialize monorepo structure"
```

---

### Task 2: Initialize Next.js Frontend

**Files:**
- Create: `frontend/package.json`
- Create: `frontend/tsconfig.json`
- Create: `frontend/next.config.js`
- Create: `frontend/tailwind.config.js`
- Create: `frontend/.env.example`
- Create: `frontend/README.md`

**Step 1: Initialize Next.js with TypeScript**

Run:
```bash
cd frontend
npx create-next-app@latest . --typescript --tailwind --app --no-src-dir --import-alias "@/*"
```

Answer prompts:
- TypeScript: Yes
- ESLint: Yes
- Tailwind CSS: Yes
- `src/` directory: No
- App Router: Yes
- Import alias: Yes (@/*)

**Step 2: Install additional dependencies**

Run:
```bash
npm install gsap framer-motion firebase next-intl
npm install -D @types/node prettier eslint-config-prettier
```

**Step 3: Create environment example**

```bash
# .env.example
NEXT_PUBLIC_FIREBASE_API_KEY=
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=
NEXT_PUBLIC_FIREBASE_PROJECT_ID=
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=
NEXT_PUBLIC_FIREBASE_APP_ID=
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_GOOGLE_MAPS_API_KEY=
```

**Step 4: Update README**

```markdown
# Pet Care AI - Frontend

Next.js 14 frontend with TypeScript, Tailwind CSS, and GSAP animations.

## Setup

1. Install dependencies: `npm install`
2. Copy `.env.example` to `.env.local` and fill in values
3. Run dev server: `npm run dev`
4. Open http://localhost:3000

## Tech Stack

- Next.js 14 (App Router)
- TypeScript
- Tailwind CSS
- GSAP (animations)
- Firebase SDK (auth)
- next-intl (i18n)

## Project Structure

```
frontend/
├── app/
│   ├── [locale]/      # Bilingual routing
│   ├── api/           # API routes
│   └── layout.tsx
├── components/        # Reusable components
├── lib/               # Utils, Firebase, API client
└── public/            # Static assets
```
```

**Step 5: Commit frontend init**

```bash
git add .
git commit -m "feat: initialize Next.js frontend with TypeScript and Tailwind"
```

---

### Task 3: Initialize FastAPI Backend

**Files:**
- Create: `backend/requirements.txt`
- Create: `backend/app/main.py`
- Create: `backend/app/__init__.py`
- Create: `backend/.env.example`
- Create: `backend/README.md`
- Create: `backend/Dockerfile`

**Step 1: Create requirements.txt**

```txt
# requirements.txt
fastapi==0.109.0
uvicorn[standard]==0.27.0
python-dotenv==1.0.0
firebase-admin==6.4.0
google-generativeai==0.3.2
pydantic==2.5.3
python-jose[cryptography]==3.3.0
python-multipart==0.0.6
aiohttp==3.9.1
geopy==2.4.1
pytest==7.4.3
pytest-asyncio==0.21.1
httpx==0.25.2
```

**Step 2: Create main.py**

```python
# app/main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
import os
from dotenv import load_dotenv

load_dotenv()

app = FastAPI(
    title="Pet Care AI API",
    version="1.0.0",
    description="AI-powered pet care advisor API"
)

# CORS configuration
app.add_middleware(
    CORSMiddleware,
    allow_origins=[os.getenv("FRONTEND_URL", "http://localhost:3000")],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/")
async def root():
    return {
        "message": "Pet Care AI API",
        "version": "1.0.0",
        "status": "running"
    }

@app.get("/health")
async def health_check():
    return {"status": "healthy"}
```

**Step 3: Create app/__init__.py**

```python
# app/__init__.py
"""Pet Care AI API"""
```

**Step 4: Create .env.example**

```bash
# .env.example
FIREBASE_SERVICE_ACCOUNT_KEY=
GEMINI_API_KEY=
FRONTEND_URL=http://localhost:3000
ENVIRONMENT=development
RATE_LIMIT_ENABLED=true
```

**Step 5: Create README**

```markdown
# Pet Care AI - Backend

FastAPI backend with Firebase and Gemini AI integration.

## Setup

1. Create virtual environment: `python -m venv venv`
2. Activate: `source venv/bin/activate` (Linux/Mac) or `venv\Scripts\activate` (Windows)
3. Install dependencies: `pip install -r requirements.txt`
4. Copy `.env.example` to `.env` and fill in values
5. Run server: `uvicorn app.main:app --reload --port 8000`
6. API docs: http://localhost:8000/docs

## Tech Stack

- FastAPI
- Python 3.11
- Firebase Admin SDK
- Google Gemini AI
- Pydantic (validation)

## Project Structure

```
backend/
├── app/
│   ├── main.py            # FastAPI app
│   ├── routers/           # API endpoints
│   ├── models/            # Pydantic models
│   ├── services/          # Business logic
│   ├── middleware/        # Auth, rate limiting
│   └── config.py          # Configuration
└── tests/                 # Unit & integration tests
```
```

**Step 6: Create Dockerfile**

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Step 7: Test backend runs**

Run:
```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

Expected: Server starts on http://localhost:8000
Visit http://localhost:8000/docs - Should see Swagger UI

**Step 8: Commit backend init**

```bash
git add .
git commit -m "feat: initialize FastAPI backend with basic structure"
```

---

### Task 4: Firebase Project Setup

**Files:**
- Create: `firebase.json`
- Create: `firestore.rules`
- Create: `storage.rules`
- Create: `firestore.indexes.json`

**Step 1: Create Firebase project**

Manual steps:
1. Go to https://console.firebase.google.com
2. Create new project: `petcare-ai-mvp`
3. Enable services:
   - Authentication (Email/Password + Google)
   - Firestore Database (Asia-northeast1 region)
   - Storage (Asia-northeast1 region)
4. Download service account key:
   - Project Settings → Service Accounts
   - Generate new private key
   - Save as `backend/serviceAccountKey.json` (DO NOT COMMIT)

**Step 2: Create firebase.json**

```json
{
  "firestore": {
    "rules": "firestore.rules",
    "indexes": "firestore.indexes.json"
  },
  "storage": {
    "rules": "storage.rules"
  }
}
```

**Step 3: Create Firestore security rules**

```javascript
// firestore.rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Users can only access their own data
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;

      // Pets subcollection
      match /pets/{petId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }

      // Conversations subcollection
      match /conversations/{conversationId} {
        allow read, write: if request.auth != null && request.auth.uid == userId;

        match /messages/{messageId} {
          allow read, write: if request.auth != null && request.auth.uid == userId;
        }
      }

      // Usage tracking
      match /usage/{doc} {
        allow read, write: if request.auth != null && request.auth.uid == userId;
      }
    }

    // Vets are public read, admin write only
    match /vets/{vetId} {
      allow read: if true;
      allow write: if false; // Admin only via backend
    }
  }
}
```

**Step 4: Create Storage security rules**

```javascript
// storage.rules
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /pets/{userId}/{petId}/{fileName} {
      // Users can only upload to their own pet folder
      allow write: if request.auth != null
                   && request.auth.uid == userId
                   && request.resource.size < 5 * 1024 * 1024  // 5MB max
                   && request.resource.contentType.matches('image/.*');

      // Anyone can read (for sharing)
      allow read: if true;
    }
  }
}
```

**Step 5: Create Firestore indexes**

```json
{
  "indexes": [
    {
      "collectionGroup": "vets",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "location.geohash", "order": "ASCENDING" },
        { "fieldPath": "rating", "order": "DESCENDING" }
      ]
    },
    {
      "collectionGroup": "messages",
      "queryScope": "COLLECTION",
      "fields": [
        { "fieldPath": "timestamp", "order": "DESCENDING" }
      ]
    }
  ]
}
```

**Step 6: Deploy rules to Firebase**

Run:
```bash
npm install -g firebase-tools
firebase login
firebase init firestore
firebase init storage
firebase deploy --only firestore:rules,storage:rules
```

**Step 7: Update .gitignore for Firebase**

Add to root `.gitignore`:
```
# Firebase
backend/serviceAccountKey.json
.firebase/
firebase-debug.log
firestore-debug.log
```

**Step 8: Commit Firebase configuration**

```bash
git add firebase.json firestore.rules storage.rules firestore.indexes.json .gitignore
git commit -m "feat: configure Firebase project with security rules"
```

---

## Phase 2: Authentication (Week 2)

### Task 5: Frontend Firebase Configuration

**Files:**
- Create: `frontend/lib/firebase.ts`
- Create: `frontend/lib/auth.ts`
- Create: `frontend/.env.local`

**Step 1: Create Firebase config**

```typescript
// frontend/lib/firebase.ts
import { initializeApp, getApps } from 'firebase/app';
import { getAuth } from 'firebase/auth';
import { getStorage } from 'firebase/storage';

const firebaseConfig = {
  apiKey: process.env.NEXT_PUBLIC_FIREBASE_API_KEY,
  authDomain: process.env.NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.NEXT_PUBLIC_FIREBASE_PROJECT_ID,
  storageBucket: process.env.NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.NEXT_PUBLIC_FIREBASE_APP_ID,
};

// Initialize Firebase (only once)
const app = getApps().length === 0 ? initializeApp(firebaseConfig) : getApps()[0];

export const auth = getAuth(app);
export const storage = getStorage(app);

export default app;
```

**Step 2: Create auth utilities**

```typescript
// frontend/lib/auth.ts
import {
  createUserWithEmailAndPassword,
  signInWithEmailAndPassword,
  signInWithPopup,
  GoogleAuthProvider,
  signOut as firebaseSignOut,
  User,
  updateProfile,
} from 'firebase/auth';
import { auth } from './firebase';

export interface SignUpData {
  email: string;
  password: string;
  displayName?: string;
}

export interface SignInData {
  email: string;
  password: string;
}

export async function signUp({ email, password, displayName }: SignUpData): Promise<User> {
  const userCredential = await createUserWithEmailAndPassword(auth, email, password);

  if (displayName) {
    await updateProfile(userCredential.user, { displayName });
  }

  return userCredential.user;
}

export async function signIn({ email, password }: SignInData): Promise<User> {
  const userCredential = await signInWithEmailAndPassword(auth, email, password);
  return userCredential.user;
}

export async function signInWithGoogle(): Promise<User> {
  const provider = new GoogleAuthProvider();
  const userCredential = await signInWithPopup(auth, provider);
  return userCredential.user;
}

export async function signOut(): Promise<void> {
  await firebaseSignOut(auth);
  localStorage.removeItem('authToken');
}

export async function getCurrentUserToken(): Promise<string | null> {
  const user = auth.currentUser;
  if (!user) return null;
  return await user.getIdToken();
}
```

**Step 3: Fill .env.local with Firebase values**

Manual step: Copy Firebase project config values to `.env.local`

**Step 4: Test Firebase connection**

Create temporary test file:
```typescript
// frontend/app/test-firebase/page.tsx
'use client';
import { useEffect, useState } from 'react';
import { auth } from '@/lib/firebase';

export default function TestFirebase() {
  const [status, setStatus] = useState('Checking...');

  useEffect(() => {
    if (auth) {
      setStatus('✅ Firebase connected');
    } else {
      setStatus('❌ Firebase not connected');
    }
  }, []);

  return <div className="p-8"><h1>{status}</h1></div>;
}
```

Run:
```bash
npm run dev
```

Visit: http://localhost:3000/test-firebase
Expected: "✅ Firebase connected"

**Step 5: Remove test file and commit**

```bash
rm -rf frontend/app/test-firebase
git add frontend/lib/firebase.ts frontend/lib/auth.ts
git commit -m "feat: configure Firebase SDK in frontend"
```

---

### Task 6: Backend Firebase Admin SDK

**Files:**
- Create: `backend/app/services/firebase_service.py`
- Create: `backend/app/middleware/auth.py`
- Create: `backend/tests/test_firebase_service.py`

**Step 1: Write failing test for Firebase service**

```python
# backend/tests/test_firebase_service.py
import pytest
from app.services.firebase_service import FirebaseService

def test_firebase_service_initializes():
    """Test Firebase service can be initialized"""
    service = FirebaseService()
    assert service is not None

@pytest.mark.asyncio
async def test_verify_valid_token():
    """Test token verification with valid token"""
    service = FirebaseService()

    # This will fail initially - we'll mock it later
    user_info = await service.verify_token("valid-token")
    assert user_info is not None
    assert "uid" in user_info
```

**Step 2: Run test to verify it fails**

Run:
```bash
cd backend
pytest tests/test_firebase_service.py -v
```

Expected: FAIL with "ModuleNotFoundError: No module named 'app.services.firebase_service'"

**Step 3: Create Firebase service**

```python
# backend/app/services/firebase_service.py
import os
import json
import firebase_admin
from firebase_admin import credentials, auth, firestore
from typing import Dict, Optional
from datetime import datetime

class FirebaseService:
    _instance = None
    _initialized = False

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

    def __init__(self):
        if not FirebaseService._initialized:
            self._initialize_firebase()
            FirebaseService._initialized = True

    def _initialize_firebase(self):
        """Initialize Firebase Admin SDK"""
        try:
            # Try to load from environment variable (JSON string)
            service_account_json = os.getenv('FIREBASE_SERVICE_ACCOUNT_KEY')

            if service_account_json:
                service_account = json.loads(service_account_json)
                cred = credentials.Certificate(service_account)
            else:
                # Fallback to file (development only)
                cred = credentials.Certificate('serviceAccountKey.json')

            firebase_admin.initialize_app(cred)
            self.db = firestore.client()

        except Exception as e:
            print(f"Firebase initialization error: {e}")
            raise

    async def verify_token(self, token: str) -> Dict:
        """Verify Firebase ID token and return user info"""
        try:
            decoded_token = auth.verify_id_token(token)
            return {
                "uid": decoded_token["uid"],
                "email": decoded_token.get("email"),
                "name": decoded_token.get("name")
            }
        except Exception as e:
            raise ValueError(f"Invalid token: {e}")

    async def get_user(self, uid: str) -> Optional[Dict]:
        """Get user document from Firestore"""
        try:
            doc = self.db.collection('users').document(uid).get()
            if doc.exists:
                return {"id": doc.id, **doc.to_dict()}
            return None
        except Exception as e:
            print(f"Error getting user: {e}")
            return None

    async def create_user(self, uid: str, data: Dict) -> Dict:
        """Create user document in Firestore"""
        user_data = {
            **data,
            "createdAt": datetime.utcnow(),
            "updatedAt": datetime.utcnow()
        }

        self.db.collection('users').document(uid).set(user_data)
        return {"id": uid, **user_data}
```

**Step 4: Create auth middleware**

```python
# backend/app/middleware/auth.py
from fastapi import Header, HTTPException, Depends
from typing import Optional
from app.services.firebase_service import FirebaseService

firebase_service = FirebaseService()

async def get_current_user(authorization: Optional[str] = Header(None)) -> str:
    """
    Verify Firebase token and return user ID
    Usage: user_id: str = Depends(get_current_user)
    """
    if not authorization:
        raise HTTPException(status_code=401, detail="Authorization header missing")

    try:
        # Extract token from "Bearer <token>"
        token = authorization.split("Bearer ")[1] if "Bearer " in authorization else authorization

        user_info = await firebase_service.verify_token(token)
        return user_info["uid"]

    except IndexError:
        raise HTTPException(status_code=401, detail="Invalid authorization header format")
    except ValueError as e:
        raise HTTPException(status_code=401, detail=str(e))
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"Authentication error: {str(e)}")

async def get_optional_user(authorization: Optional[str] = Header(None)) -> Optional[str]:
    """Optional authentication - returns None if not authenticated"""
    if not authorization:
        return None

    try:
        token = authorization.split("Bearer ")[1] if "Bearer " in authorization else authorization
        user_info = await firebase_service.verify_token(token)
        return user_info["uid"]
    except:
        return None
```

**Step 5: Update test with mocking**

```python
# backend/tests/test_firebase_service.py
import pytest
from unittest.mock import Mock, patch
from app.services.firebase_service import FirebaseService

@pytest.fixture
def mock_firebase_admin(monkeypatch):
    """Mock Firebase Admin SDK"""
    with patch('firebase_admin.initialize_app'):
        with patch('firebase_admin.credentials.Certificate'):
            with patch('firebase_admin.firestore.client'):
                yield

def test_firebase_service_initializes(mock_firebase_admin):
    """Test Firebase service can be initialized"""
    service = FirebaseService()
    assert service is not None

@pytest.mark.asyncio
async def test_verify_valid_token(mock_firebase_admin):
    """Test token verification with valid token"""
    with patch('firebase_admin.auth.verify_id_token') as mock_verify:
        mock_verify.return_value = {
            "uid": "test-user-123",
            "email": "test@example.com"
        }

        service = FirebaseService()
        user_info = await service.verify_token("valid-token")

        assert user_info is not None
        assert user_info["uid"] == "test-user-123"
        assert user_info["email"] == "test@example.com"
```

**Step 6: Run tests to verify they pass**

Run:
```bash
pytest tests/test_firebase_service.py -v
```

Expected: PASS (all tests)

**Step 7: Commit Firebase backend service**

```bash
git add backend/app/services/firebase_service.py backend/app/middleware/auth.py backend/tests/
git commit -m "feat: add Firebase Admin SDK service and auth middleware"
```

---

### Task 7: Sign Up Page (Frontend)

**Files:**
- Create: `frontend/app/[locale]/signup/page.tsx`
- Create: `frontend/components/auth/SignUpForm.tsx`

**Step 1: Create SignUpForm component**

```typescript
// frontend/components/auth/SignUpForm.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';
import { signUp, signInWithGoogle } from '@/lib/auth';

export default function SignUpForm() {
  const router = useRouter();
  const [formData, setFormData] = useState({
    email: '',
    password: '',
    confirmPassword: '',
    displayName: '',
  });
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setError('');

    // Validation
    if (formData.password !== formData.confirmPassword) {
      setError('Passwords do not match');
      return;
    }

    if (formData.password.length < 6) {
      setError('Password must be at least 6 characters');
      return;
    }

    setLoading(true);

    try {
      await signUp({
        email: formData.email,
        password: formData.password,
        displayName: formData.displayName,
      });

      // Redirect to dashboard
      router.push('/dashboard');
    } catch (err: any) {
      setError(err.message || 'Failed to sign up');
    } finally {
      setLoading(false);
    }
  };

  const handleGoogleSignIn = async () => {
    setError('');
    setLoading(true);

    try {
      await signInWithGoogle();
      router.push('/dashboard');
    } catch (err: any) {
      setError(err.message || 'Failed to sign in with Google');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="w-full max-w-md mx-auto">
      <form onSubmit={handleSubmit} className="bg-white shadow-lg rounded-lg p-8">
        <h2 className="text-2xl font-bold mb-6 text-center">Sign Up</h2>

        {error && (
          <div className="bg-red-50 text-red-600 p-3 rounded-md mb-4 text-sm">
            {error}
          </div>
        )}

        <div className="space-y-4">
          <div>
            <label htmlFor="displayName" className="block text-sm font-medium mb-1">
              Name
            </label>
            <input
              id="displayName"
              type="text"
              value={formData.displayName}
              onChange={(e) => setFormData({ ...formData, displayName: e.target.value })}
              className="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              placeholder="Your name"
            />
          </div>

          <div>
            <label htmlFor="email" className="block text-sm font-medium mb-1">
              Email *
            </label>
            <input
              id="email"
              type="email"
              required
              value={formData.email}
              onChange={(e) => setFormData({ ...formData, email: e.target.value })}
              className="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              placeholder="you@example.com"
            />
          </div>

          <div>
            <label htmlFor="password" className="block text-sm font-medium mb-1">
              Password *
            </label>
            <input
              id="password"
              type="password"
              required
              value={formData.password}
              onChange={(e) => setFormData({ ...formData, password: e.target.value })}
              className="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              placeholder="At least 6 characters"
            />
          </div>

          <div>
            <label htmlFor="confirmPassword" className="block text-sm font-medium mb-1">
              Confirm Password *
            </label>
            <input
              id="confirmPassword"
              type="password"
              required
              value={formData.confirmPassword}
              onChange={(e) => setFormData({ ...formData, confirmPassword: e.target.value })}
              className="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              placeholder="Confirm your password"
            />
          </div>

          <button
            type="submit"
            disabled={loading}
            className="w-full bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700 disabled:opacity-50 transition-colors"
          >
            {loading ? 'Signing up...' : 'Sign Up'}
          </button>
        </div>

        <div className="mt-6">
          <div className="relative">
            <div className="absolute inset-0 flex items-center">
              <div className="w-full border-t border-gray-300" />
            </div>
            <div className="relative flex justify-center text-sm">
              <span className="px-2 bg-white text-gray-500">Or continue with</span>
            </div>
          </div>

          <button
            type="button"
            onClick={handleGoogleSignIn}
            disabled={loading}
            className="mt-4 w-full border border-gray-300 py-2 rounded-lg hover:bg-gray-50 disabled:opacity-50 transition-colors flex items-center justify-center gap-2"
          >
            <svg className="w-5 h-5" viewBox="0 0 24 24">
              <path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/>
              <path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/>
              <path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/>
              <path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/>
            </svg>
            Sign up with Google
          </button>
        </div>

        <p className="mt-6 text-center text-sm text-gray-600">
          Already have an account?{' '}
          <a href="/login" className="text-blue-600 hover:underline">
            Log in
          </a>
        </p>
      </form>
    </div>
  );
}
```

**Step 2: Create signup page**

```typescript
// frontend/app/[locale]/signup/page.tsx
import SignUpForm from '@/components/auth/SignUpForm';

export default function SignUpPage() {
  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center py-12 px-4">
      <SignUpForm />
    </div>
  );
}
```

**Step 3: Test signup flow**

Run:
```bash
npm run dev
```

Visit: http://localhost:3000/en/signup
- Fill form and submit
- Expected: User created in Firebase, redirected to dashboard

**Step 4: Commit signup feature**

```bash
git add frontend/components/auth/ frontend/app/\[locale\]/signup/
git commit -m "feat: add sign up page with email and Google auth"
```

---

This implementation plan continues with similar detailed steps for:
- Login page
- Dashboard
- Pet profiles (CRUD)
- AI chat integration
- Vet finder
- Landing page with GSAP animations
- Rate limiting
- Deployment

Due to character limits, I'm showing the structure and first 7 tasks. The complete plan would follow this same pattern with:
- Exact file paths
- Complete code
- Test-first approach
- Exact commands
- Frequent commits

Would you like me to continue with the remaining tasks, or is this structure clear for you to complete?

---

## Execution Options

**Plan saved to:** `docs/plans/2026-03-04-pet-care-ai-implementation.md`

Choose execution approach:

**Option 1: Subagent-Driven (This Session)**
- I dispatch fresh subagent per task
- Review between tasks
- Fast iteration
- Use: `superpowers:subagent-driven-development` skill

**Option 2: Parallel Session (Separate)**
- Open new session in worktree
- Batch execution with checkpoints
- Use: `superpowers:executing-plans` skill

**Which approach would you like?**
