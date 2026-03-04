# Pet Care AI Platform - Design Document

**Date:** March 4, 2026
**Status:** Approved
**Target Market:** Japan (bilingual EN/JA)
**Goal:** MVP to test product-market fit with 100 users in Month 1

---

## Executive Summary

Transform the existing Pet-Care-Note Bootstrap template into a modern AI-powered pet care advisor platform. Users can create pet profiles, chat with an AI veterinary assistant (powered by Google Gemini), and find trusted veterinary clinics with intelligent recommendations.

**Key Decision:** Start fresh with modern tech stack (Next.js + FastAPI + Firebase + Gemini) rather than modernizing 8-year-old template.

---

## 1. Architecture & Tech Stack

### Core Technologies

**Frontend:**
- Next.js 14 (App Router) - React framework with SSR
- TypeScript - Type safety
- Tailwind CSS - Utility-first styling
- GSAP 3 - Smooth animations
- Framer Motion - Micro-interactions
- next-intl - Bilingual Japanese/English routing
- Firebase Client SDK - Authentication

**Backend:**
- FastAPI (Python 3.11+) - REST API
- Firebase Admin SDK - Auth verification & database operations
- Google Gemini AI SDK - AI chat functionality
- Pydantic - Data validation & serialization
- Uvicorn - ASGI server

**Infrastructure:**
- Firebase Authentication - User accounts (email/password + Google Sign-In)
- Firebase Firestore - NoSQL database (Asia-northeast1 region for Japan)
- Firebase Storage - Pet photos (Asia-northeast1)
- Vercel - Next.js frontend hosting (global CDN, fast in Japan)
- Railway/Render - FastAPI backend hosting
- Google Gemini 1.5 Pro - AI model

**Development Tools:**
- Git - Version control
- ESLint + Prettier - Code formatting
- Jest + React Testing Library - Frontend tests
- Pytest - Backend tests
- Playwright - E2E tests

### Project Structure

```
pet-care-ai/
├── frontend/                   # Next.js app
│   ├── app/
│   │   ├── [locale]/          # Bilingual routing (en/ja)
│   │   │   ├── page.tsx               # Landing page
│   │   │   ├── login/                 # Auth pages
│   │   │   ├── dashboard/             # User dashboard
│   │   │   ├── chat/                  # AI chat interface
│   │   │   ├── pets/                  # Pet profiles
│   │   │   └── vets/                  # Vet finder
│   │   └── api/               # Next.js API routes (proxy to FastAPI)
│   ├── components/            # Reusable React components
│   ├── lib/                   # Utils, Firebase client, API client
│   ├── public/                # Static assets
│   ├── styles/                # Global styles
│   └── package.json
│
├── backend/                    # FastAPI app
│   ├── app/
│   │   ├── main.py                    # FastAPI entry point
│   │   ├── routers/                   # API endpoints
│   │   │   ├── chat.py                # Gemini AI chat
│   │   │   ├── pets.py                # Pet CRUD
│   │   │   └── vets.py                # Vet recommendations
│   │   ├── models/                    # Pydantic models
│   │   ├── services/                  # Business logic
│   │   │   ├── gemini_service.py      # AI integration
│   │   │   ├── firebase_service.py    # Database operations
│   │   │   └── vet_service.py         # Vet search logic
│   │   ├── middleware/                # Auth, CORS, rate limiting
│   │   └── config.py                  # Environment config
│   ├── tests/                 # Unit & integration tests
│   ├── requirements.txt
│   └── Dockerfile
│
├── docs/                       # Documentation
│   └── plans/                 # Design & planning docs
│
└── README.md
```

---

## 2. Core Features

### 2.1 Landing Page (Public)

**Purpose:** Convert visitors into users

**Sections:**
1. **Hero Section**
   - Headline: "AI-Powered Pet Care Advisor" / "AIペットケアアドバイザー"
   - Subtext: Explain the service value proposition
   - CTA: "Get Started Free" button with hover animation
   - Hero image: Pet + AI concept illustration
   - GSAP animations: Smooth fade-in, floating elements

2. **Features Overview** (3 cards)
   - 24/7 AI Pet Expert
   - Personal Pet Profiles
   - Find Trusted Vets
   - Animated icon entrance (staggered)

3. **How It Works** (3 steps)
   - Create your pet's profile
   - Chat with AI expert
   - Get personalized advice
   - Scroll-triggered animations

4. **Navigation**
   - Language switcher (EN/JA toggle in header)
   - Simple menu: Home | Login | Sign Up

**Design Style:**
- Clean white background with soft pastel accents
- Calming pet-friendly colors (soft blue, warm coral, fresh green)
- Smooth parallax scrolling (GSAP ScrollTrigger)
- Rounded corners, soft shadows (modern Japanese aesthetic)
- Generous whitespace

### 2.2 Authentication

**Sign Up:**
- Email + Password (Firebase Auth)
- Google Sign-In button
- Form validation with animated error messages
- Welcome animation on success
- Redirect to dashboard

**Login:**
- Email + Password
- "Forgot Password" flow (Firebase password reset)
- Remember me option
- Redirect to dashboard

