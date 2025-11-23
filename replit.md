# Overview

InBack/Clickback is a Flask-based real estate platform specializing in cashback services for new construction properties in the Krasnodar region, expanding across Krasnodarsky Krai and the Republic of Adygea. It connects buyers and developers, offers property listings, streamlines application processes, and integrates CRM tools. The platform provides unique cashback incentives, an intuitive user experience, intelligent property search with interactive maps, residential complex comparisons, user favorites, a manager dashboard for client and cashback tracking, and robust notification and document generation capabilities. The project aims to capture a significant market share by offering a compelling value proposition to both buyers and developers in the new construction segment.

# User Preferences

Preferred communication style: Simple, everyday language.

**Design Preferences:**
- Brand color: rgb(0 136 204) = #0088CC - consistently applied across entire dashboard
- No purple/violet/fuchsia colors in UI

# System Architecture

## Frontend

The frontend utilizes server-side rendered HTML with Jinja2 and CDN-based TailwindCSS for a mobile-first, responsive design. Interactivity is handled by modular vanilla JavaScript, enabling features like smart search, real-time filtering, Yandex Maps integration, property comparison, and PDF generation. Key UI/UX features include AJAX-powered sorting/filtering, interactive map pages, mobile-optimized search, saved searches, dynamic results, property alerts, and a city selector. The dashboard features gradient stat cards, enhanced loading states, a collapsible sidebar, real-time badge counters, and unified tabs for Favorites, Saved Searches, and Comparison. UI elements consistently use the brand color #0088CC and avoid purple/violet/fuchsia tones. Recent UI updates include a two-step phone registration process with modern styling, a modal login system, and unified icon colors.

## Backend

Built with Flask, the backend employs an MVC pattern with blueprints and SQLAlchemy with PostgreSQL. It includes Flask-Login for session management and RBAC (Regular Users, Managers, Admins), robust security, and custom parsing for Russian address formats. The system supports phone verification via SMS, manager-to-client presentation delivery, multi-city data management, and city-aware data filtering. Performance is optimized with Flask-Caching and batch API endpoints. A key feature is the intelligent automatic detection system for sold properties, marking them as inactive and notifying users when properties disappear from external data sources.

## Data Storage

PostgreSQL, managed via SQLAlchemy, serves as the primary database, storing Users, Managers, Properties, Residential Complexes, Developers, Marketing Materials, transactional records, and search analytics.

## Authentication & Authorization

The system supports Regular Users, Managers, and Admins through a unified Flask-Login system with dynamic user model loading and extended session duration.

**Phone-Only Authentication (November 2025):**
- **Login**: Exclusively via phone number + SMS verification code. No email/password login for regular users.
- **Registration Flow**: 
  1. Phone + SMS verification code
  2. Auto-generated secure temporary password (using `secrets` module) sent via SMS
  3. Automatic login with temporary password
  4. Redirect to dashboard with modal window for profile completion (ФИО, email, telegram)
  5. No separate registration page - all handled inline
- **Password Management**: Temporary passwords are cryptographically secure (8 characters, alphanumeric). Users can change password in dashboard settings.
- **Profile Completion**: Dashboard modal auto-shows for users with `profile_completed=False`. Modal includes ФИО (required), email (optional), telegram (optional). "Skip" button allows later completion.
- **UI**: Both header modal and /login page show unified phone-only interface with "Войти по телефону" heading
- **Security**: All flows protected by CSRF tokens (`@require_json_csrf`), rate limiting (60s cooldown), code expiration (10 min), 3 attempts max. RED SMS API integration with MD5 signature authentication.
- **User Experience**: Single streamlined flow handles both login and registration automatically based on phone existence in database. New users receive welcome SMS with temporary password.

## Intelligent Address Parsing & Universal Multi-City Smart Search System

This system leverages DaData.ru for address normalization and Yandex Maps Geocoder API for geocoding. It features auto-enrichment for new properties, optimized batch processing, smart caching, and city-aware address suggestions. The Universal Smart Search dynamically loads all active cities from the database, offering scalable architecture for new cities. It includes intelligent city detection, automatic city switching based on search queries, and smart suggestions covering cities, residential complexes, districts, and streets, similar to major real estate portals.

## Balance Management System

A production-ready system with `UserBalance`, `BalanceTransaction`, and `WithdrawalRequest` models, including a service layer for credit/debit operations, withdrawal workflows, dedicated API endpoints, UI integration, auto-cashback, and notifications. All financial amounts use Decimal precision.

## Comprehensive SEO Optimization

The platform implements production-ready multi-city SEO, including Canonical URLs, City-Aware Meta Tags, JSON-LD Structured Data (schema.org), Regional Variations, Comprehensive Sitemap, robots.txt Configuration, HSTS Headers, and Yandex.Metrika analytics.

## Parser Integration System

A universal service (`ParserImportService`) for automatic data import from external parsers, supporting full hierarchy (Developer → ЖК → Building → Property) with automatic SEO-friendly Latin slug generation. It includes smart update logic to prevent duplicates, multi-city support, and efficient batch processing for large datasets.

## Comprehensive District & Neighborhood System

The system includes a full reference catalog of districts and neighborhoods with hierarchical taxonomy support. The `District` model features a `district_type` column (`admin` | `micro`) to distinguish administrative districts from microdistricts. The API endpoint `/api/districts/<city_id>` implements conditional filtering: for Sochi (city_id=2), it returns only 4 administrative districts (Адлерский, Лазаревский, Хостинский, Центральный) matching Yandex.Nedvizhimost UX; for other cities, it returns all districts. Properties link to authoritative District table records with additional text fields for fallback search. Search suggestions include districts with property counts. All developer filtering is city-scoped via `/api/developers?city_id` with AJAX-driven UI population.

# External Dependencies

## Third-Party APIs

-   **SendGrid**: Email sending.
-   **OpenAI**: Smart search and content generation.
-   **Telegram Bot API**: User notifications and communication.
-   **Yandex Maps API**: Interactive maps, geocoding, and location visualization.
-   **DaData.ru**: Address normalization, suggestions, and geocoding.
-   **RED SMS**: Russian SMS service for phone verification and authentication.
-   **Google Analytics**: User behavior tracking.
-   **LaunchDarkly**: Feature flagging.
-   **Chaport**: Chat widget.
-   **reCAPTCHA**: Spam and bot prevention.
-   **ipwho.is**: IP-based city detection.

## Web Scraping Infrastructure

-   `selenium`, `playwright`, `beautifulsoup4`, `undetected-chromedriver`: Used for automated data collection.

## PDF Generation

-   `weasyprint`, `reportlab`: Used for generating property detail sheets, comparison reports, and cashback calculations.

## Image Processing

-   `Pillow`: Used for image resizing, compression, WebP conversion, and QR code generation.