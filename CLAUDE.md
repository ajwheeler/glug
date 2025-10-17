# Glug - Development Guide

## Project Overview
Glug is a baby milk tracker - a single-page web app that tracks milk consumption with one-click logging. Part of the Toolshed platform (multiple independent apps sharing a single Supabase backend).

## Tech Stack
- Single HTML file with inline CSS and JavaScript
- Supabase for backend (PostgreSQL database + authentication)
- Supabase JS client library (loaded via CDN)
- No build process, no framework

## Key Implementation Details

### Table Naming
- Use `glug_feeds` as the table name (NOT `milk_feeds`)
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
  oz: INTEGER           // Always 1 per click
}
```

### Display Logic
- **15-minute chunking**: Group feeds by 15-minute windows for display
  - Example: clicks at 2:23, 2:33, 2:36 → display as "2:30 PM - 3 oz"
  - Round DOWN to nearest 15-minute mark
- **Rolling totals**:
  - Last 3 hours (from current time)
  - Last 24 hours (from current time)
  - Today's total (3am to now, local timezone)
- **Timezone**: All times use device's local timezone (no UTC conversion)

### UI Requirements
- Mobile-first design (large touch targets)
- Big button for adding oz
- Clear display of all three time-window totals
- Recent feed history (15-min chunks, 10 most recent with data)
- Simple date display at top
- Purple gradient background (#667eea to #764ba2)
- White cards for content
- Clean, minimal design

## Development Workflow
1. Build/modify `glug.html` as a complete standalone file
2. Keep the Supabase credential placeholders intact
3. Test locally (will need mock data or actual credentials to connect)
4. User will manually add credentials and deploy

## Future Enhancements
- Manual delete/edit of individual feeds (not in initial build)

## Important Constraints
- Keep everything in a single HTML file
- No build process or bundler
- No npm packages (CDN only for Supabase client)
- Must work on mobile browsers
- Stay within Supabase free tier limits