**Security:**
- JWT tokens from Firebase
- Token stored in localStorage (httpOnly cookies for production later)
- All API calls include Authorization header
- Backend validates token with Firebase Admin SDK

### 2.3 Dashboard (Protected)

**Layout:**
- Left sidebar navigation (collapsible on mobile)
  - Dashboard
  - My Pet
  - AI Chat
  - Find Vets
  - Settings
  - Logout
- Active indicator animates between nav items

**Dashboard Content:**
- Welcome message: "Welcome back, [Name]!"
- Quick stats cards (animated entrance):
  - Pet registered: Yes/No
  - Conversations this week: X
  - Trial status: "Day 3/7 - 18/30 messages left today"
- Quick action buttons:
  - Add Pet (if no pet yet)
  - Start Chat
  - Find Vets
- Recent activity feed (optional for MVP)

**GSAP Animations:**
- Staggered card entrance on load
- Hover lift effect on cards
- Smooth page transitions
- Micro-interactions on buttons

### 2.4 Pet Profile

**Single Pet (MVP Limit: 1 pet maximum)**

**Create/Edit Form:**
- Name* (required)
- Species*: Dog, Cat, Bird, Rabbit, Other (dropdown)
- Breed (text input with autocomplete suggestions)
- Birth Date / Age (date picker or number input)
- Weight (number + unit selector: kg/lb)
- Gender: Male, Female, Unknown
- Color (optional)
- Microchip ID (optional)
- Medical History (textarea)
- Allergies (tag input)
- Current Medications (list with name, dosage, frequency)
- Vaccinations (list with name, date, next due)
- Photo Upload (drag & drop, 1 photo max for MVP, 5MB limit)

**Photo Upload:**
- Drag & drop zone
- File browser fallback
- Preview before save
- Upload to Firebase Storage: `/pets/{userId}/{petId}/{filename}`
- Animated progress bar
- Crop/resize on frontend before upload (reduce storage costs)

**Pet Display Card:**
- Large photo with rounded corners
- Name, species, breed prominently displayed
- Age, weight as secondary info
- Edit button (modal opens with form)
- Delete button (confirmation dialog)

**GSAP Animations:**
- Card entrance with scale animation
- Photo upload preview with elastic bounce
- Modal slide-in/out
- Form input focus animations

**Firestore Schema:**
```javascript
users/{userId}/pets/{petId}
{
  name: "Hachi",
  species: "dog",
  breed: "Shiba Inu",
  birthDate: timestamp,
  age: 3, // calculated
  weight: 10.5,
  weightUnit: "kg",
  gender: "male",
  color: "red",
  microchipId: "123456789",
  medicalHistory: "Had surgery in 2023...",
  allergies: ["chicken", "corn"],
  medications: [
    { name: "Antibiotic", dosage: "250mg", frequency: "twice daily", startDate: timestamp, endDate: timestamp }
  ],
  vaccinations: [
    { name: "Rabies", date: timestamp, nextDue: timestamp }
  ],
  photoUrl: "https://storage.googleapis.com/...",
  createdAt: timestamp,
  updatedAt: timestamp
}
```

### 2.5 AI Chat (Core Feature)

**Purpose:** Real-time conversation with Gemini AI pet expert

**Layout:**
- Full-height chat interface
- Pet info bar at top (shows which pet context is active)
- Message thread (scrollable, auto-scroll to bottom)
- Input box at bottom with Send button
- "New Conversation" button in header

**Chat Features:**

1. **Message Types:**
   - User messages: Right-aligned, blue bubble
   - AI responses: Left-aligned, gray bubble
   - System messages: Centered, subtle (e.g., "New conversation started")

2. **Streaming Responses:**
   - Gemini supports streaming
   - Show typing indicator (three pulsing dots)
   - Display AI response incrementally (typewriter effect)
   - Auto-scroll as text appears

3. **Conversation History:**
   - Save all messages to Firestore
   - Load past conversations
   - Sidebar shows conversation list (title auto-generated from first message)
   - Click to resume conversation

4. **Context Awareness:**
   - AI has access to pet's full profile
   - System prompt includes pet details, medical history, allergies
   - AI considers breed-specific issues (e.g., Shiba Inu hip dysplasia)

5. **Safety Features:**
   - AI always recommends vet for serious symptoms
   - Clear disclaimer: "I'm an AI assistant, not a veterinarian"
   - Emergency message if keywords detected: "This sounds serious - contact a vet immediately"

**Gemini System Prompt Template:**
```
You are an expert veterinary assistant helping pet owners.

Pet Information:
- Name: {pet.name}
- Species: {pet.species}
- Breed: {pet.breed}
- Age: {pet.age} years old
- Weight: {pet.weight} kg
- Medical History: {pet.medicalHistory}
- Known Allergies: {pet.allergies}
- Current Medications: {pet.medications}

Instructions:
1. Provide helpful, accurate pet care advice
2. Be empathetic and supportive
3. If the issue seems serious, life-threatening, or beyond home care, strongly recommend visiting a veterinarian immediately
4. Consider the pet's specific breed, age, and medical history in your advice
5. Provide practical, actionable steps
6. Keep responses concise but thorough (200-400 words)
7. Respond in {language} (Japanese or English)
8. Use a warm, professional tone

IMPORTANT: You are NOT a replacement for veterinary care. Always err on the side of caution and recommend professional help for anything beyond basic advice.

User's question: {message}
```

