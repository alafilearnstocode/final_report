---
sidebar_position: 8
---

# Appendix G: Instructions for Replication

This guide provides step-by-step instructions to replicate the complete ClimateCloset application, including frontend deployment to Vercel, backend deployment to Google Cloud Functions, and database setup with Supabase.

## Repository Links

The complete ClimateCloset codebase is available across multiple GitHub repositories:

- **Complete Application (Full Stack)**: [https://github.com/scaryjoshie/v2-climate-closet-full](https://github.com/scaryjoshie/v2-climate-closet-full)
- **Frontend Repository**: [https://github.com/scaryjoshie/climate-closet](https://github.com/scaryjoshie/climate-closet)
- **Backend Repository**: [https://github.com/scaryjoshie/climate-closet-backend](https://github.com/scaryjoshie/climate-closet-backend)

## Table of Contents
1. [Introduction](#introduction)
2. [System Overview](#system-overview)
3. [Prerequisites](#prerequisites)
4. [Database Setup (Supabase)](#database-setup-supabase)
5. [Backend Setup (Google Cloud Functions)](#backend-setup-google-cloud-functions)
6. [Frontend Setup (Vercel Deployment)](#frontend-setup-vercel-deployment)
7. [Environment Variables Configuration](#environment-variables-configuration)
8. [API Keys and External Services](#extra-help-on-getting-api-keys-and-external-services)
9. [Testing the Application](#testing-the-application)
10. [Troubleshooting](#troubleshooting)

---

## Introduction

**ClimateCloset** is an AI-powered wardrobe assistant that helps users create weather-appropriate outfits based on their personal clothing collection and local weather conditions. 

### What ClimateCloset Does:

**Weather-Aware Outfit Generation**: Automatically creates outfits based on current and forecasted weather conditions, ensuring you're always dressed appropriately for the climate.

**Personal Wardrobe Management**: Users can photograph and catalog their clothing items, with AI analyzing each piece to understand its weather suitability, style, and characteristics.

**AI-Powered Recommendations**: Uses Google's Gemini AI to understand clothing combinations, weather patterns, and user preferences to generate personalized outfit suggestions.

**Wardrobe Analytics**: Provides insights into your wardrobe usage, identifies gaps in your clothing collection, and suggests new items based on your lifestyle and local climate.

**Feedback Learning**: Learns from user feedback on outfit comfort and temperature appropriateness to improve future recommendations.

**Calendar Integration**: Plan outfits for specific dates and occasions, taking into account weather forecasts and your personal schedule.

### Target Users:
- People who struggle with daily outfit decisions
- Those living in climates with variable weather
- Fashion-conscious individuals wanting to optimize their wardrobe
- Anyone looking to make better use of their existing clothing collection

---

## System Overview

### Overall Design Process

During the development process, we utilized some AI tools. I have listed the overall process in which we used them below:

![Overall Design Process](/img/design_process.png)

### System Architecture

To understand the system as a whole, we must first understand the components:

- **The frontend** is the UI the user interacts with
- **The SQL database** stores user information  
- **The Gemini API** is used to make calls to the Large Language Model
- **The backend** allows all of these components to talk to one another

![System Architecture](/img/system_architecture.png)

The backend acts as the central API interface, processing requests from the frontend, managing database operations, and coordinating with external services like the Gemini AI and weather APIs. This architecture ensures secure data handling, scalable performance, and clean separation of concerns between different system components.

---

## Prerequisites

Before starting, ensure you have the following accounts and tools:

### Required Accounts
- **GitHub Account** (for code repository)
- **Vercel Account** (for frontend deployment)
- **Google Cloud Platform Account** (for backend functions)
- **Supabase Account** (for database)
- **Google AI Studio Account** (for Gemini API)

### Required Tools
- **Node.js 18+** and npm/yarn
- **Python 3.9+** and pip
- **Git**
- **Google Cloud CLI** (`gcloud`)

### Installation Commands
```bash
# Install Node.js (via nvm - recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install 18
nvm use 18

# Install Python (if not already installed)
# Windows: Download from python.org
# macOS: brew install python@3.9
# Linux: sudo apt install python3.9 python3.9-pip

# Install Google Cloud CLI
# Follow instructions at: https://cloud.google.com/sdk/docs/install

# Verify installations
node --version    # Should be 18+
python3 --version # Should be 3.9+
gcloud --version  # Should show gcloud info
```

---

## Database Setup (Supabase)

### Step 1: Create Supabase Project

1. Go to [supabase.com](https://supabase.com) and sign up/login
Go to [supabase.com](https://supabase.com/dashboard/organizations) and click "New Organization". Follow their given steps to create your oganization. 
2. Click on your organization and select "New Project"
3. Choose organization and fill in:
   - **Project Name**: `climatecloset`
   - **Database Password**: Generate a strong password (save this!)
   - **Region**: Choose closest to your users
4. Click "Create new project"
5. Wait for project initialization (2-3 minutes)

### Step 2: Configure Database Schema

1. In your Supabase dashboard, go to **SQL Editor** (it's on the menu at the left)
   ![Supabase SQL Editor Location](/img/left_menu.png)
2. Click "New Query"
3. Copy and paste the following schema:

```sql
-- Enable Row Level Security
ALTER TABLE auth.users ENABLE ROW LEVEL SECURITY;

-- Create profiles table
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

-- Create clothing_items table
CREATE TABLE clothing_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    category TEXT NOT NULL,
    name TEXT,
    description TEXT,
    image_url TEXT,
    weather_rating JSONB DEFAULT '{}',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Create outfits table
CREATE TABLE outfits (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    name TEXT,
    outfit_date DATE NOT NULL,
    activity_level TEXT CHECK (activity_level IN ('low', 'moderate', 'high')),
    times_of_day TEXT[] DEFAULT '{}',
    occasion TEXT CHECK (occasion IN ('casual', 'work', 'social', 'formal')),
    notes TEXT,
    ai_reasoning TEXT,
    weather_match NUMERIC CHECK (weather_match >= 0 AND weather_match <= 100),
    fashion_score NUMERIC CHECK (fashion_score >= 0 AND fashion_score <= 100),
    overall_rating INTEGER CHECK (overall_rating >= -2 AND overall_rating <= 2),
    temp_feedback INTEGER CHECK (temp_feedback >= -2 AND temp_feedback <= 2),
    feedback_notes TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    UNIQUE(user_id, outfit_date)
);

-- Create outfit_items junction table
CREATE TABLE outfit_items (
    outfit_id UUID REFERENCES outfits(id) ON DELETE CASCADE,
    clothing_id UUID REFERENCES clothing_items(id) ON DELETE RESTRICT,
    PRIMARY KEY (outfit_id, clothing_id)
);

-- Create wardrobe_insights table
CREATE TABLE wardrobe_insights (
    user_id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    insights TEXT,
    overall_rating NUMERIC,
    raw_data JSONB DEFAULT '{}'
);

-- Enable Row Level Security on all tables
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE clothing_items ENABLE ROW LEVEL SECURITY;
ALTER TABLE outfits ENABLE ROW LEVEL SECURITY;
ALTER TABLE outfit_items ENABLE ROW LEVEL SECURITY;
ALTER TABLE wardrobe_insights ENABLE ROW LEVEL SECURITY;

-- Create RLS Policies for profiles
CREATE POLICY "Users can view own profile" ON profiles
    FOR SELECT USING (auth.uid() = id);
CREATE POLICY "Users can update own profile" ON profiles
    FOR UPDATE USING (auth.uid() = id);
CREATE POLICY "Users can insert own profile" ON profiles
    FOR INSERT WITH CHECK (auth.uid() = id);

-- Create RLS Policies for clothing_items
CREATE POLICY "Users can view own clothing items" ON clothing_items
    FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "Users can insert own clothing items" ON clothing_items
    FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users can update own clothing items" ON clothing_items
    FOR UPDATE USING (auth.uid() = user_id);
CREATE POLICY "Users can delete own clothing items" ON clothing_items
    FOR DELETE USING (auth.uid() = user_id);

-- Create RLS Policies for outfits
CREATE POLICY "Users can view own outfits" ON outfits
    FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "Users can insert own outfits" ON outfits
    FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users can update own outfits" ON outfits
    FOR UPDATE USING (auth.uid() = user_id);
CREATE POLICY "Users can delete own outfits" ON outfits
    FOR DELETE USING (auth.uid() = user_id);

-- Create RLS Policies for outfit_items
CREATE POLICY "Users can view own outfit items" ON outfit_items
    FOR SELECT USING (
        EXISTS (
            SELECT 1 FROM outfits 
            WHERE outfits.id = outfit_items.outfit_id 
            AND outfits.user_id = auth.uid()
        )
    );
CREATE POLICY "Users can insert own outfit items" ON outfit_items
    FOR INSERT WITH CHECK (
        EXISTS (
            SELECT 1 FROM outfits 
            WHERE outfits.id = outfit_items.outfit_id 
            AND outfits.user_id = auth.uid()
        )
    );
CREATE POLICY "Users can delete own outfit items" ON outfit_items
    FOR DELETE USING (
        EXISTS (
            SELECT 1 FROM outfits 
            WHERE outfits.id = outfit_items.outfit_id 
            AND outfits.user_id = auth.uid()
        )
    );

-- Create RLS Policies for wardrobe_insights
CREATE POLICY "Users can view own insights" ON wardrobe_insights
    FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "Users can insert own insights" ON wardrobe_insights
    FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users can update own insights" ON wardrobe_insights
    FOR UPDATE USING (auth.uid() = user_id);
CREATE POLICY "Users can delete own insights" ON wardrobe_insights
    FOR DELETE USING (auth.uid() = user_id);

-- Create indexes for better performance
CREATE INDEX idx_clothing_items_user_id ON clothing_items(user_id);
CREATE INDEX idx_outfits_user_id ON outfits(user_id);
CREATE INDEX idx_outfits_date ON outfits(outfit_date);
CREATE INDEX idx_outfits_user_date ON outfits(user_id, outfit_date);
CREATE INDEX idx_outfit_items_outfit_id ON outfit_items(outfit_id);
CREATE INDEX idx_outfit_items_clothing_id ON outfit_items(clothing_id);
```

4. Click "Run" to execute the schema
5. Verify tables were created in the **Table Editor**

### Step 3: Get Database Credentials

1. Go to **Settings** → **API**
2. Save these values (you'll need them later):
   - **Project URL** (looks like `https://abcdefghijklmnop.supabase.co`)
   - **Project API Key** (anon public key)
   ![Supabase Project URL and API Keys](/img/project_url.png)
   - **Service Role Key** (service_role key - keep this secret!)
3. Go to **Settings** → **Data API**
4. Save the **JWT Secret** (you'll need this for backend, so keep it handy)

   ![Supabase JWT Secret Location](/img/jwt_secret_location.png)

---

## Backend Setup (Google Cloud Functions)

### Step 1: Set Up Google Cloud Project

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Follow Google's instructions and create a new project or select existing one. If you are having trouble, you can follow [this guide](https://developers.google.com/workspace/guides/create-project) by Google
3. Follow [Google's instructions](https://cloud.google.com/endpoints/docs/openapi/enable-api) and enable the following APIs:
   ```bash
   Cloud Functions
   Cloud Build
   Artifact Registry
   ```

### Step 2: Install Dependencies

1. Navigate to a folder where you are comfortable storing your backend temporarily.
2. Open your terminal, and ```cd``` to that location by doing ```cd [file path]```
3. Clone the repository and navigate to backend:
   ```bash
   git clone <your-repo-url>
   cd climatecloset/backend
   ```

4. Install Python dependencies:
   ```bash
   pip install -r requirements.txt
   ```

### Step 3: Configure Environment Variables

Create a `.env.yaml` file in the backend directory:

```yaml
# .env.yaml
SUPABASE_URL: "https://your-project.supabase.co"
SUPABASE_SERVICE_KEY: "your-service-role-key-here"
SUPABASE_JWT_SECRET: "your-jwt-secret-here"
GEMINI_API_KEY: "your-gemini-api-key-here"
```

⚠️ **Security Note**: Never commit `.env.yaml` to git, since that allows others to view your API keys publicly. I cannot overstate how large of a security risk this is. It allows anyone online to use your services with no limit while charging YOUR account for money. Make sure to add your `.env.yaml` as a line to your `.gitignore` file to prevent this.

### Step 4: Deploy to Google Cloud Functions

1. Authenticate with Google Cloud:
   ```bash
   gcloud auth login
   gcloud config set project YOUR_PROJECT_ID
   ```

2. Deploy the function:
   ```bash
   gcloud functions deploy climatecloset-api \
     --runtime python39 \
     --trigger-http \
     --allow-unauthenticated \
     --entry-point main \
     --source . \
     --env-vars-file .env.yaml \
     --memory 512MB \
     --timeout 60s
   ```

   You can also execute the deploy scripts provided in "backend" (`deploy_complete.sh` and `deploy_complete.bat`) to deploy as well.

3. Note the trigger URL (you'll need this for frontend):
   ```
   https://us-central1-YOUR_PROJECT_ID.cloudfunctions.net/climatecloset-api
   ```

### Step 5: Test Backend Deployment

```bash
# Test the health endpoint
curl "https://us-central1-YOUR_PROJECT_ID.cloudfunctions.net/climatecloset-api/get_weather"
```

---

## Frontend Setup (Vercel Deployment)

### Step 1: Prepare Frontend Code

1. Navigate to frontend directory:
   ```bash
   cd ../frontend
   ```

2. Install dependencies:
   ```bash
   npm install
   # or
   yarn install
   ```

3. Test locally:
   ```bash
   npm run dev
   # or
   yarn dev
   ```

### Step 2: Configure Environment Variables

Create `.env.local` file in frontend directory:

```bash
# .env.local
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-public-key-here
NEXT_PUBLIC_API_BASE_URL=https://us-central1-YOUR_PROJECT_ID.cloudfunctions.net/climatecloset-api
```

### Step 3: Deploy to Vercel

#### Option A: Vercel CLI (Recommended)

1. Install Vercel CLI:
   ```bash
   npm i -g vercel
   ```

2. Login and deploy:
   ```bash
   vercel login
   vercel --prod
   ```

3. Follow prompts to configure project

#### Option B: Vercel Dashboard

1. Go to [vercel.com](https://vercel.com) and login
2. Click "New Project"
3. Import your GitHub repository
4. Configure:
   - **Framework Preset**: Next.js
   - **Root Directory**: `frontend`
   - **Build Command**: `npm run build`
   - **Output Directory**: `.next`

### Step 4: Add Environment Variables in Vercel

1. In Vercel dashboard, go to your project
2. Go to **Settings** → **Environment Variables**
3. Add each variable from your `.env.local`:
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `NEXT_PUBLIC_API_BASE_URL`
4. Deploy again to apply changes

---

## Extra Help on Getting API Keys and External Services

### Step 1: Get Supabase Database Credentials (Detailed)

#### Finding Your Supabase Project URL and API Keys

1. **Navigate to your Supabase project dashboard**
   - Go to [supabase.com](https://supabase.com) and login
   - Click on your `climatecloset` project from the project list

2. **Get Project URL and API Keys**
   - In the left sidebar, click **"Settings"** (gear icon at bottom)
   - Click **"Data API"** from the settings menu
   
   ![Supabase Settings Menu](/img/settings_environment_variables.png)

3. **Copy the required values** (you'll see these in the API settings page):
   
   **Project URL:**
   - Look for "Project URL" section
   - Copy the URL (looks like `https://abcdefghijklmnop.supabase.co`)
   - Save this as your `SUPABASE_URL`

   ![Supabase Project URL](/img/project_url.png)

   **JWT Secret:**
   - Scroll down to find "JWT Settings" section
   - Copy the "JWT Secret" value
   - Save this as your `SUPABASE_JWT_SECRET` (for backend)
   
   ![Supabase JWT Secret Location](/img/jwt_secret_location.png)

4. **Getting Anon and Service Role Keys**
   **Anon (Public) Key:**
   - Look for "API keys" section under "Project settings"
   - Find the "anon public" key (usually the first one)
   - Click the copy icon next to it
   - Save this as your `NEXT_PUBLIC_SUPABASE_ANON_KEY` (for frontend)

   **Service Role Key:**
   - In the same "Project API keys" section
   - Find the "service_role" key (usually the second one)
   - Click the copy icon next to it
   - ⚠️ **Keep this secret!** Save as your `SUPABASE_SERVICE_KEY` (for backend only)

   ![Supabase API Keys Location](/img/supabase_api_keys_location.png)

### Step 2: Get Google Cloud Project ID (Detailed)

#### Setting Up Your Google Cloud Project

1. **Go to Google Cloud Console**
   - Visit [console.cloud.google.com](https://console.cloud.google.com)
   - Login with your Google account

2. **Create a New Project (if needed)**
   - Click the project dropdown at the top of the page (next to "Google Cloud")
   - Click "NEW PROJECT" in the popup
   - Enter project name: `climatecloset-app` (or your preferred name)
   - Click "CREATE"

   ![Google Cloud New Project](/img/gcloud_new_project.png)

3. **Find Your Project ID**
   - After creating/selecting your project, note the **Project ID** (not the name)
   - The Project ID appears in the project dropdown and in the URL
   - It usually looks like `climatecloset-app-123456`
   - **Save this!** You'll need it for deployment commands

   ![Google Cloud Project ID](/img/example_project_ID.png)

### Step 3: Get Gemini API Key (Detailed)

#### Creating Your Gemini API Key for AI Features

1. **Access Google AI Studio**
   - Go to [makersuite.google.com](https://makersuite.google.com)
   - Login with the same Google account you used for Google Cloud
   - You may need to accept terms of service for AI Studio

2. **Navigate to API Keys**
   - Look for "Get API Key" or "API Key" in the navigation.
   - Or go directly to [makersuite.google.com/app/apikey](https://makersuite.google.com/app/apikey)
   
3. **Create a New API Key**
   - Click **"Create API Key"** button
   - Choose "Create API key in new project" (recommended)
   - Or select an existing Google Cloud project if you prefer

   ![Gemini API Key Location](/img/gemini_API_key.png)

4. **Copy Your API Key**
   - After creation, you'll see your new API key
   - Click the copy icon to copy the key
   - ⚠️ **Important:** Save this immediately! You won't be able to see it again
   - The key starts with `AIzaSy...`
   - Save this as your `GEMINI_API_KEY`

### Step 4: Configure Vercel Environment Variables (Detailed)

#### Adding Environment Variables in Vercel Dashboard

1. **Access Your Vercel Project**
   - Go to [vercel.com](https://vercel.com) and login
   - Click on your deployed ClimateCloset project

2. **Navigate to Settings**
   - Click the **"Settings"** tab at the top of your project page
   - In the left sidebar, click **"Environment Variables"**

   ![Vercel Settings Menu](/img/settings_environment_variables.png)

3. **Add Each Environment Variable**
   
   For each variable, you'll:
   - Click **"Add New"** button
   - Enter the **Name** (exact spelling matters!)
   - Enter the **Value** (paste your copied keys)
   - Select **Environment**: Choose "Production", "Preview", and "Development" (all three)
   - Click **"Save"**

   **Variables to add:**
   
   **Variable 1:**
   - **Name**: `NEXT_PUBLIC_SUPABASE_URL`
   - **Value**: Your Supabase Project URL (from Step 1)
   
   **Variable 2:**
   - **Name**: `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - **Value**: Your Supabase anon key (from Step 1)
   
   **Variable 3:**
   - **Name**: `NEXT_PUBLIC_API_BASE_URL`
   - **Value**: Your Google Cloud Function URL (from backend deployment)
   - Example: `https://us-central1-climatecloset-app-123456.cloudfunctions.net/climatecloset-api`

   ![Vercel Add Environment Variable](/img/add_vercel_env_var.png)

4. **Redeploy to Apply Changes**
   - After adding all variables, go to the **"Deployments"** tab
   - Click the **"⋯"** menu on your latest deployment
   - Click **"Redeploy"** to apply the new environment variables

### Step 5: Weather API Setup (No API Key Required)

The National Weather Service API is completely free and requires no registration or API key! However, it's configured for **Evanston, Illinois** by default.

#### To Change Location (Optional)

If you want weather for a different city:

1. **Find Your Weather Grid Coordinates**
   - Go to [weather.gov](https://weather.gov)
   - Enter your city/zip code in the search
   - Click on your location
   - Look at the URL - it will show your grid coordinates
   - Example: For Chicago, the URL might show `LOT/73,81`

2. **Update Backend Code**
   - In your `backend/weather_api.py` file, update these lines:
   ```python
   grid_id = 'LOT'    # Your grid ID (e.g., 'LOT' for Chicago area)
   grid_x = 73        # Your X coordinate
   grid_y = 81        # Your Y coordinate
   ```

---

## Environment Variables Configuration

### Complete Environment Variables List

#### Backend (.env.yaml)
```yaml
SUPABASE_URL: "https://your-project.supabase.co"
SUPABASE_SERVICE_KEY: "your-service-role-key-here"
SUPABASE_JWT_SECRET: "your-jwt-secret-here"
GEMINI_API_KEY: "your-gemini-api-key-here"
```

#### Frontend (.env.local)
```bash
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-public-key-here
NEXT_PUBLIC_API_BASE_URL=https://us-central1-YOUR_PROJECT_ID.cloudfunctions.net/climatecloset-api
```

### Security Best Practices

1. **Never commit secrets to git**:
   ```bash
   # Add to .gitignore
   .env.local
   .env.yaml
   .env
   ```

2. **Use different keys for different environments**
3. **Regularly rotate API keys**
4. **Use Supabase service role key only in backend**
5. **Use anon key only in frontend**

---

## Testing the Application

### Step 1: Test Database Connection

1. Try creating an account in your deployed frontend
2. Check if user appears in Supabase **Authentication** → **Users**
3. Verify profile created in **Table Editor** → **profiles**

### Step 2: Test Backend Endpoints

```bash
# Test weather endpoint
curl "https://your-cloud-function-url/get_weather"

# Test authenticated endpoint (replace with actual token)
curl -H "Authorization: Bearer YOUR_JWT_TOKEN" \
     "https://your-cloud-function-url/get_wardrobe"
```

### Step 3: Test Full Workflow

1. **Sign up** for an account
2. **Add clothing items** (test image analysis)
3. **Create an outfit** (test AI generation)
4. **View analytics** (test wardrobe insights)
5. **Submit feedback** (test feedback system)

### Step 4: Monitor Logs

- **Frontend**: Check Vercel function logs
- **Backend**: Check Google Cloud Functions logs:
  ```bash
  gcloud functions logs read climatecloset-api --limit 50
  ```
- **Database**: Check Supabase logs in dashboard

---

## Troubleshooting

### Common Issues and Solutions

#### 1. Database Connection Issues

**Error**: "Invalid JWT" or "Authentication failed"
**Solution**:
- Verify JWT secret matches between Supabase and backend
- Check service role key is correct
- Ensure RLS policies are properly configured

#### 2. CORS Issues

**Error**: "CORS policy blocked"
**Solution**:
- Verify `CORS_HEADERS` in backend `main.py`
- Check API URL is correct in frontend
- Ensure functions allow unauthenticated access

#### 3. Environment Variables Not Loading

**Error**: Environment variables are undefined
**Solution**:
- Verify `.env.yaml` is in correct location (backend root)
- Check Vercel environment variables are set
- Restart development servers after adding variables
- Ensure variable names match exactly (including prefixes)

#### 4. Image Upload Issues

**Error**: "Failed to analyze clothing item"
**Solution**:
- Check Gemini API key is valid
- Verify image is properly base64 encoded
- Check image size (should be < 10MB)
- Ensure proper image format (JPEG/PNG)

#### 5. Deployment Failures

**Backend Deployment Error**:
```bash
# Check function logs
gcloud functions logs read climatecloset-api --limit 10

# Redeploy with verbose output
gcloud functions deploy climatecloset-api --verbosity=debug
```

**Frontend Deployment Error**:
- Check build logs in Vercel dashboard
- Verify all dependencies are in `package.json`
- Check for TypeScript errors
- Ensure environment variables are set

#### 6. Weather API Issues

**Error**: Weather data not loading
**Solution**:
- Check if National Weather Service API is accessible
- Verify grid coordinates are correct for your location
- Check network connectivity
- Review weather_api.py error handling

### Performance Optimization

1. **Database Indexes**: Already included in schema
2. **Function Memory**: Increase if needed:
   ```bash
   gcloud functions deploy climatecloset-api --memory 1024MB
   ```
3. **Caching**: Analytics are cached automatically
4. **Image Optimization**: Consider using Vercel Image Optimization

### Monitoring and Maintenance

1. **Set up alerts** for function failures
2. **Monitor database usage** in Supabase
3. **Check API quotas** for Gemini API
4. **Regular backups** of database
5. **Update dependencies** regularly

---

## Production Considerations

### Security Hardening

1. **Enable 2FA** on all service accounts
2. **Restrict API access** to specific domains
3. **Monitor unusual activity** 
4. **Regular security audits**

### Scaling Considerations

1. **Database**: Supabase scales automatically
2. **Backend**: Cloud Functions scale automatically
3. **Frontend**: Vercel scales automatically
4. **Rate Limiting**: Consider adding rate limits for API calls

### Cost Management

1. **Monitor usage** of all services
2. **Set billing alerts** 
3. **Optimize function execution time**
4. **Consider regional deployment** to reduce latency

---

## Support

If you encounter issues not covered in this guide:

1. Review service-specific documentation:
   - [Supabase Docs](https://supabase.com/docs)
   - [Vercel Docs](https://vercel.com/docs)
   - [Google Cloud Functions Docs](https://cloud.google.com/functions/docs)
2. Check service status pages for outages

---

## Summary

After completing this guide, you will have:

✅ **Database**: Fully configured Supabase PostgreSQL database with RLS  
✅ **Backend**: Deployed Google Cloud Functions with all endpoints  
✅ **Frontend**: Deployed Next.js application on Vercel  
✅ **Integrations**: Connected weather API, Gemini AI, and authentication  
✅ **Security**: Proper environment variable management and access controls  

Your ClimateCloset application should now be fully functional and accessible at your Vercel deployment URL!