# Asana-WordPress Content Automation MVP
## Product Requirements Document

**Version:** 1.1  
**Date:** September 5, 2025  
**Project Codename:** ContentFlow MVP

---

## Executive Summary

Develop a minimal viable product that automatically creates WordPress content from Asana tasks with file attachments. The MVP focuses on demonstrating core automation functionality with basic document processing capabilities, triggered when designated tasks are created in Asana.

## Problem Statement

Content creation teams currently face manual overhead transferring project specifications from Asana tasks into WordPress posts, leading to delays and potential errors in the content publishing workflow.

## Success Criteria

- **Demo-Ready Automation**: Complete end-to-end workflow from Asana task creation to published WordPress post
- **Basic Document Processing**: Successfully extract content from common document formats attached to tasks
- **Reliable Core Function**: 90%+ success rate for supported file types during internal testing
- **Internal Stakeholder Buy-In**: Demonstrate clear value proposition for full development investment

## MVP Scope

### Core Features (Must-Have)

#### 1. Asana Integration
- **Webhook Handler**: Receive notifications when tasks are created in designated Asana projects
- **Task Data Retrieval**: Fetch task details including attachments upon creation
- **File Download**: Automatically download documents attached to the newly created task
- **Basic Authentication**: Connect to Asana API using Personal Access Token

#### 2. Document Processing
- **PDF Text Extraction**: Extract plain text content from PDF documents
- **Word Document Processing**: Extract text content from DOCX files
- **Simple Content Mapping**: Map extracted text to basic WordPress post structure
- **Attachment Validation**: Check for presence of supported file types on task creation

#### 3. WordPress Content Creation
- **Post Generation**: Create WordPress posts via REST API
- **Basic ACF Population**: Populate essential ACF fields (title, content, excerpt)
- **Draft Status**: All generated posts created as drafts for review

#### 4. Error Handling & Logging
- **Basic Error Logging**: Log processing failures with error details
- **Status Notifications**: Simple success/failure status reporting
- **No-Attachment Handling**: Clear logging when tasks created without attachments
- **Manual Fallback**: Clear documentation for manual intervention when automation fails

### Supported Content Flow

```
Asana Task Creation (with PDF/DOCX attachment) 
    ↓
Node.js Webhook Handler (triggered on task creation)
    ↓
Task Data & Attachment Retrieval
    ↓
File Download & Text Extraction
    ↓
WordPress Draft Post Creation
    ↓
Internal Review & Manual Publication
```

## Technical Specifications

### MVP Architecture

**Components:**
- **Webhook Server**: Node.js/Express application
- **Task Processor**: Asana task and attachment handler
- **File Processor**: PDF and DOCX text extraction
- **WordPress API Client**: REST API integration
- **Simple Dashboard**: Basic status monitoring

**Technology Stack:**
- **Backend**: Node.js, Express
- **File Processing**: pdf-parse, mammoth (for DOCX)
- **WordPress Integration**: WordPress REST API
- **Hosting**: Simple cloud deployment (Heroku/DigitalOcean)

### Data Flow

1. **Trigger**: Asana webhook fires when task is created in designated project
2. **Fetch**: Server retrieves complete task data including attachments
3. **Validate**: Check for presence of supported file attachments
4. **Download**: Download file(s) using Asana API
5. **Process**: Extract text content from document
6. **Transform**: Map content to WordPress post structure
7. **Create**: Generate WordPress draft post with ACF fields
8. **Notify**: Log success/failure status

### Minimum Content Mapping

**Task + Document → WordPress Post:**
- **Task name** → Post Title (primary)
- **First line/heading from document** → Post Title (fallback)
- **Full text content** → Post Content (ACF field)
- **First paragraph** → Post Excerpt (ACF field)
- **Task description** → Post notes/meta (if present)
- **File name** → Internal reference (post meta)
- **Task creation timestamp** → Post date

## Feature Limitations (MVP)

### Explicitly Excluded
- **Multi-file processing** (processes first supported document only)
- **Image handling** (text-only extraction)
- **Advanced content parsing** (no section detection)
- **Custom template assignment** (uses default post type)
- **Real-time publishing** (drafts only)
- **Content updates** (creation only, no updates)
- **User authentication** (single service account)
- **Advanced error recovery** (basic logging only)
- **Tasks without attachments** (logged but not processed)
- **Retroactive processing** (only new tasks trigger workflow)

### Supported File Types
- **PDF documents** (text-based only, no OCR)
- **Microsoft Word documents** (.docx format only)
- **Maximum file size**: 10MB

### WordPress Requirements
- **ACF Pro plugin** installed and activated
- **Custom post type** with designated ACF field group
- **REST API access** enabled
- **Application passwords** configured for API access

