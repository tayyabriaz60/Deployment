# Changelog

## [Unreleased]

### Added
- Security-focused environment template (`env.example`) and git ignore rules.
- Centralized logging configuration with console and file handlers.
- Structured error handling utilities and request logging middleware.
- Health endpoint with database connectivity and pool statistics.

### Changed
- Replaced Gemini analysis calls with true async HTTP client implementation (httpx).
- Refactored background tasks to create independent database sessions.
- Hardened authentication by validating `SECRET_KEY` and tightening Socket.IO auth handling.
- Optimized SQL queries with server-side filtering and added database indexes.
- Updated frontend to escape user-provided content to prevent XSS.
- Streamed CSV exports to avoid high memory usage.

### Fixed
- Database connection leaks, pool exhaustion, and fake async design.
- Multiple cross-site scripting vectors and insecure frontend rendering.
- Production readiness gaps around logging, configuration, and documentation accuracy.
- Removed deprecated `pool_use_lifo` parameter for SQLAlchemy 2.0 compatibility.

### Security
- Added comprehensive SECRET_KEY validation (no defaults, minimum length check).
- Implemented HTML escaping for all user-provided content in frontend.
- Enhanced Socket.IO authentication validation.
- Added proper CORS configuration guidance for production.

### Performance
- Optimized database queries to filter in SQL instead of Python.
- Added composite indexes for common query patterns.
- Configured database connection pool with proper settings (pool_size=20, max_overflow=10).
- Added pool health checks with `pool_pre_ping=True`.

### Infrastructure
- Created structured logging system with file rotation.
- Added request/response logging middleware.
- Implemented proper error handling with structured responses.
- Added database connection health checks.

## [1.0.0] - Initial Release

### Features
- Patient feedback submission system
- AI-powered sentiment and urgency analysis using Google Gemini
- Real-time alerts via Socket.IO
- Staff dashboard with filtering and analytics
- Action tracking and management
- CSV export functionality
- JWT-based authentication with role-based access control
