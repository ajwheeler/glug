# Glug - Baby Milk Tracker

## Platform
Part of **Toolshed** - a single Supabase project hosting multiple tiny web apps with shared infrastructure.

## Overview
Single-page web application to track daily milk consumption for a baby. One button increments the daily total.

## Core Features
- Two buttons: "+1 oz" and "+½ oz"
- Each click records a timestamp in the database
- Display feed history grouped in 15-minute chunks (rounded to nearest)
- Supabase authentication
- Backend data persistence via Supabase

### Oz Storage Model
- Each database record represents 0.5 oz (stored as `oz: 1`)
- "+1 oz" button inserts 2 records (2 × 0.5 oz = 1 oz)
- "+½ oz" button inserts 1 record (1 × 0.5 oz = 0.5 oz)
- Display logic multiplies each record by 0.5 when calculating totals

## User Flow
1. User visits site → login modal appears
2. After auth → sees summary stats and add button
3. Click button → adds 1 oz with timestamp
4. Display updates immediately with new totals

### Supabase Backend (Toolshed)
- **Table schema**:
  ```sql
  CREATE TABLE glug_feeds (
    id SERIAL PRIMARY KEY,
    timestamp TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    oz INTEGER NOT NULL DEFAULT 1
  );
  ```
- **Naming convention**: `{appname}_tablename` (e.g., glug_feeds)
- **Benefits**:
  - Shared Supabase project across all Toolshed apps (free tier)
  - Multi-device access
  - Automatic backups

### Authentication
- Supabase Auth with email/password
- Session auto-managed by Supabase client
- RLS policies protect data (authenticated users only)

### UI Components
- Login modal (first visit)
- Main view:
  - Summary stats: Last 3 hours / Last 24 hours
  - Time since last feed
  - Large "+1 oz" button and "+½ oz" button
  - Feed history (15-min chunks, 10 most recent)
- Purple gradient background (#667eea to #764ba2)
- White cards with clean typography

## Implementation Notes
- **Time calculations**: All times in local timezone
- **History display**:
  - Group feeds by rounding to nearest 15-minute mark
  - Show only 10 most recent chunks with data
  - Each chunk shows aggregated total from all feeds in that window
- **Mobile-first**: Large touch targets throughout

## Toolshed Architecture
- **Single Supabase project** serves as shared backend for all apps
- **Prefixed tables** keep data organized (glug_feeds, otherapp_data, etc.)
- **Independent deployment**: Each app is standalone HTML file
- **Shared auth**: One login across all Toolshed apps
- **Free tier**: 500MB database, unlimited API requests