**Rate Limiting Display:**
```
Bottom of chat input:
"Messages today: 12/30 | Resets in 4 hours"

When approaching limit (24/30):
Yellow warning: "You've used 24/30 messages today"

When limit hit:
Input disabled, show: "Daily limit reached. Resets at midnight. Upgrade for unlimited messages!"
```

**GSAP Animations:**
- Message bubbles fade in with slight bounce
- Typing indicator (dots bounce up/down)
- Auto-scroll animation (smooth, not instant)
- Input focus animation (border color, subtle shadow)
- Send button pulse on hover

**Firestore Schema:**
```javascript
users/{userId}/conversations/{conversationId}
{
  title: "Coughing issue", // Auto-generated from first message
  petId: "pet123",
  language: "ja",
  createdAt: timestamp,
  updatedAt: timestamp,
  messageCount: 12
}

users/{userId}/conversations/{conversationId}/messages/{messageId}
{
  role: "user" | "assistant" | "system",
  content: "My dog has been coughing...",
  timestamp: timestamp,
  tokens: 45 // For usage tracking
}
```

**API Endpoint:**
```python
POST /api/v1/chat/message
Request:
{
  "conversationId": "conv123",  # Optional for new conversation
  "petId": "pet123",
  "message": "My dog is coughing, what should I do?",
  "language": "en" | "ja"
}

Response:
{
  "conversationId": "conv123",
  "messageId": "msg456",
  "response": "I understand your concern about Hachi's coughing...",
  "timestamp": "2026-03-04T10:30:00Z",
  "tokens": 150
}
```

### 2.6 Vet Finder

**Purpose:** Find and recommend veterinary clinics based on location, specialties, and AI analysis

**Search Interface:**

1. **Search Filters:**
   - Location input (Google Places Autocomplete or manual address)
   - Current location button (geolocation API)
   - Search radius slider: 1-50 km
   - Specialty filter (multi-select):
     - General Practice
     - Emergency 24/7
     - Surgery
     - Dentistry
     - Exotic Animals
     - Orthopedics
   - Species filter: Dog, Cat, Bird, Rabbit, Other
   - "Search" button

2. **Results Display:**
   - Map view (Google Maps embedded) with vet markers
   - List view (scrollable side panel or below map)
   - Toggle between map/list on mobile

3. **Vet Card (List View):**
   - Name (Japanese + English)
   - Distance from search location
   - Rating: ★★★★☆ (4.5/5) - 128 reviews
   - Specialty tags (badges)
   - Address
   - Phone number (click to call on mobile)
   - Hours: Open now / Closes at 6:00 PM
   - "View Details" button
   - "Get Directions" link (opens Google Maps)

4. **Vet Details Modal:**
   - Full description
   - Photo gallery
   - Complete hours (all days)
   - Services/specialties list
   - Price range indicator (¥, ¥¥, ¥¥¥)
   - Features: Parking, English-speaking staff, Emergency 24h
   - Reviews section (Phase 2 - not MVP)
   - Website link
   - "Call Now" button
   - "Get AI Recommendation" button

**AI Recommendation Feature:**

After search, button appears: "Ask AI Which Vet is Best for [Pet Name]"

**Flow:**
1. User clicks button
2. Modal shows: "Analyzing vets for Hachi..."
3. Backend sends to Gemini:
   - Pet profile (breed, age, medical history)
   - Search results (vet names, specialties, ratings, distances)
   - Reason for visit (user can optionally specify: routine, emergency, specialty)
4. Gemini responds with:
   - Top recommendation with reasoning
   - Alternative options
   - Urgency level (low/medium/high)
5. Display recommendation in modal with typewriter effect

**Example AI Recommendation:**
```
Based on Hachi's breed (Shiba Inu) and age (3 years), I recommend:

🏥 Top Choice: Tanaka Veterinary Clinic
✓ 1.2 km away - very convenient
✓ 4.5★ rating with 128 reviews
✓ Specializes in medium breeds like Shiba Inus
✓ English-speaking staff available
✓ Open until 7 PM today

Why this choice: Shiba Inus commonly experience hip and joint issues as they age. Dr. Tanaka is known for preventive orthopedic care and has excellent reviews for Shiba owners specifically.

Alternative Options:
- Shibuya Animal Hospital (emergency care specialist)
- Green Hills Vet (budget-friendly option)

Urgency: Low - This is suitable for routine checkups.
```

**Rate Limiting:**
- 10 vet searches total for MVP trial
- Show counter: "Vet searches: 4/10 remaining"
- When limit reached: "Upgrade for unlimited vet searches"

**GSAP Animations:**
- Map markers drop in with bounce
- Vet cards slide in from left (staggered)
- Card hover: lift + shadow increase
- Modal entrance: scale up from center
- Star ratings animate (fill from left to right)
- Recommendation text appears with typewriter effect

