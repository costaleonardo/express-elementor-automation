# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**ContentFlow MVP** - An automation system that creates WordPress content from Asana tasks with file attachments. This is a Node.js-based webhook system that processes PDF/DOCX documents and creates WordPress draft posts via REST API.

## Architecture Components

Based on the PRD, the system consists of:

- **Webhook Server**: Node.js/Express application handling Asana webhooks
- **Task Processor**: Handles Asana task data and attachment retrieval  
- **File Processor**: PDF and DOCX text extraction using pdf-parse and mammoth
- **WordPress API Client**: REST API integration for post creation
- **Simple Dashboard**: Basic status monitoring

## Technology Stack

- **Backend**: Node.js, Express
- **File Processing**: pdf-parse (PDF), mammoth (DOCX) 
- **WordPress Integration**: WordPress REST API
- **Hosting**: Cloud deployment (Heroku/DigitalOcean)

## Data Flow

1. Asana webhook triggers on task creation in designated project
2. Server retrieves task data and attachments via Asana API
3. Downloads and validates supported files (PDF/DOCX, max 10MB)
4. Extracts text content from documents
5. Maps content to WordPress post structure with ACF fields
6. Creates WordPress draft post
7. Logs success/failure status

## Content Mapping

- **Task name** → Post Title (primary)
- **First line/heading from document** → Post Title (fallback)
- **Full text content** → Post Content (ACF field)
- **First paragraph** → Post Excerpt (ACF field)
- **Task description** → Post notes/meta
- **Task creation timestamp** → Post date

## External Dependencies

- **Asana API**: Personal Access Token with webhook and task read permissions
- **WordPress**: ACF Pro plugin, custom post type with ACF field group, REST API enabled, application passwords configured
- **File Processing**: Supports PDF (text-based, no OCR) and DOCX only

## MVP Limitations

- Single file processing only (first supported document)
- Text extraction only (no images, OCR, or structured parsing)
- Draft posts only (no auto-publishing)
- Tasks without attachments are logged but not processed
- No retroactive processing (new tasks only)
- Basic error handling and logging

## Success Criteria

- 90%+ processing success rate for supported file types
- ≤2 minutes processing time from task creation to WordPress draft
- Graceful handling of tasks without attachments