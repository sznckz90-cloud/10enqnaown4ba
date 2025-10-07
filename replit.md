# Overview

CashWatch is a React-based web application that allows users to earn money by watching advertisements. The platform features a gamified experience with daily streaks, referral systems, and withdrawal capabilities. Built with a modern full-stack architecture using React, Express, PostgreSQL, shadcn/ui components, and Telegram Bot integration.

# Recent Changes (October 2025)

## Spin & Win Feature (October 7, 2025)
- **New Standalone Page**: Created `/spin` page accessible from bottom navigation with dedicated Spin & Win functionality
- **Ad Watching Integration**: Users can watch ads directly on the Spin page to earn both TON rewards and free spins
- **Earning Mechanics**:
  - 1 free spin earned per 10 ads watched
  - Maximum 26 daily spins (160 ads = 16 free spins + 10 extra spins)
  - Each ad watched earns 0.0002 TON + progress toward spins
- **Weighted Random Rewards**: 8 reward tiers from 0.000071 to 1 TON with probability-based selection
  - Very High: 0.000071 TON (40% chance)
  - High: 0.00029 TON (30% chance)
  - Medium: 0.0006 TON (15% chance)
  - Medium-Low: 0.0013 TON (8% chance)
  - Low: 0.0062 TON (4% chance)
  - Very Low: 0.031 TON (2% chance)
  - Extremely Low: 0.52 TON (0.9% chance)
  - Ultra Rare: 1 TON (0.1% chance)
- **Database Schema**: Added spin tracking columns (freeSpinsAvailable, extraSpinsUsed, totalSpinsUsed, lastSpinDate)
- **Daily Reset**: All spin counters automatically reset at 00:00 UTC alongside other daily metrics
- **UI Features**:
  - Visual progress bar showing ads watched toward next spin
  - Animated spin wheel with TON coin icon (💎)
  - Comprehensive reward probability table
  - Real-time spin availability display
- **Files**:
  - `client/src/pages/Spin.tsx` (new standalone page with ad watching)
  - `server/routes.ts` (added `/api/spin/status` and `/api/spin/perform` endpoints)
  - `server/storage.ts` (spin logic, weighted random selection, daily reset integration)
  - `shared/schema.ts` (database schema updates)
  - `server/migrate.ts` (automatic migration for spin columns)

## Bot Account Dashboard Update
- **New Dashboard Format**: Updated the Telegram bot's `/account` command to display a clean, emoji-free dashboard
- **Format**: Shows Username, User ID, Joined date, Balance (TON), Earned today (TON), Earned total (TON), Referrals count, and Referral Income (TON)
- **Refresh Button**: Added inline "🔄 Refresh" button that updates all data in real-time without spam
- **Implementation**: New `formatAccountDashboard()` function in `server/telegram.ts` with callback handler for refresh

## Referral System Anti-Spam Fix
- **Disabled Real-time Notifications**: Removed instant bot messages when referred friends watch ads
- **Data Still Tracked**: All referral commissions (10% of ad earnings) continue to be calculated and stored in the database
- **View Total**: Users can see their total referral income via the dashboard refresh button or app
- **Location**: Modified `processReferralCommission()` in `server/storage.ts`

## Affiliates Page (App Integration)
- **New Page**: Created `/affiliates` page accessible from bottom navigation
- **Features**:
  - Displays referral link with copy and share functionality
  - Shows total referral count and total referral income in real-time
  - Explains reward structure: 0.002 TON instant bonus + 10% lifetime commission
  - Includes warning about avoiding fake accounts
- **Files**: 
  - `client/src/pages/Affiliates.tsx` (new page)
  - `client/src/App.tsx` (added route)
  - `client/src/components/Layout.tsx` (added navigation item)
  - `server/routes.ts` (added `/api/referrals/stats` endpoint)

# User Preferences

Preferred communication style: Simple, everyday language.

# System Architecture

## Frontend Architecture
- **Framework**: React with TypeScript using Vite as the build tool
- **Routing**: Wouter for client-side routing with protected routes based on authentication state
- **State Management**: TanStack Query (React Query) for server state management and caching
- **UI Components**: shadcn/ui component library with Radix UI primitives and Tailwind CSS for styling
- **Form Handling**: React Hook Form with Zod validation schemas
- **Design System**: Custom CSS variables for theming with light/dark mode support