**Firestore Schema:**
```javascript
vets/{vetId}
{
  name: {
    en: "Tanaka Veterinary Clinic",
    ja: "田中動物病院"
  },
  description: {
    en: "Full-service veterinary clinic...",
    ja: "フルサービスの動物病院..."
  },
  location: {
    lat: 35.6812,
    lng: 139.7671,
    geohash: "xn76urx66", // For geoqueries
    address: {
      en: "1-2-3 Shibuya, Tokyo",
      ja: "東京都渋谷区1-2-3"
    },
    prefecture: "Tokyo",
    city: "Shibuya",
    postalCode: "150-0002"
  },
  contact: {
    phone: "+81-3-1234-5678",
    email: "info@tanaka-vet.jp",
    website: "https://tanaka-vet.jp"
  },
  specialties: ["general", "surgery", "dental", "emergency"],
  species: ["dog", "cat", "bird", "rabbit"],
  hours: {
    monday: { open: "09:00", close: "18:00" },
    tuesday: { open: "09:00", close: "18:00" },
    wednesday: { open: "09:00", close: "18:00" },
    thursday: { open: "09:00", close: "18:00" },
    friday: { open: "09:00", close: "18:00" },
    saturday: { open: "09:00", close: "15:00" },
    sunday: { closed: true }
  },
  rating: 4.5,
  reviewCount: 128,
  priceRange: "¥¥", // ¥ = budget, ¥¥ = moderate, ¥¥¥ = premium
  features: ["parking", "emergency24h", "englishSpeaking"],
  photos: ["url1", "url2", "url3"],
  verified: true, // Admin verified
  createdAt: timestamp,
  updatedAt: timestamp
}
```

**API Endpoints:**
```python
POST /api/v1/vets/search
Request:
{
  "location": { "lat": 35.6812, "lng": 139.7671 },
  "radius": 5, # km
  "specialties": ["general", "surgery"], # Optional
  "species": ["dog"], # Optional
  "language": "ja"
}

Response:
{
  "vets": [
    {
      "id": "vet123",
      "name": "Tanaka Veterinary Clinic",
      "distance": 1.2, # km
      "location": {...},
      "rating": 4.5,
      "reviewCount": 128,
      "specialties": ["general", "surgery"],
      "phone": "+81-3-1234-5678",
      "isOpen": true # Based on current time
    }
  ],
  "total": 15
}

POST /api/v1/vets/recommend
Request:
{
  "petId": "pet123",
  "searchResults": ["vet123", "vet456", "vet789"],
  "reason": "routine" | "emergency" | "specialty", # Optional
  "language": "ja"
}

Response:
{
  "recommendedVetId": "vet456",
  "explanation": "Based on Hachi's breed and age...",
  "alternativeVets": ["vet123", "vet789"],
  "urgencyLevel": "low" | "medium" | "high"
}
```

---

## 3. Visual Design & Animations

### 3.1 Design System

**Color Palette (Japanese Market Friendly):**
```css
/* Primary Colors */
--soft-blue: #6B9FE8;        /* Trust, calm */
--warm-coral: #FF8B7B;       /* Friendly, approachable */
--fresh-green: #7ED957;      /* Health, nature */

/* Neutral Colors */
--white: #FFFFFF;            /* Clean backgrounds */
--light-gray: #F5F7FA;       /* Subtle sections */
--medium-gray: #8B95A5;      /* Secondary text */
--dark-gray: #2D3748;        /* Primary text */

/* Semantic Colors */
--success: #48BB78;
--warning: #F6AD55;
--error: #FC8181;
--info: #4299E1;
```

**Typography:**
```css
/* Font Families */
--font-japanese: 'Noto Sans JP', sans-serif;
--font-english: 'Inter', sans-serif;

/* Font Sizes */
--text-xs: 12px;
--text-sm: 14px;
--text-base: 16px;
--text-lg: 18px;
--text-xl: 20px;
--text-2xl: 24px;
--text-3xl: 28px;
--text-4xl: 36px;
--text-5xl: 48px;

/* Line Height */
--leading-normal: 1.6; /* Better for Japanese text */
```

**Spacing System:**
```
4px, 8px, 12px, 16px, 24px, 32px, 48px, 64px, 96px
(Consistent 4px base unit)
```

**Border Radius:**
```css
--radius-sm: 8px;    /* Buttons, inputs */
--radius-md: 12px;   /* Cards */
--radius-lg: 16px;   /* Modals, large sections */
--radius-full: 9999px; /* Pills, avatars */
```

**Shadows (Subtle for Japanese aesthetic):**
```css
--shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
--shadow-md: 0 4px 6px rgba(0, 0, 0, 0.07);
--shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
--shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.12);
```

### 3.2 GSAP Animation Patterns

**Philosophy:**
- Subtle and smooth (Japanese users prefer refined over flashy)
- Always 60fps (GPU-accelerated transforms)
- Purposeful (guide attention, provide feedback, create delight)
- Performant (cleanup on unmount, reduce motion respect)

**Common Patterns:**

1. **Page Load (Staggered Entrance):**
```javascript
gsap.from(".dashboard-card", {
  opacity: 0,
  y: 30,
  stagger: 0.1,
  duration: 0.6,
  ease: "power2.out"
});
```

