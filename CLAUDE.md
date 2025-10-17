# Glug - Development Guide

## Project Overview
Glug is a baby milk tracker - a single-page web app that tracks milk consumption with one-click logging. One of multiple independent apps (collectively: toolshed) sharing a single Supabase backend.

## Tech Stack
- Single HTML file with inline CSS and JavaScript
- Supabase for backend (PostgreSQL database + authentication)
- Supabase JS client library (loaded via CDN)
- No build process, no framework

## Key Implementation Details

### Table Naming
- Use `glug_feeds` as the table name
- This is part of Toolshed's naming convention: `{appname}_tablename`
- All queries must reference `glug_feeds`

### Authentication
- Simple email/password login via Supabase Auth
- Login modal on first visit
- Session persists automatically via Supabase client
- Single hardcoded user (created manually in Supabase dashboard)

### Data Model
Each click records:
```javascript
{
  id: SERIAL,           // Auto-generated
  timestamp: TIMESTAMPTZ, // Auto-set to NOW()
  oz: INTEGER           // 1 = 1/2 oz
}
```

### Display Logic
- **15-minute chunking**: Group feeds by 15-minute windows for display
  - Example: clicks at 2:23, 2:33, 2:36 → display as "2:30 PM - 3 oz"
  - Round to NEAREST 15-minute mark
- **Rolling totals**:
  - Last 3 hours (from current time)
  - Last 24 hours (from current time)
- **Timezone**: All times use device's local timezone (no UTC conversion)

### UI Requirements
- Mobile-first design (large touch targets)
- Big buttons

## Development Workflow
1. Build/modify `index.html` as a complete standalone file
2. Keep the Supabase credentials intact
3. Test locally (will need mock data or actual credentials to connect)
4. User will manually add credentials and deploy

## Important Constraints
- Keep everything in a single HTML file
- No build process or bundler
- No npm packages (CDN only for Supabase client)
- Must work on mobile browsers
- Stay within Supabase free tier limits