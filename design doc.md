# Glug - Baby Milk Tracker

## Platform
Part of **Toolshed** - a single Supabase project hosting multiple tiny web apps with shared infrastructure.

## Overview
Single-page web application to track daily milk consumption for a baby. One button increments the daily total.

## Core Features
- Single button to add 1 oz per click
- Record each click with timestamp
- Display feed history grouped in 15-minute chunks
- Supabase authentication
- Backend data persistence via Supabase

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
  - Date display
  - Summary stats: Last 3 hours / Last 24 hours / Today's total
  - Large "Add 1 oz" button
  - Feed history (15-min chunks, 10 most recent)
- Purple gradient background (#667eea to #764ba2)
- White cards with clean typography

## Implementation Notes
- **Time calculations**: All times in local timezone
- **Day reset**: "Today" = 3am to now (not midnight)
- **15-min chunking**: Round down (2:23 → 2:15, 2:33 → 2:30)
- **History display**: Show only 10 most recent chunks with data
- **Mobile-first**: Large touch targets throughout

## Toolshed Architecture
- **Single Supabase project** serves as shared backend for all apps
- **Prefixed tables** keep data organized (glug_feeds, otherapp_data, etc.)
- **Independent deployment**: Each app is standalone HTML file
- **Shared auth**: One login across all Toolshed apps
- **Free tier**: 500MB database, unlimited API requests