2. **Scroll Triggered:**
```javascript
gsap.from(".feature-card", {
  scrollTrigger: {
    trigger: ".features-section",
    start: "top 80%",
    toggleActions: "play none none reverse"
  },
  y: 60,
  opacity: 0,
  stagger: 0.2,
  duration: 0.8,
  ease: "power3.out"
});
```

3. **Button Hover:**
```javascript
button.addEventListener("mouseenter", () => {
  gsap.to(button, {
    scale: 1.05,
    duration: 0.3,
    ease: "power2.out"
  });
});
```

4. **Modal Enter/Exit:**
```javascript
// Enter
gsap.from(".modal", {
  scale: 0.9,
  opacity: 0,
  duration: 0.3,
  ease: "back.out(1.4)"
});

// Exit
gsap.to(".modal", {
  scale: 0.9,
  opacity: 0,
  duration: 0.2,
  ease: "power2.in"
});
```

5. **Chat Message:**
```javascript
gsap.from(".message", {
  opacity: 0,
  y: 20,
  duration: 0.4,
  ease: "power2.out"
});
```

6. **Typing Indicator:**
```javascript
gsap.to(".typing-dot", {
  y: -10,
  stagger: {
    each: 0.2,
    repeat: -1,
    yoyo: true
  },
  duration: 0.5,
  ease: "power1.inOut"
});
```

**Performance Considerations:**
```javascript
// Respect reduced motion preference
const prefersReducedMotion = window.matchMedia("(prefers-reduced-motion: reduce)");
if (prefersReducedMotion.matches) {
  gsap.globalTimeline.timeScale(100); // Instant animations
}

// Cleanup on component unmount (React)
useEffect(() => {
  const ctx = gsap.context(() => {
    // All animations here
  });
  return () => ctx.revert(); // Cleanup
}, []);
```

### 3.3 Responsive Design

**Breakpoints:**
```javascript
sm: 640px   // Mobile landscape
md: 768px   // Tablet
lg: 1024px  // Desktop
xl: 1280px  // Large desktop
2xl: 1536px // Ultra-wide
```

**Mobile Optimizations (Critical for Japan - 70%+ mobile traffic):**
- Bottom navigation bar (common pattern in Japanese mobile apps)
- Larger touch targets (minimum 48px)
- Simplified animations (reduce for performance)
- Swipe gestures (pet card carousel, chat history drawer)
- One-handed operation friendly (important controls in thumb zone)

**Japanese UX Patterns:**
- More whitespace than Western sites
- Subtle color transitions
- Clear visual hierarchy
- Trust indicators (verification badges, expert labels)
- Privacy-conscious (clear data usage messaging)

---

## 4. Data Flow & API Design

### 4.1 Authentication Flow

```
1. User signs up/logs in
   Frontend → Firebase Auth → Returns JWT token

2. Store token
   localStorage.setItem('authToken', token)

3. All API calls include token
   Headers: { Authorization: `Bearer ${token}` }

4. Backend validates
   FastAPI → Firebase Admin SDK → Verify token → Get user ID

5. Use user ID for database operations
   Firestore: users/{userId}/...
```

### 4.2 Rate Limiting (Balanced MVP)

**Per User Limits:**
```python
# Trial limits (7 days from signup)
PET_PROFILES_MAX = 1  # Cannot create more than 1 pet
CHAT_MESSAGES_PER_DAY = 30  # Resets at midnight local time
VET_SEARCHES_TOTAL = 10  # Total for entire trial period
PHOTO_UPLOADS_PER_PET = 1  # 1 photo per pet (1 total since 1 pet)
AI_TOKENS_TOTAL = 50000  # Hard cap for cost control
TRIAL_DURATION_DAYS = 7

# Usage tracking in Firestore
users/{userId}/usage {
  trialStartDate: timestamp,
  trialExpired: false,
  messagesUsedToday: 12,
  lastMessageReset: timestamp,
  vetSearchesUsed: 4,
  totalTokensUsed: 15420,
  photoUploadsUsed: 1
}
```

**UI Display:**
```
Dashboard trial status card:
"Free Trial: Day 3 of 7"
"Messages today: 12/30 (resets in 4 hours)"
"Vet searches: 4/10 remaining"

Warning (at 80% of limit):
"You've used 24/30 messages today. Resets at midnight."

Limit reached:
"🎉 You're loving PetCare AI! Upgrade for unlimited access."
[Upgrade Button] (links to pricing page - not built in MVP)
```

**Backend Implementation:**
```python
# middleware/rate_limit.py
async def check_rate_limit(user_id: str, limit_type: str):
    usage = await get_user_usage(user_id)

    # Check trial expiration
    if usage.trial_expired or (datetime.now() - usage.trialStartDate).days > 7:
        raise RateLimitException("Trial period ended")

    # Check specific limits
    if limit_type == "chat":
        if usage.messagesUsedToday >= 30:
            raise RateLimitException("Daily message limit reached")
        if usage.totalTokensUsed >= 50000:
            raise RateLimitException("Token limit reached for trial")

    elif limit_type == "vet_search":
        if usage.vetSearchesUsed >= 10:
            raise RateLimitException("Vet search limit reached")

    elif limit_type == "photo_upload":
        if usage.photoUploadsUsed >= 1:
            raise RateLimitException("Photo upload limit reached")

    return True
```

