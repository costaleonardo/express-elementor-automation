# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**ContentFlow MVP** - An automation system that creates WordPress content from Asana tasks with file attachments. This is a Node.js-based webhook system that processes PDF/DOCX documents and creates WordPress draft posts via REST API.

## Development Commands

### Common Development Tasks
```bash
# Development with auto-reload
pnpm run dev

# Production start
pnpm start

# Run all tests
pnpm test

# Run specific test file
pnpm test -- tests/services/asana.test.js

# Lint code
pnpm run lint
pnpm run lint:fix

# Format code with Prettier
pnpm run format

# Debug mode (when implemented)
DEBUG=contentflow:* pnpm run dev
```

### Package Management
- **Package Manager**: Uses pnpm (not npm/yarn)
- **Module System**: ES modules (`"type": "module"` in package.json)
- **Entry Point**: `src/index.js`

## Architecture & Code Structure

### High-Level Architecture
The system follows an event-driven architecture with webhook processing:

```
Asana Webhook → Express Server → Processing Pipeline → WordPress API
```

### Core Processing Pipeline
1. **Webhook Receipt** (`/webhook/asana` endpoint)
2. **Task Data Fetching** (Asana API integration)
3. **File Download & Validation** (PDF/DOCX, 10MB max)
4. **Content Extraction** (pdf-parse, mammoth libraries)
5. **Content Mapping** (Task → WordPress post structure)
6. **WordPress Post Creation** (REST API with ACF fields)

### Project Structure
```
src/
  ├── controllers/     # HTTP request handlers (webhook, health endpoints)
  ├── services/        # Core business logic
  │   ├── asana/       # Asana API client and task processing
  │   ├── processors/  # File processing (PDF, DOCX)
  │   └── wordpress/   # WordPress API client and post creation
  ├── utils/          # Shared utilities and helpers
  ├── middleware/     # Express middleware (auth, validation, logging)
  └── index.js        # Application entry point and server setup
```

### Key Integration Points
- **Asana Integration**: Personal Access Token authentication, webhook signature verification
- **WordPress Integration**: Application passwords, ACF Pro field mapping
- **File Processing**: Stream-based processing for memory efficiency with large files

## Content Mapping Strategy

**Critical mapping logic** (affects all content creation):
- **Task name** → Post Title (primary source)
- **Document first line/heading** → Post Title (fallback)
- **Full document content** → ACF content field
- **First paragraph** → ACF excerpt field
- **Task metadata** → WordPress post meta

## Environment Configuration

Required environment variables (see `.env.example`):
- `ASANA_ACCESS_TOKEN` - Asana API authentication
- `ASANA_WEBHOOK_SECRET` - Webhook signature verification
- `WORDPRESS_API_URL` - WordPress site REST API endpoint
- `WORDPRESS_USERNAME` & `WORDPRESS_APP_PASSWORD` - WordPress authentication

## MVP Constraints

**Explicitly excluded features** (do not implement):
- Multi-file processing (single file only)
- Image/OCR processing (text extraction only)
- Auto-publishing (drafts only)
- Retroactive processing (new tasks only)
- Advanced error recovery

## Error Handling Patterns

- **No attachments**: Log and skip (no error)
- **Unsupported files**: Log error, continue processing
- **API failures**: Implement retry with exponential backoff
- **Processing timeouts**: Configurable limits, graceful failure

## Development Notes

- **File Cleanup**: Temporary files must be cleaned up after processing
- **Memory Management**: Stream processing for large files to avoid memory issues
- **API Rate Limits**: Both Asana and WordPress APIs have rate limiting
- **Webhook Security**: Always verify Asana webhook signatures
- **ACF Dependencies**: WordPress site must have ACF Pro plugin and configured field groups