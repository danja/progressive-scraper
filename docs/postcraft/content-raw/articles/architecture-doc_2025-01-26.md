# Progressive Web Scraper Architecture

## Overview
A pipeline-based web scraper that processes URLs through increasingly complex stages, only advancing to more resource-intensive methods when necessary. Uses loglevel for consistent logging across all components.

## Logging Strategy
- Structured JSON logging format
- Log levels: ERROR, WARN, INFO, DEBUG, TRACE
- Component-specific logging namespaces
- Request ID tracking across pipeline
- Performance timing logs
- Error stack traces with context
- Rate limit and compliance logging
- Resource usage logging
A pipeline-based web scraper that processes URLs through increasingly complex stages, only advancing to more resource-intensive methods when necessary.

## Pipeline Stages

### 1. URL Validator
- Validates and normalizes input URLs
- Checks robots.txt compliance
- Output: Validated URL with metadata

### 2. Status Checker
- Performs HEAD request
- Checks content type, status
- Handles redirects
- Output: Status info, final URL

### 3. Simple HTML Extractor
- Attempts basic HTTP GET
- Extracts raw HTML content
- Output: Raw HTML if successful

### 4. Metadata Extractor
- Extracts meta tags, OpenGraph data
- Analyzes document structure
- Identifies content patterns
- Output: Enhanced metadata object

### 5. Dynamic Content Detector
- Analyzes page for JavaScript requirements
- Checks for anti-bot measures
- Routes to appropriate extractor
- Output: Processing route decision

### 6. Content Extractors
a. Simple Content Extractor
   - Processes static HTML
   - Extracts structured content
   - Output: Normalized content object

b. Headless Browser Extractor
   - Manages browser instances
   - Handles JavaScript execution
   - Extracts dynamic content
   - Output: Normalized content object

## Message Format
```javascript
{
  url: string,
  status: {
    code: number,
    contentType: string,
    lastModified: string
  },
  content: {
    raw: string,
    structured: object
  },
  metadata: {
    dynamic: boolean,
    requiresJS: boolean,
    extractionMethod: string
  }
}
```

## Error Handling
- Each component handles specific error types
- Error information added to message metadata
- Pipeline can be configured to retry or abort

## Rate Limiting & Compliance
- Domain-specific rate limiters
- Robots.txt parser and cache
- Politeness delays with jitter
- Per-domain concurrent request limits
- Shared rate limit state for horizontal scaling

## Metrics Collection
- Success/failure rates per stage
- Processing time per component
- Resource usage tracking
- Stage transition statistics
- Domain-specific performance metrics

## Error Recovery
- Retry policies per error type
- Circuit breaker implementation
- Partial result handling
- State preservation for resumption
- Error classification system

## Scaling Strategy
- Redis-based rate limit sharing
- Pipeline stage replication
- Domain-based request routing
- Shared state management
- Load balancer configuration