### 4.3 API Endpoints

**Base URL:** `https://api.petcare-ai.com` (or your backend URL)

#### Chat API

```python
POST /api/v1/chat/message
"""Send message and get AI response"""
Headers: Authorization: Bearer {token}
Request:
{
  "conversationId": "conv123",  # Optional for new conversation
  "petId": "pet123",
  "message": "My dog is coughing, what should I do?",
  "language": "en" | "ja"
}
Response:
{
  "conversationId": "conv123",
  "messageId": "msg456",
  "response": "I understand your concern about Hachi's coughing...",
  "timestamp": "2026-03-04T10:30:00Z",
  "tokens": 150,
  "usageRemaining": {
    "messagesToday": 18,
    "totalTokens": 35000
  }
}

GET /api/v1/chat/conversations
"""Get all user's conversations"""
Response:
{
  "conversations": [
    {
      "id": "conv123",
      "title": "Coughing issue",
      "petId": "pet123",
      "petName": "Hachi",
      "lastMessage": "I understand your concern...",
      "updatedAt": "2026-03-04T10:30:00Z",
      "messageCount": 12
    }
  ]
}

GET /api/v1/chat/conversations/{conversationId}/messages
"""Get all messages in a conversation"""
Response:
{
  "messages": [
    {
      "id": "msg1",
      "role": "user",
      "content": "My dog is coughing...",
      "timestamp": "2026-03-04T10:25:00Z"
    },
    {
      "id": "msg2",
      "role": "assistant",
      "content": "I understand your concern...",
      "timestamp": "2026-03-04T10:25:15Z"
    }
  ]
}

DELETE /api/v1/chat/conversations/{conversationId}
"""Delete a conversation"""
```

#### Pet API

```python
GET /api/v1/pets
"""Get all user's pets (max 1 for MVP)"""
Response:
{
  "pets": [
    {
      "id": "pet123",
      "name": "Hachi",
      "species": "dog",
      "breed": "Shiba Inu",
      "age": 3,
      "photoUrl": "https://...",
      "createdAt": "2025-01-15T08:00:00Z"
    }
  ]
}

GET /api/v1/pets/{petId}
"""Get single pet details"""

POST /api/v1/pets
"""Create a new pet (max 1 for MVP)"""
Request:
{
  "name": "Hachi",
  "species": "dog",
  "breed": "Shiba Inu",
  "birthDate": "2023-01-15",
  "weight": 10.5,
  "weightUnit": "kg",
  "gender": "male",
  "medicalHistory": "...",
  "allergies": ["chicken"],
  "photoUrl": "https://..." # From Firebase Storage
}

PUT /api/v1/pets/{petId}
"""Update pet information"""

DELETE /api/v1/pets/{petId}
"""Delete a pet"""

POST /api/v1/pets/{petId}/photo
"""Upload pet photo"""
Request: multipart/form-data with image file
Response: { "photoUrl": "https://storage.googleapis.com/..." }
```

#### Vet API

```python
POST /api/v1/vets/search
"""Search for vets by location"""
Request:
{
  "location": { "lat": 35.6812, "lng": 139.7671 },
  "radius": 5, # km
  "specialties": ["general", "surgery"],
  "species": ["dog"],
  "language": "ja"
}
Response:
{
  "vets": [
    {
      "id": "vet123",
      "name": "Tanaka Veterinary Clinic",
      "distance": 1.2,
      "location": {...},
      "rating": 4.5,
      "reviewCount": 128,
      "specialties": ["general", "surgery"],
      "phone": "+81-3-1234-5678",
      "isOpen": true
    }
  ],
  "usageRemaining": { "vetSearches": 6 }
}

GET /api/v1/vets/{vetId}
"""Get detailed vet information"""

POST /api/v1/vets/recommend
"""Get AI recommendation for best vet"""
Request:
{
  "petId": "pet123",
  "searchResults": ["vet123", "vet456"],
  "reason": "routine",
  "language": "ja"
}
Response:
{
  "recommendedVetId": "vet456",
  "explanation": "Based on Hachi's breed...",
  "alternativeVets": ["vet123"],
  "urgencyLevel": "low"
}
```

#### User API

```python
GET /api/v1/users/me
"""Get current user info"""
Response:
{
  "uid": "user123",
  "email": "user@example.com",
  "displayName": "Taro Yamada",
  "preferredLanguage": "ja",
  "createdAt": "2025-01-15T08:00:00Z",
  "usage": {
    "trialStartDate": "2026-03-01T00:00:00Z",
    "trialDaysRemaining": 4,
    "messagesUsedToday": 12,
    "messageLimitDaily": 30,
    "vetSearchesUsed": 4,
    "vetSearchesLimit": 10,
    "totalTokensUsed": 15420,
    "tokenLimit": 50000
  }
}

PUT /api/v1/users/me
"""Update user settings"""
Request:
{
  "displayName": "Taro Yamada",
  "preferredLanguage": "ja"
}
```

