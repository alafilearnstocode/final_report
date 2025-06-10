---
sidebar_position: 12
---
# Appendix L: ClimateCloset Documentation
# DTC Team 22.1

# ClimateCloset Documentation

## Repository Links

The complete ClimateCloset codebase is available across multiple GitHub repositories:

- **Complete Application (Full Stack)**: [https://github.com/scaryjoshie/v2-climate-closet-full](https://github.com/scaryjoshie/v2-climate-closet-full)
- **Frontend Repository**: [https://github.com/scaryjoshie/climate-closet](https://github.com/scaryjoshie/climate-closet)
- **Backend Repository**: [https://github.com/scaryjoshie/climate-closet-backend](https://github.com/scaryjoshie/climate-closet-backend)

## System Overview

ClimateCloset is a weather-based outfit recommendation web application consisting of:
- **Frontend**: Next.js/React TypeScript PWA
- **Backend**: Google Cloud Functions (Python)
- **Database**: Supabase (PostgreSQL)
- **AI Service**: Google Gemini API
- **Weather Service**: National Weather Service API

**Key Features:**
- AI-powered outfit generation based on weather and user preferences
- Wardrobe management with clothing item analysis
- User feedback system with learning capabilities
- Calendar-based outfit tracking and reshuffling
- Comprehensive wardrobe analytics with personalized insights

## System Architecture Diagram Reference

![System Architecture](/img/system_architecture.png)

The system architecture shows how the four main components interact:
- **Frontend (React/Next.js)**: User interface for interactions
- **Backend (Cloud Functions)**: API interfacer that coordinates all services  
- **SQL Database (Supabase)**: Stores user data, clothing items, and outfits
- **Gemini API**: Provides AI-powered outfit generation and analysis

All requests for data flow through the backend, which can spawn AI API calls and manages secure database access with proper authentication and authorization.

---

## File Structure

### Root Directory
```
/
├── frontend/           # Next.js React application
├── backend/           # Python Google Cloud Functions
├── archives/          # Historical files (ignored)
├── docs/             # Documentation (ignored)
├── README.md         # Project overview
└── PROJECT_ORGANIZATION.md  # Project structure guide
```

### Frontend Structure (`/frontend/`)
```
frontend/
├── app/                         # Next.js app directory
│   ├── layout.tsx               # Root layout component
│   ├── page.tsx                 # Root page component
│   ├── globals.css              # Global styles
│   └── test/                    # Test pages
├── components/                  # React components
│   ├── ui/                      # Reusable UI components
│   ├── weather-app.tsx          # Main app component
│   ├── auth-screen.tsx          # Authentication UI
│   ├── calendar-page.tsx        # Calendar view
│   ├── wardrobe-page.tsx        # Wardrobe management
│   ├── outfit-display.tsx       # Outfit viewing/editing
│   ├── create-outfit-page.tsx   # Outfit creation
│   ├── add-item-page.tsx        # Clothing item upload
│   ├── item-analysis-page.tsx   # AI analysis confirmation
│   ├── item-edit-page.tsx       # Clothing item editing
│   ├── category-view.tsx        # Category browsing
│   ├── settings-page.tsx        # User settings
│   └── [utility components...]
├── lib/                         # Utility libraries
│   ├── api.ts                   # Backend API interface
│   ├── supabase.ts              # Supabase client config
│   ├── image-utils.ts           # Image processing utilities
│   └── utils.ts                 # General utilities
├── contexts/                    # React contexts
│   └── auth-context.tsx         # Authentication context
├── hooks/                       # Custom React hooks
├── styles/                      # Additional styles
├── types/                       # TypeScript type definitions
└── public/                      # Static assets
```

### Backend Structure (`/backend/`)
```
backend/
├── main.py              # Main Cloud Function entry point
├── climatecloset_db.py  # Database models and operations
├── weather_api.py       # Weather service integration
├── requirements.txt     # Python dependencies
├── deploy_complete.sh   # Deployment script (Unix)
├── deploy_complete.bat  # Deployment script (Windows)
└── deployment/          # Deployment configurations
```

---

## Database Schema

### Tables Overview
- `profiles` - User profile information
- `clothing_items` - Individual clothing pieces
- `outfits` - Daily outfit combinations
- `outfit_items` - Junction table linking outfits to clothing items
- `wardrobe_insights` - AI-generated analytics per user
- `outfit_feedback` - User feedback on outfits

### Database Schema Details

#### `profiles` Table
```sql
CREATE TABLE profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    email TEXT,
    full_name TEXT,
    display_name TEXT,
    location TEXT DEFAULT 'Evanston, IL',
    preferences JSONB DEFAULT '{}',
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### `clothing_items` Table
```sql
CREATE TABLE clothing_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    category TEXT NOT NULL,
    name TEXT,
    description TEXT,
    image_url TEXT,
    weather_rating JSONB DEFAULT '{}',  -- {cold: -2 to 2, hot: -2 to 2, rain: -2 to 2, wind: -2 to 2}
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### `outfits` Table
```sql
CREATE TABLE outfits (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    name TEXT,
    outfit_date DATE NOT NULL,
    activity_level TEXT CHECK (activity_level IN ('low', 'moderate', 'high')),
    times_of_day TEXT[] DEFAULT '{}',  -- ['morning', 'afternoon', 'evening']
    occasion TEXT CHECK (occasion IN ('casual', 'work', 'social', 'formal')),
    notes TEXT,
    ai_reasoning TEXT,
    weather_match NUMERIC CHECK (weather_match >= 0 AND weather_match <= 100),
    fashion_score NUMERIC CHECK (fashion_score >= 0 AND fashion_score <= 100),
    overall_rating INTEGER CHECK (overall_rating >= -2 AND overall_rating <= 2),  -- NULL = no feedback
    temp_feedback INTEGER CHECK (temp_feedback >= -2 AND temp_feedback <= 2),    -- NULL = no feedback
    feedback_notes TEXT,  -- User's written feedback about outfit performance
    created_at TIMESTAMPTZ DEFAULT NOW(),
    UNIQUE(user_id, outfit_date)
);
```

**Feedback Rating System:**
- **NULL**: No feedback provided yet
- **-2**: Terrible/Too Cold
- **-1**: Poor/Bit Cold  
- **0**: Fair/Just Right
- **+1**: Good/Bit Warm
- **+2**: Excellent/Too Hot

#### `outfit_items` Junction Table
```sql
CREATE TABLE outfit_items (
    outfit_id UUID REFERENCES outfits(id) ON DELETE CASCADE,
    clothing_id UUID REFERENCES clothing_items(id) ON DELETE RESTRICT,
    PRIMARY KEY (outfit_id, clothing_id)
);
```

#### `wardrobe_insights` Table
```sql
CREATE TABLE wardrobe_insights (
    user_id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    insights TEXT,  -- Human-readable analytics text
    overall_rating NUMERIC,
    raw_data JSONB DEFAULT '{}'  -- Structured analytics data
);
```

---

## Frontend-Backend API Interface

### API Base Configuration
- **Base URL**: `https://us-central1-dtc-2-461504.cloudfunctions.net/climatecloset-api`
- **Authentication**: Bearer tokens via Supabase Auth
- **Content-Type**: `application/json`

### Frontend API Class (`frontend/lib/api.ts`)

#### Interface Definitions
```typescript
export interface OutfitData {
  date: string
  activity_level: "low" | "moderate" | "high"
  times_of_day: string[]
  occasion: "work" | "casual" | "formal" | "social"
  notes?: string
}

export interface ClothingAnalysis {
  image_data: string  // Base64 encoded image
  category: string
  name?: string
}

export interface ClothingItem {
  category: string
  name: string
  description: string
  weather_ratings: {
    cold: number    // -2 to +2
    hot: number     // -2 to +2
    rain: number    // -2 to +2
    wind: number    // -2 to +2
  }
  image_url?: string
  image_data?: string  // Base64 for new items
}
```

#### API Methods and Endpoints

| Method | Endpoint | Auth Required | Purpose |
|--------|----------|--------------|---------|
| `getCurrentWeather()` | `/get_weather` | ❌ | Get current weather data |
| `getWeatherForDate(date)` | `/get_weather_for_date` | ❌ | Get weather for specific date |
| `createOutfit(outfitData)` | `/create_outfit` | ✅ | Generate AI outfit |
| `getOutfitDays()` | `/get_outfit_days` | ✅ | Get calendar dates with outfits |
| `getOutfitByDate(date)` | `/get_outfit_by_date` | ✅ | Get specific outfit |
| `getWardrobeAnalytics(force?)` | `/get_wardrobe_analytics` | ✅ | Get AI wardrobe insights |
| `analyzeClothing(analysis)` | `/analyze_clothing` | ✅ | AI analyze clothing image |
| `saveClothing(clothing)` | `/save_clothing` | ✅ | Save clothing item |
| `getWardrobe()` | `/get_wardrobe` | ✅ | Get all user's clothing |
| `updateClothing(clothing)` | `/update_clothing` | ✅ | Update clothing item |
| `deleteClothing(itemId)` | `/delete_clothing` | ✅ | Delete clothing item |
| `updateOutfit(outfit)` | `/update_outfit` | ✅ | Update outfit details |
| `deleteOutfit(outfitId)` | `/delete_outfit` | ✅ | Delete outfit |
| `reshuffleOutfit(data)` | `/reshuffle_outfit` | ✅ | Generate new outfit for date |
| `getUserProfile()` | `/get_user_profile` | ✅ | Get user profile |
| `updateUserPreferences(prefs)` | `/update_user_preferences` | ✅ | Update user preferences |
| `fixUserProfile()` | `/fix_user_profile` | ✅ | Create missing profile |
| `submitOutfitFeedback(feedback)` | `/submit_outfit_feedback` | ✅ | Submit outfit feedback |

### Frontend-Backend Call Locations

#### Authentication Calls (via Supabase directly)
- **File**: `frontend/contexts/auth-context.tsx`
- **Functions**: `signIn()`, `signUp()`, `signOut()`, `getSession()`
- **Target**: Supabase Auth API (not backend)

#### Weather API Calls
- **Files**: `frontend/weather-app.tsx` (lines 84-100)
- **Calls**: `api.getCurrentWeather()`
- **Purpose**: Load weather data for home screen

#### Outfit Management Calls
- **Files**: 
  - `frontend/components/create-outfit-page.tsx` (lines 125-160)
  - `frontend/components/outfit-display.tsx` (lines 100-130, 350-380, 450-480)
  - `frontend/weather-app.tsx` (lines 130-145)
- **Calls**: 
  - `api.createOutfit()` - Create new outfit
  - `api.getOutfitByDate()` - Load specific outfit
  - `api.updateOutfit()` - Update outfit
  - `api.deleteOutfit()` - Delete outfit
  - `api.reshuffleOutfit()` - Generate new outfit

#### Wardrobe Management Calls
- **Files**:
  - `frontend/components/add-item-page.tsx` (lines 120-140)
  - `frontend/components/item-analysis-page.tsx` (lines 185-210)
  - `frontend/components/item-edit-page.tsx` (lines 280-320)
  - `frontend/components/wardrobe-page.tsx` (lines 45-65)
  - `frontend/components/category-view.tsx` (lines 35-55)
- **Calls**:
  - `api.analyzeClothing()` - AI analyze uploaded image
  - `api.saveClothing()` - Save new clothing item
  - `api.getWardrobe()` - Load user wardrobe
  - `api.updateClothing()` - Update clothing item
  - `api.deleteClothing()` - Delete clothing item

#### Analytics Calls
- **Files**: `frontend/components/wardrobe-page.tsx` (lines 75-95)
- **Calls**: `api.getWardrobeAnalytics()`
- **Purpose**: Load AI-generated wardrobe insights

#### Calendar Calls
- **Files**: `frontend/components/calendar-page.tsx` (lines 45-65)
- **Calls**: `api.getOutfitDays()`
- **Purpose**: Load dates with existing outfits

#### Feedback Calls
- **Files**: `frontend/components/outfit-display.tsx` (lines 267-295)
- **Calls**: `api.submitOutfitFeedback()`
- **Purpose**: Submit user feedback for outfit performance

---

## Backend Function Documentation

### Main Entry Point (`backend/main.py`)

#### `main(request)` - Cloud Function Entry Point
```python
@functions_framework.http
def main(request):
    """
    Main entry point for all ClimateCloset API endpoints
    
    Args:
        request: Flask request object containing endpoint and data
        
    Returns:
        tuple: (response_json, status_code, headers)
        
    Handles:
        - CORS preflight requests
        - Request routing to appropriate endpoints
        - Error handling and response formatting
    """
```

#### Authentication Functions

```python
def authenticate_user(request) -> Optional[str]:
    """
    Extract and validate user authentication from request
    
    Args:
        request: Flask request object with Authorization header
        
    Returns:
        Optional[str]: User ID if valid token, None if invalid
        
    Database Access:
        - Validates JWT token with Supabase Auth
        - Returns authenticated user ID
    """

def ensure_user_profile_exists(user_id: str) -> bool:
    """
    Ensure user profile exists in database, create if missing
    
    Args:
        user_id (str): Supabase user UUID
        
    Returns:
        bool: True if profile exists/created, False if failed
        
    Database Access:
        - SELECT from profiles table
        - INSERT profile if missing
        - GET user data from Supabase Auth
    """
```

#### Endpoint Functions

```python
def endpoint_create_outfit(request):
    """
    Create AI-generated outfit for specified date
    
    Args:
        request: Contains outfit requirements (date, activity_level, times_of_day, occasion, notes)
        
    Returns:
        dict: Outfit data with AI reasoning and selected items
        
    Database Access:
        - SELECT clothing_items WHERE user_id = ?
        - SELECT recent outfits for context
        - INSERT/UPDATE outfit
        - INSERT outfit_items relationships
        
    External APIs:
        - Weather API for target date
        - Gemini AI for outfit generation
    """

def endpoint_get_outfit_days(request):
    """
    Get all dates that have outfits for calendar display
    
    Args:
        request: Authenticated request
        
    Returns:
        dict: List of outfit dates
        
    Database Access:
        - SELECT outfit_date FROM outfits WHERE user_id = ?
    """

def endpoint_get_outfit_by_date(request):
    """
    Get specific outfit by date with clothing item details
    
    Args:
        request: Contains date parameter
        
    Returns:
        dict: Complete outfit data with clothing items
        
    Database Access:
        - SELECT outfit WHERE user_id = ? AND outfit_date = ?
        - SELECT outfit_items JOIN clothing_items
    """

def endpoint_get_wardrobe_analytics(request):
    """
    Generate AI-powered wardrobe analytics
    
    Args:
        request: May contain force_recalculate parameter
        
    Returns:
        dict: Analytics scores and recommendations
        
    Database Access:
        - SELECT/INSERT wardrobe_insights (upsert pattern)
        - SELECT all clothing_items for analysis
        - SELECT recent outfits for context
        
    External APIs:
        - Gemini AI for analytics generation
    """

def endpoint_get_weather(request):
    """
    Get current weather data for home screen
    
    Returns:
        dict: Weather data with current conditions and forecasts
        
    External APIs:
        - National Weather Service API
    """

def endpoint_analyze_clothing(request):
    """
    Use AI to analyze uploaded clothing image
    
    Args:
        request: Contains image_data (base64), category, optional name
        
    Returns:
        dict: AI analysis with name, description, weather ratings
        
    External APIs:
        - Gemini Vision AI for image analysis
    """

def endpoint_save_clothing(request):
    """
    Save analyzed clothing item to database
    
    Args:
        request: Contains clothing item data from analysis
        
    Returns:
        dict: Success response with item_id
        
    Database Access:
        - INSERT clothing_items
        - DELETE wardrobe_insights (invalidate cache)
    """

def endpoint_get_wardrobe(request):
    """
    Get all clothing items for authenticated user
    
    Returns:
        dict: List of clothing items with details
        
    Database Access:
        - SELECT clothing_items WHERE user_id = ? ORDER BY created_at DESC
    """

def endpoint_update_clothing(request):
    """
    Update existing clothing item
    
    Args:
        request: Contains item id and updated data
        
    Returns:
        dict: Success/failure response
        
    Database Access:
        - UPDATE clothing_items WHERE id = ? AND user_id = ?
        - DELETE wardrobe_insights (invalidate cache)
    """

def endpoint_delete_clothing(request):
    """
    Delete clothing item from wardrobe
    
    Args:
        request: Contains item id
        
    Returns:
        dict: Success/failure response
        
    Database Access:
        - DELETE clothing_items WHERE id = ? AND user_id = ?
        - DELETE wardrobe_insights (invalidate cache)
    """

def endpoint_update_outfit(request):
    """
    Update existing outfit details
    
    Args:
        request: Contains outfit id and updated data
        
    Returns:
        dict: Success/failure response
        
    Database Access:
        - UPDATE outfits WHERE id = ? AND user_id = ?
    """

def endpoint_delete_outfit(request):
    """
    Delete outfit and associated items
    
    Args:
        request: Contains outfit id
        
    Returns:
        dict: Success/failure response
        
    Database Access:
        - DELETE outfit_items WHERE outfit_id = ?
        - DELETE outfits WHERE id = ? AND user_id = ?
    """

def endpoint_reshuffle_outfit(request):
    """
    Generate new outfit for existing date
    
    Args:
        request: Contains date, requirements, and previous outfit items
        
    Returns:
        dict: New outfit data with AI reasoning
        
    Database Access:
        - SELECT clothing_items for wardrobe
        - SELECT recent outfits for context
        - UPDATE existing outfit
        - DELETE/INSERT outfit_items
        
    External APIs:
        - Weather API for target date
        - Gemini AI for outfit generation
    """

def endpoint_update_user_preferences(request):
    """
    Update user preferences in profile
    
    Args:
        request: Contains preferences object
        
    Returns:
        dict: Success response with updated preferences
        
    Database Access:
        - SELECT/UPDATE profiles SET preferences = ?
    """

def endpoint_submit_outfit_feedback(request):
    """
    Submit feedback for an existing outfit
    
    Args:
        request: Contains outfit_id, overall_rating (0-4), temp_feedback (0-4), feedback_notes
        
    Returns:
        dict: Success/failure response
        
    Database Access:
        - SELECT outfit notes WHERE id = ? AND user_id = ?
        - UPDATE outfits SET overall_rating, temp_feedback, notes WHERE id = ? AND user_id = ?
        
    Rating Conversion:
        - Frontend ratings (0-4) converted to backend scale (-2 to +2)
        - Feedback notes appended to existing outfit notes with [Feedback]: prefix
    """

#### AI Helper Functions

```python
def analyze_clothing_image(image_base64: str, category: str, user_provided_name: str = "") -> Dict:
    """
    Use Gemini Vision to analyze clothing item image
    
    Args:
        image_base64 (str): Base64 encoded image data
        category (str): Clothing category
        user_provided_name (str): Optional user-provided name
        
    Returns:
        Dict: Analysis results with name, description, weather ratings, formality
        
    External APIs:
        - Gemini Vision API for image analysis
    """

def generate_outfit_with_llm(wardrobe_context: str, weather_data: Dict, requirements: Dict, 
                            user_preferences: str = "", recent_outfits_context: str = "", feedback_context: str = "") -> Dict:
    """
    Use Gemini to generate outfit recommendations
    
    Args:
        wardrobe_context (str): User's available clothing items
        weather_data (Dict): Weather conditions for target date
        requirements (Dict): User's outfit requirements
        user_preferences (str): User's style preferences
        recent_outfits_context (str): Recent outfits to avoid repetition
        feedback_context (str): Recent feedback for learning and improvement
        
    Returns:
        Dict: Generated outfit with reasoning and scores
        
    External APIs:
        - Gemini API for outfit generation
        
    AI Learning:
        - Analyzes recent feedback patterns for temperature preferences
        - Learns from user satisfaction ratings to improve future selections
        - Adjusts clothing weight based on past temperature feedback
        - Incorporates user notes about comfort and style preferences
    """

def generate_wardrobe_analytics(wardrobe: List[ClothingItem], recent_outfits: List[Outfit]) -> Dict:
    """
    Generate AI analytics for wardrobe with harsh scoring criteria
    
    Args:
        wardrobe (List[ClothingItem]): User's clothing items
        recent_outfits (List[Outfit]): Recent outfit history
        
    Returns:
        Dict: Analytics with weather preparedness, style diversity scores and recommendations
        
    External APIs:
        - Gemini API for analytics generation
    """

def get_recent_feedback_context(user_id: str, db) -> str:
    """
    Get recent outfit feedback to include in outfit generation context
    
    Args:
        user_id (str): User UUID
        db: Database instance
        
    Returns:
        str: Formatted feedback context for AI prompt
        
    Database Access:
        - SELECT recent outfits with actual feedback WHERE user_id = ? AND overall_rating IS NOT NULL
        
    Processing:
        - Retrieves last 10 outfits where both ratings are NOT NULL (actual feedback provided)
        - Extracts 3 most recent complete feedback entries (ignores NULL/unrated outfits)
        - Converts numeric ratings to descriptive text
        - Includes user feedback notes from dedicated feedback_notes column
        - Returns formatted string for AI context to learn from actual user experiences
    """
```

#### Utility Functions

```python
def get_weather_for_date(target_date: str) -> Dict:
    """
    Get weather forecast for specific date
    
    Args:
        target_date (str): Date in YYYY-MM-DD format
        
    Returns:
        Dict: Weather data including hourly forecast and daily summary
        
    External APIs:
        - Weather API via get_weather_data_general()
    """

def normalize_date(date_str: str) -> str:
    """
    Normalize date string to YYYY-MM-DD format
    
    Args:
        date_str (str): Date string in various formats
        
    Returns:
        str: Normalized date in YYYY-MM-DD format
    """

def invalidate_wardrobe_analytics(user_id: str):
    """
    Helper function to invalidate wardrobe analytics when wardrobe changes
    
    Args:
        user_id (str): User UUID
        
    Database Access:
        - DELETE wardrobe_insights WHERE user_id = ?
    """
```

### Database Module (`backend/climatecloset_db.py`)

#### Data Models

```python
@dataclass
class ClothingItem:
    """
    Represents a single clothing item in user's wardrobe
    
    Attributes:
        id (Optional[str]): UUID primary key
        user_id (Optional[str]): Foreign key to user
        category (str): Clothing category (e.g., 'shirt', 'pants')
        name (str): User-defined or AI-generated name
        description (str): Detailed description for outfit matching
        image_url (str): URL or data URL for item image
        weather_rating (Dict[str, int]): Weather suitability ratings (-2 to +2)
        created_at (Optional[str]): ISO timestamp
    """

@dataclass 
class Outfit:
    """
    Represents a complete outfit for a specific date
    
    Attributes:
        id (Optional[str]): UUID primary key
        user_id (Optional[str]): Foreign key to user
        name (str): AI-generated outfit name
        outfit_date (str): Date in YYYY-MM-DD format
        activity_level (str): 'low', 'moderate', 'high'
        times_of_day (List[str]): ['morning', 'afternoon', 'evening']
        occasion (str): 'casual', 'work', 'social', 'formal'
        notes (str): User notes
        ai_reasoning (str): AI explanation for outfit choice
        weather_match (float): Weather suitability score (0-100)
        fashion_score (float): Fashion coordination score (0-100)
        overall_rating (int): User rating (-2 to +2)
        temp_feedback (int): Temperature feedback (-2 to +2)
        clothing_items (List[str]): List of clothing item UUIDs
        created_at (Optional[str]): ISO timestamp
    """
```

#### Database Class

```python
class ClimateClosetDB:
    """
    Database interface class for ClimateCloset application
    
    Handles all database operations including:
    - User authentication and profiles
    - Clothing item CRUD operations
    - Outfit management
    - Wardrobe analytics
    - Image storage
    """
    
    def __init__(self, supabase_url: str, supabase_key: str):
        """
        Initialize Supabase client
        
        Args:
            supabase_url (str): Supabase project URL
            supabase_key (str): Supabase service role key
        """
```

#### Authentication Methods

```python
def sign_up(self, email: str, password: str, display_name: str = ""):
    """
    Register a new user
    
    Args:
        email (str): User email address
        password (str): User password
        display_name (str): Optional display name
        
    Database Access:
        - Supabase Auth user creation
        - INSERT into profiles table
    """

def sign_in(self, email: str, password: str):
    """
    Authenticate existing user
    
    Args:
        email (str): User email
        password (str): User password
        
    Returns:
        Auth response with session data
        
    Database Access:
        - Supabase Auth sign in
    """

def get_current_user(self):
    """
    Get currently authenticated user
    
    Returns:
        Current user object or None
        
    Database Access:
        - Supabase Auth session check
    """
```

#### Clothing Item Methods

```python
def add_clothing_item(self, item: ClothingItem, image_file_path: Optional[str] = None, 
                     user_id: Optional[str] = None) -> Optional[str]:
    """
    Add new clothing item to wardrobe
    
    Args:
        item (ClothingItem): Clothing item data
        image_file_path (Optional[str]): Path to image file
        user_id (Optional[str]): User UUID
        
    Returns:
        Optional[str]: Created item UUID or None if failed
        
    Database Access:
        - INSERT into clothing_items table
        - Upload image to storage bucket if provided
    """

def get_user_wardrobe(self, user_id: Optional[str] = None) -> List[ClothingItem]:
    """
    Get all clothing items for user
    
    Args:
        user_id (Optional[str]): User UUID
        
    Returns:
        List[ClothingItem]: User's wardrobe items
        
    Database Access:
        - SELECT * FROM clothing_items WHERE user_id = ?
    """

def update_clothing_item(self, item_id: str, updates: Dict) -> bool:
    """
    Update existing clothing item
    
    Args:
        item_id (str): Item UUID
        updates (Dict): Fields to update
        
    Returns:
        bool: Success status
        
    Database Access:
        - UPDATE clothing_items SET ... WHERE id = ?
    """

def delete_clothing_item(self, item_id: str) -> bool:
    """
    Delete clothing item from wardrobe
    
    Args:
        item_id (str): Item UUID
        
    Returns:
        bool: Success status
        
    Database Access:
        - DELETE FROM clothing_items WHERE id = ?
    """
```

#### Outfit Methods

```python
def save_outfit(self, outfit: Outfit, user_id: Optional[str] = None) -> Optional[str]:
    """
    Save outfit to database
    
    Args:
        outfit (Outfit): Outfit data
        user_id (Optional[str]): User UUID
        
    Returns:
        Optional[str]: Created outfit UUID or None if failed
        
    Database Access:
        - INSERT/UPDATE outfits table (upsert on user_id, outfit_date)
        - DELETE/INSERT outfit_items relationships
    """

def get_outfit_by_date(self, outfit_date: str, user_id: Optional[str] = None) -> Optional[Outfit]:
    """
    Get outfit for specific date
    
    Args:
        outfit_date (str): Date in YYYY-MM-DD format
        user_id (Optional[str]): User UUID
        
    Returns:
        Optional[Outfit]: Outfit data or None if not found
        
    Database Access:
        - SELECT outfit WHERE user_id = ? AND outfit_date = ?
        - SELECT outfit_items JOIN clothing_items for item details
    """

def get_user_outfits(self, user_id: Optional[str] = None, limit: int = 30) -> List[Outfit]:
    """
    Get user's recent outfits
    
    Args:
        user_id (Optional[str]): User UUID
        limit (int): Maximum number of outfits to return
        
    Returns:
        List[Outfit]: Recent outfits ordered by date
        
    Database Access:
        - SELECT outfits WHERE user_id = ? ORDER BY outfit_date DESC LIMIT ?
        - SELECT outfit_items for each outfit
    """
```

#### Analytics Methods

```python
def update_wardrobe_insights(self, insights: str, overall_rating: float = None, 
                           raw_data: Dict = None, user_id: Optional[str] = None):
    """
    Update wardrobe analytics insights
    
    Args:
        insights (str): Human-readable insights text
        overall_rating (float): Numeric overall rating
        raw_data (Dict): Structured analytics data
        user_id (Optional[str]): User UUID
        
    Database Access:
        - INSERT/UPDATE wardrobe_insights (upsert on user_id)
    """

def get_wardrobe_insights(self, user_id: Optional[str] = None) -> Optional[Dict]:
    """
    Get cached wardrobe analytics
    
    Args:
        user_id (Optional[str]): User UUID
        
    Returns:
        Optional[Dict]: Analytics data or None if not found
        
    Database Access:
        - SELECT * FROM wardrobe_insights WHERE user_id = ?
    """
```

### Weather API Module (`backend/weather_api.py`)

```python
def get_weather_data_general(target_date: str = None, next_24_hours: bool = False):
    """
    Fetches weather data from NWS API and returns structured JSON data
    
    Args:
        target_date (str, optional): Date in YYYY-MM-DD format. If None, uses today.
        next_24_hours (bool): If True, shows next 24 hours from now instead of target date range
    
    Returns: 
        dict: Weather data containing:
            - current_weather: Weather condition
            - current_temp: Current temperature (°F)
            - current_feels_like_temp: Feels-like temperature
            - current_high/low: Daily high/low temperatures
            - current_wind: Wind speed (mph)
            - current_precipitation: Precipitation chance (%)
            - hourly_forecast: List of hourly forecasts
            - daily_forecast: List of daily forecasts
            - target_date: Processed target date
            - is_today: Boolean if target date is today
            
    External APIs:
        - National Weather Service API
        - Grid ID: LOT (Chicago office)
        - Grid coordinates: 73,81 (Evanston, IL)
        
    Functions:
        - categorize_weather(): Converts detailed forecasts to simple categories
        - calculate_feels_like(): Computes heat index or wind chill
        - parse_wind_speed(): Extracts numeric wind speed from text
    """

def categorize_weather(forecast_text):
    """
    Convert detailed forecast text to simplified weather categories
    
    Args:
        forecast_text (str): NWS forecast description
        
    Returns:
        str: Simplified category ('sunny', 'cloudy', 'rainy', 'snowing')
    """

def calculate_feels_like(temp_f, humidity, wind_speed):
    """
    Calculate feels-like temperature using heat index or wind chill
    
    Args:
        temp_f (float): Temperature in Fahrenheit
        humidity (float): Relative humidity percentage
        wind_speed (float): Wind speed in mph
        
    Returns:
        int: Feels-like temperature in Fahrenheit
        
    Formula:
        - Heat index for temp >= 80°F with humidity
        - Wind chill for temp <= 50°F with wind >= 3mph
        - Actual temperature otherwise
    """
```

---

## External API Integrations

### Gemini AI API
- **Purpose**: AI image analysis and outfit generation
- **Files**: `backend/main.py`
- **Functions**:
  - `analyze_clothing_image()` - Analyze uploaded clothing images
  - `generate_outfit_with_llm()` - Generate outfit recommendations
  - `generate_wardrobe_analytics()` - Create wardrobe insights
- **Model**: `gemini-1.5-flash`
- **Input Types**: Text prompts, base64 encoded images
- **Rate Limiting**: Handled with 10-second timeout

### National Weather Service API
- **Purpose**: Weather data for outfit recommendations
- **Files**: `backend/weather_api.py`
- **Base URL**: `https://api.weather.gov/gridpoints/LOT/73,81`
- **Endpoints**:
  - `/forecast` - Daily forecasts
  - `/forecast/hourly` - Hourly forecasts
- **Location**: Evanston, IL (Grid LOT 73,81)
- **Data Processing**:
  - Weather categorization (sunny/cloudy/rainy/snowing)
  - Feels-like temperature calculation
  - Timezone handling (America/Chicago)

### Supabase Services

#### Authentication API
- **Purpose**: User authentication and session management
- **Files**: `frontend/contexts/auth-context.tsx`, `frontend/lib/supabase.ts`
- **Methods**:
  - `signUp()` - User registration
  - `signInWithPassword()` - Email/password login
  - `signOut()` - User logout
  - `getSession()` - Get current session
  - `onAuthStateChange()` - Listen for auth changes

#### Database API
- **Purpose**: Data persistence and retrieval
- **Files**: `backend/climatecloset_db.py`, all backend endpoints
- **Operations**: CRUD operations on all tables with RLS policies
- **Connection**: Service role key for backend, user JWTs for RLS

#### Storage API
- **Purpose**: Image file storage (currently using data URLs instead)
- **Bucket**: `wardrobe-images`
- **Files**: `backend/climatecloset_db.py`
- **Status**: Configured but not actively used (images stored as data URLs)

---

## Data Flow Diagrams

### Outfit Creation Flow
```
1. User selects date/requirements (create-outfit-page.tsx)
   ↓
2. Frontend calls api.createOutfit() (api.ts)
   ↓ 
3. Backend endpoint_create_outfit() (main.py)
   ↓
4. Get weather data (weather_api.py → NWS API)
   ↓
5. Get user wardrobe (Supabase DB)
   ↓
6. Generate outfit (Gemini AI)
   ↓
7. Save outfit to database (Supabase DB)
   ↓
8. Return outfit data to frontend
```

### Clothing Addition Flow
```
1. User uploads image (add-item-page.tsx)
   ↓
2. Frontend calls api.analyzeClothing() (api.ts)
   ↓
3. Backend endpoint_analyze_clothing() (main.py)
   ↓
4. AI analyzes image (Gemini Vision API)
   ↓
5. Return analysis to frontend (item-analysis-page.tsx)
   ↓
6. User confirms/edits analysis
   ↓
7. Frontend calls api.saveClothing() (api.ts)
   ↓
8. Backend endpoint_save_clothing() (main.py)
   ↓
9. Save to database (Supabase DB)
   ↓
10. Invalidate analytics cache
```

### Analytics Generation Flow
```
1. User opens wardrobe page (wardrobe-page.tsx)
   ↓
2. Frontend calls api.getWardrobeAnalytics() (api.ts)
   ↓
3. Backend endpoint_get_wardrobe_analytics() (main.py)
   ↓
4. Check for cached analytics (Supabase DB)
   ↓
5. If stale/missing: Generate new analytics (Gemini AI)
   ↓
6. Cache results (Supabase DB)
   ↓
7. Return analytics to frontend
```

### Feedback Submission Flow
```
1. User views outfit and provides feedback (outfit-display.tsx)
   ↓
2. User submits feedback form with ratings and notes
   ↓
3. Frontend calls api.submitOutfitFeedback() (api.ts)
   ↓
4. Backend endpoint_submit_outfit_feedback() (main.py)
   ↓
5. Convert ratings from 0-4 to -2/+2 scale
   ↓
6. Merge feedback notes with existing outfit notes
   ↓
7. Update outfit in database (Supabase DB)
   ↓
8. Return success to frontend
   ↓
9. Frontend updates local state and shows confirmation
```

### Enhanced Feedback Submission Flow with NULL Handling
```
1. User views outfit and provides feedback (outfit-display.tsx)
   ↓
2. User submits feedback form with ratings and notes
   ↓
3. Frontend calls api.submitOutfitFeedback() (api.ts)
   ↓
4. Backend endpoint_submit_outfit_feedback() (main.py)
   ↓
5. Convert ratings from 0-4 to -2/+2 scale
   ↓
6. Store ratings and feedback_notes in dedicated columns (replaces NULL)
   ↓
7. Update outfit in database (Supabase DB)
   ↓
8. Return success to frontend
   ↓
9. Frontend updates local state and shows confirmation
```

### AI Learning from Feedback with NULL Filtering
```
1. User requests new outfit (create-outfit-page.tsx)
   ↓
2. Backend gets user wardrobe (Supabase DB)
   ↓
3. Backend gets recent outfits (Supabase DB)
   ↓
4. Backend calls get_recent_feedback_context() (main.py)
   ├── Query: SELECT WHERE overall_rating IS NOT NULL AND temp_feedback IS NOT NULL
   ├── Filter: Only outfits with actual user feedback (ignores NULL/unrated)
   ├── Extract: Last 3 complete feedback entries with ratings + notes
   └── Format: Convert ratings to descriptive text for AI understanding
   ↓
5. Backend gets weather data (NWS API)
   ↓
6. AI generates outfit with feedback learning (Gemini AI)
   ├── Temperature patterns: "Too Cold" feedback → add warmer layers
   ├── Satisfaction patterns: "Excellent" outfits → replicate successful combos
   └── User notes: Specific feedback → adjust future selections
   ↓
7. Save outfit to database with NULL feedback (Supabase DB)
   ↓
8. Return outfit to frontend
```

---

## Component Interaction Map

### Frontend Component Hierarchy
```
weather-app.tsx (Root)
├── auth-screen.tsx (when not authenticated)
└── [Navigation Pages]
    ├── Home (weather display + today's outfit)
    ├── calendar-page.tsx
    │   └── outfit-display.tsx (when date selected)
    ├── wardrobe-page.tsx
    │   ├── category-view.tsx (when category selected)
    │   ├── add-item-page.tsx → item-analysis-page.tsx
    │   └── item-edit-page.tsx (when item selected)
    ├── create-outfit-page.tsx
    └── settings-page.tsx
```

### State Management Flow
```
weather-app.tsx (Main State)
├── currentPage (navigation)
├── selectedDate (for outfit viewing)
├── selectedCategory (for wardrobe browsing) 
├── selectedItem (for editing)
├── analysisData (for clothing analysis)
├── analyticsRefreshTrigger (for cache invalidation)
└── outfitRefreshTrigger (for outfit updates)
```

### Database Access Patterns

#### Read Operations
- **Frequent**: Current weather, today's outfit, wardrobe items
- **Moderate**: Outfit by date, outfit days for calendar
- **Infrequent**: Wardrobe analytics, user profile

#### Write Operations  
- **Create**: New outfits, new clothing items
- **Update**: Outfit modifications, clothing edits, user preferences, outfit feedback
- **Delete**: Outfit removal, clothing removal, analytics cache invalidation

#### Caching Strategy
- **Wardrobe Analytics**: Cached in database, invalidated on wardrobe changes
- **Weather Data**: No caching, always fetch fresh
- **User Session**: Cached by Supabase Auth
- **Wardrobe Items**: Re-fetched on page navigation
- **Outfit Feedback**: Immediately persisted, no caching (latest feedback overwrites previous)

---

## Security and Authentication

### Authentication Flow
1. User signs up/in via Supabase Auth
2. Supabase returns JWT access token
3. Frontend stores session in memory
4. All API calls include Bearer token in Authorization header
5. Backend validates token with Supabase
6. Database enforces Row Level Security (RLS) policies

### Database Security
- **Row Level Security**: All tables have RLS enabled
- **User Isolation**: Users can only access their own data
- **Service Role**: Backend uses service role for admin operations
- **Foreign Key Constraints**: Ensure data integrity

### API Security
- **CORS**: Configured for frontend domain
- **Authentication**: Required for all user data endpoints
- **Input Validation**: All inputs validated and sanitized
- **Error Handling**: Secure error messages, no sensitive data exposure

---

This documentation provides a complete map of all functions, API calls, database interactions, and component relationships in the ClimateCloset application. Use this as a reference for creating system diagrams and understanding the complete data flow through the application. 