## Backend Architecture
- **Runtime**: Node.js with Express.js framework
- **Database ORM**: Drizzle ORM with PostgreSQL dialect for type-safe database operations
- **API Design**: RESTful API with JSON responses and proper HTTP status codes
- **Session Management**: Express sessions with PostgreSQL session store for persistence
- **Development Setup**: ESBuild for production bundling, tsx for development server

## Authentication & Authorization
- **Authentication Provider**: Replit OAuth integration using OpenID Connect (OIDC)
- **Session Strategy**: Server-side sessions with secure HTTP-only cookies
- **Authorization Pattern**: Middleware-based route protection with user context injection
- **User Management**: Automatic user creation/updates on authentication with profile sync

## Data Storage
- **Primary Database**: PostgreSQL with connection pooling via Neon serverless
- **Schema Management**: Drizzle migrations with schema definitions in TypeScript
- **Key Entities**:
  - Users with balance tracking, streak counters, and referral codes
  - Earnings history with metadata for different earning types
  - Withdrawals with status tracking and payment method details
  - Referral relationships for user acquisition tracking
  - Sessions table for authentication persistence

## Business Logic Features
- **Ad Watching System**: Integration with external ad providers for reward distribution
  - Earns 0.0002 TON per ad watched
  - Daily limit of 160 ads
  - 30-second cooldown between ads
  - Integrated with Spin & Win system (1 free spin per 10 ads)
- **Spin & Win System**: Gamified reward system with weighted random selection
  - Free spins: 1 spin per 10 ads watched (max 16 from 160 ads)
  - Extra spins: 1 spin per 2 ads watched (max 10 extra spins)
  - Total daily maximum: 26 spins
  - Reward tiers: 8 different TON amounts from 0.000071 to 1 TON
  - Probability-weighted selection ensures rare big rewards
  - All spin data resets daily at 00:00 UTC
- **Daily Streak Rewards System**: Gamified consecutive day tracking with ad-based rewards
  - **Normal Reward**: 0.0001 TON per daily claim
  - **5-Day Bonus**: 0.0003 TON bonus on the 5th consecutive day, then streak resets to 0
  - **Ad Integration**: Users must watch an ad before claiming their daily reward (same format as Watch-to-Earn)
  - **Channel Membership**: Must join @PaidAdsNews Telegram channel to claim rewards
  - **Real-time Verification**: Channel membership checked every 5 seconds with instant unlock
  - **Live Timer**: 24-hour UTC-based countdown showing time until next claim
  - **Streak Tracking**: Consecutive day logic with proper reset on missed days
- **Referral Program**: User-generated referral codes with commission tracking
- **Withdrawal System**: Multiple payment methods with pending/completed status tracking
- **Earnings Analytics**: Time-based earning summaries (daily, weekly, monthly)

# External Dependencies

## Core Infrastructure
- **Database**: Neon PostgreSQL serverless database with connection pooling
- **Authentication**: Replit OAuth/OIDC service for user authentication
- **Session Storage**: PostgreSQL-backed session storage using connect-pg-simple
- **Telegram Bot API**: Channel membership verification using node-telegram-bot-api
  - Required for daily streak channel verification
  - Bot must be added as admin to @PaidAdsNews channel for membership checks

## Frontend Libraries
- **UI Framework**: Radix UI primitives for accessible component foundations
- **Styling**: Tailwind CSS with custom CSS variables for theming
- **State Management**: TanStack Query for server state caching and synchronization
- **Form Management**: React Hook Form with Zod schema validation
- **Date Handling**: date-fns for date manipulation and formatting

## Development Tools
- **Build System**: Vite with React plugin and TypeScript support
- **Code Quality**: TypeScript for type safety across the entire stack
- **Development Experience**: Replit-specific plugins for cartographer and error overlays
- **Package Management**: npm with lockfile for dependency consistency

## Ad Integration
- **Ad Provider**: External advertisement service accessed via global window object
- **Reward System**: Fixed reward rates per ad view with metadata tracking
- **Anti-Fraud**: Daily ad viewing limits and timestamp verification