### 4.4 Error Handling

**Standard Error Response:**
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Daily message limit reached",
    "details": "You've used 30/30 messages today. Limit resets at midnight.",
    "timestamp": "2026-03-04T10:30:00Z"
  }
}
```

**Error Codes:**
- `AUTH_REQUIRED` (401) - No auth token
- `INVALID_TOKEN` (401) - Invalid/expired token
- `FORBIDDEN` (403) - User doesn't have access
- `NOT_FOUND` (404) - Resource not found
- `INVALID_INPUT` (400) - Validation error
- `RATE_LIMIT_EXCEEDED` (429) - Rate limit hit
- `TRIAL_EXPIRED` (403) - Trial period ended
- `AI_SERVICE_ERROR` (503) - Gemini API error
- `INTERNAL_ERROR` (500) - Server error

---

## 5. Deployment & Infrastructure

### 5.1 Frontend Deployment (Vercel)

**Setup:**
```bash
cd frontend
vercel --prod
```

**Environment Variables:**
```bash
NEXT_PUBLIC_FIREBASE_API_KEY=xxx
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=xxx
NEXT_PUBLIC_FIREBASE_PROJECT_ID=xxx
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=xxx
NEXT_PUBLIC_API_URL=https://api.petcare-ai.com
NEXT_PUBLIC_GOOGLE_MAPS_API_KEY=xxx
```

**Performance:**
- Image Optimization (Next.js built-in)
- Edge Functions for API routes
- Cache-Control headers for static assets
- Vercel Analytics enabled

### 5.2 Backend Deployment (Railway)

**Dockerfile:**
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Environment Variables:**
```bash
FIREBASE_SERVICE_ACCOUNT_KEY={"type": "service_account",...}
GEMINI_API_KEY=xxx
FRONTEND_URL=https://petcare-ai.vercel.app
ENVIRONMENT=production
RATE_LIMIT_ENABLED=true
SENTRY_DSN=xxx
```

### 5.3 Firebase Configuration

**Project:** `petcare-ai-mvp`

**Services:**
1. Authentication
   - Email/Password
   - Google Sign-In
   - Region: Asia-northeast1 (Tokyo)

2. Firestore
   - Region: Asia-northeast1 (Tokyo)
   - Security rules: Users can only access their own data
   - Indexes: Create for vet location queries

3. Storage
   - Region: Asia-northeast1 (Tokyo)
   - Rules: Users can upload to their own pet folder
   - Max file size: 5MB
   - Allowed types: image/*

### 5.4 Cost Estimates (MVP)

**Monthly Budget: <$100**

| Service | Expected Cost | Explanation |
|---------|--------------|-------------|
| Vercel | $0 | Free tier (hobby plan) |
| Railway | $5-20 | Backend hosting (~100 hours) |
| Firebase | $5-15 | Auth + Firestore + Storage (low usage) |
| Gemini API | $30-50 | 50k tokens × 100 users = 5M tokens (~$30) |
| Google Maps | $0-10 | Free tier covers 28k map loads |
| **Total** | **$40-95** | Well within budget |

**Cost Controls:**
- Token limit per user (50k) prevents runaway costs
- Rate limiting on all endpoints
- Image compression before upload (reduce storage)
- Firestore query optimization (minimize reads)
- Caching common AI responses

---

## 6. Testing Strategy

### 6.1 Unit Tests

**Frontend (Jest + RTL):**
- Component rendering
- Form validation
- API client functions
- Utility functions

**Backend (Pytest):**
- Gemini service
- Firebase service
- Rate limiting logic
- API endpoint logic

### 6.2 Integration Tests

- Auth flow (signup → login → token validation)
- Pet CRUD operations
- Chat message flow (create conversation → send message → receive response)
- Vet search and recommendation

### 6.3 E2E Tests (Playwright)

- Complete user journey:
  1. Sign up
  2. Create pet profile
  3. Upload photo
  4. Send chat message
  5. Receive AI response
  6. Search for vets
  7. Get AI recommendation
  8. Hit rate limit
  9. Logout

### 6.4 Manual Testing Checklist

- [ ] Signup/login works (email + Google)
- [ ] Create pet profile with photo
- [ ] Chat in English and Japanese
- [ ] Conversation history persists
- [ ] Vet search returns results
- [ ] Map displays correctly
- [ ] AI recommendation works
- [ ] Rate limits trigger correctly
- [ ] Mobile responsive (iOS + Android)
- [ ] Cross-browser (Chrome, Safari, Firefox)
- [ ] Language switcher works
- [ ] Error messages display properly
- [ ] Animations run smoothly (60fps)
- [ ] All forms validate correctly

---

## 7. Launch Plan

### Phase 1: Development (Week 1-6)

**Week 1-2: Foundation**
- Project setup (monorepo)
- Firebase configuration
- Next.js scaffolding
- FastAPI scaffolding
- Authentication flow
- Deploy hello-world

**Week 3-4: Core Features**
- Pet profile CRUD
- AI chat with Gemini
- Conversation history
- Rate limiting
- Bilingual setup

**Week 5: Vet Finder**
- Vet database (seed test data)
- Location search
- Map integration
- AI recommendations

**Week 6: Polish**
- Landing page
- GSAP animations
- Mobile responsive
- Error handling
- Performance optimization

### Phase 2: Closed Beta (Week 7)

**Beta Testers: 10 users**
- 5 Japanese speakers in Japan
- 5 English speakers (expats)
- Mix of dog/cat owners

**Feedback Collection:**
1. Is AI advice helpful and accurate?
2. Any confusing UI elements?
3. Animations smooth or distracting?
4. Japanese translation natural?
5. Would you pay? How much?
6. What features are missing?

**Success Criteria:**
- All 10 users create pet profile
- 7/10 users send 5+ messages
- No critical bugs
- Average rating >3.5/5

### Phase 3: Bug Fixes (Week 8)

- Fix issues from beta
- Improve AI prompts
- UI/UX adjustments
- Quick-win features

### Phase 4: Public MVP Launch (Week 9)

**Launch Channels:**
- Reddit: r/japanlife, r/tokyo
- Facebook: Expat groups in Japan
- Twitter/X: Pet hashtags (#ペット #犬 #猫)
- ProductHunt
- Personal network

**Landing Page Message:**
```
FREE TRIAL: AI-Powered Pet Care Advisor 🐾
Get instant expert advice for your pet, 24/7