## User Stories

### Primary User Story
**As a** content team member  
**I want** to create an Asana task with a project document attached  
**So that** a WordPress draft post is automatically created with the document content

### Acceptance Criteria
- **Given** I create a new task in a designated Asana project
- **And** I attach a PDF or DOCX file to the task before saving
- **When** the task is successfully created
- **Then** a WordPress draft post is created within 2 minutes
- **And** the post contains the extracted document text
- **And** I receive confirmation of successful processing

### Secondary User Story
**As a** content manager  
**I want** the system to handle tasks without attachments gracefully  
**So that** the workflow doesn't break when tasks are created for planning purposes

### Acceptance Criteria
- **Given** I create a new task without attachments
- **When** the webhook is triggered
- **Then** the system logs "No supported attachments found"
- **And** no WordPress post is created
- **And** no error is raised

## Success Metrics

### Functional Metrics
- **Processing Success Rate**: ≥90% for tasks with supported file types
- **Processing Time**: ≤2 minutes from task creation to WordPress draft
- **Error Recovery**: Clear error messages for 100% of failures
- **Attachment Detection**: 100% accurate identification of tasks with/without attachments

### Business Metrics
- **Time Savings**: Demonstrate 80% reduction in manual content setup time
- **Stakeholder Approval**: Positive feedback from ≥3 internal stakeholders
- **Technical Validation**: Confirm scalability path for full feature development

## Implementation Timeline

### Phase 1: Core Development (3 weeks)
**Week 1**: Webhook handler for task creation and Asana integration  
**Week 2**: Document processing and WordPress API integration  
**Week 3**: End-to-end testing and error handling

### Phase 2: Internal Testing (1 week)
**Week 4**: Internal demo preparation and stakeholder testing

### Total Timeline: 4 weeks

## Risk Assessment

### Technical Risks
- **Missing attachments on creation**: Mitigated by clear user training and graceful handling
- **Document parsing failures**: Mitigated by supporting only common, well-structured document formats
- **API rate limiting**: Mitigated by processing single files and basic throttling
- **WordPress API issues**: Mitigated by using well-documented REST API endpoints
- **Webhook timing**: Mitigated by fetching complete task data after creation event

### Business Risks
- **User behavior change**: Mitigated by requiring attachments at task creation time
- **Limited stakeholder buy-in**: Mitigated by focusing on clear, demonstrable value
- **Scope creep during development**: Mitigated by strict MVP feature limitations
- **Integration complexity**: Mitigated by using established APIs and libraries

## Dependencies

### External Dependencies
- **Asana API access**: Personal Access Token with webhook and task read permissions
- **WordPress site**: Admin access for API configuration
- **Cloud hosting**: Basic server hosting for webhook endpoint

### Internal Dependencies
- **ACF configuration**: Pre-configured field groups for target post type
- **Content team training**: Users must attach files during task creation
- **WordPress admin access**: Configuration and testing permissions

## Demo Scenarios

### Scenario 1: PDF Processing
1. Create new Asana task with project brief PDF attached
2. Demonstrate automatic WordPress draft creation upon task save
3. Show extracted content in ACF fields
4. Highlight time savings vs. manual process

### Scenario 2: Word Document Processing
1. Create Asana task with project specification DOCX attached
2. Show successful text extraction and WordPress integration
3. Demonstrate task metadata (name, description) mapping

### Scenario 3: No Attachment Handling
1. Create Asana task without any attachments
2. Show graceful handling with appropriate logging
3. Demonstrate no WordPress post creation

### Scenario 4: Error Handling
1. Create task with unsupported file type (e.g., .txt, .png)
2. Show graceful error handling and logging
3. Demonstrate manual fallback process

## Success Definition

The MVP is considered successful when:
1. **Core workflow demonstrates** complete automation from Asana task creation to WordPress
2. **Stakeholders can visualize** the value of full-featured implementation
3. **Technical foundation proves** scalability for advanced features
4. **Internal team approves** progression to full development phase

## Next Steps Post-MVP

Upon successful MVP validation:
- **Delayed attachment processing** (support for attachments added after task creation)
- **Advanced document processing** (multi-file, images, structured content)
- **Real-time publishing workflows** with approval gates
- **Custom template assignment** and advanced ACF mapping
- **Production security and monitoring** implementation
- **User interface** for configuration and monitoring
- **Bulk processing** for existing tasks with attachments

---

**Change Log:**
- **v1.1** (September 5, 2025): Updated trigger mechanism from file attachment to task creation
- **v1.0** (September 5, 2025): Initial MVP requirements

**Document Approval:**
- [ ] Technical Lead Review
- [ ] Content Team Review  
- [ ] Project Stakeholder Approval