✓ Personalized AI based on your pet's profile
✓ Find trusted vets near you in Japan
✓ Available in English & Japanese

[Start Free Trial] ← Big CTA
```

**Goal: 100 users in Month 1**

### Phase 5: Monitor & Iterate (Week 10-12)

**Daily Monitoring:**
- Signups (goal: 3-5/day)
- Active users (goal: 30% weekly active)
- Rate limit hits
- Error rates (<1%)
- Support requests

**Weekly Analysis:**
- Most used features
- Drop-off points
- Common AI questions
- Vet finder usage
- Gemini API costs

**Iterate based on data:**
- If chat heavily used → Increase limits
- If vet finder ignored → Improve UI or remove
- If users churn after 1 day → Improve onboarding
- If costs high → Optimize prompts, add caching

---

## 8. Success Metrics

### MVP Success (Month 3)

**Target Metrics:**
- ✅ 100+ total signups
- ✅ 30+ weekly active users (30% retention)
- ✅ 500+ chat messages sent
- ✅ 50+ vet searches performed
- ✅ <$100/month infrastructure cost
- ✅ 5+ positive testimonials
- ✅ <5% error rate
- ✅ 4/5 average user rating

**If targets met:** Strong signal to invest more (features, marketing, paid tiers)

**If not met:** Pivot or iterate based on feedback

---

## 9. Future Phases (Post-MVP)

### Monetization (Phase 2)

**Pricing Tiers:**
- Free Trial: Current MVP limits
- Basic (¥980/month): 3 pets, 50 messages/hour, unlimited vet searches
- Premium (¥2,980/month): Unlimited pets, unlimited messages, appointment booking, health tracking

**When to add:** After 200+ active users, 20%+ hitting rate limits regularly

### Additional Features (Phase 3+)

**Not included in MVP, consider for later:**
- Appointment booking with vets
- Medication reminders
- Health tracking & charts
- Community forums
- User reviews for vets
- Multi-user pet sharing (family accounts)
- Blog/articles
- Pet food recommendations
- Vaccination scheduler
- Emergency vet hotline
- Export health reports (PDF)
- Integration with vet management systems

---

## 10. Risks & Mitigation

### Technical Risks

1. **Gemini API costs spiral**
   - Mitigation: Strict token limits, caching, daily monitoring

2. **Bad AI advice**
   - Mitigation: Strong disclaimer, always recommend vet for serious issues, log all conversations

3. **Firebase costs spike**
   - Mitigation: Billing alerts, optimize queries, index properly

4. **Site downtime**
   - Mitigation: Vercel/Railway have good uptime (99.9%), status page

### Product Risks

1. **No one uses it**
   - Mitigation: Validate early with beta testers, iterate fast

2. **Rate limits too strict**
   - Mitigation: Monitor where users drop off, adjust limits

3. **Japanese users don't trust AI**
   - Mitigation: Emphasize "assistant" not "replacement", show expert credentials

4. **Competition launches similar**
   - Mitigation: MVP speed is advantage, focus on quality UX

### Legal Risks

1. **Liability for bad advice**
   - Mitigation: Clear Terms of Service, strong disclaimers, insurance (consult lawyer)

2. **Data privacy (Japan APPI compliance)**
   - Mitigation: Firebase is Google-compliant, clear privacy policy, get user consent

3. **Vet data accuracy**
   - Mitigation: Manual curation, verification badges, update regularly

---

## 11. Next Steps

1. ✅ Design approved
2. 📝 Design doc written (this document)
3. 🚀 Create implementation plan (invoke writing-plans skill)
4. 💻 Start development
5. 🧪 Testing
6. 🚢 Launch!

---

**Document Status:** ✅ Approved and ready for implementation planning

**Last Updated:** March 4, 2026
