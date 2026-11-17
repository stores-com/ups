# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Node.js library for the UPS API that provides OAuth authentication and package tracking functionality. It uses native `fetch` and the Node.js test runner.

## Development Commands

**Testing:**
- `npm test` - Run all tests using Node.js native test runner
- `node --test --test-force-exit test/index.js` - Run specific test file
- Tests are grouped as top-level `test()` calls with `{ concurrency: true }` and `t.test()` subtests, using async/await

**Key Environment Variables:**
- `CLIENT_ID` - UPS API client ID (required for tests)
- `CLIENT_SECRET` - UPS API client secret (required for tests)

## Architecture

The library exports a single constructor function `Ups` that creates instances with two main methods:

- `getAccessToken()` - OAuth 2.0 authentication with UPS API, includes automatic caching using memory-cache
- `getTracking(inquiryNumber, options)` - Track packages using UPS Tracking API

**Authentication Flow:**
1. OAuth uses Basic auth with client_id:client_secret encoded in Authorization header
2. Access tokens are cached in memory with half the token's lifetime as TTL
3. All API calls use Bearer token authentication

**Environment URLs:**
- Test: `https://wwwcie.ups.com` (default)
- Production: `https://onlinetools.ups.com`

**API Implementation Details:**
- Uses native `fetch`
- OAuth endpoint: `/security/v1/oauth/token`
- Tracking endpoint: `/api/track/v1/details/{inquiryNumber}`
- Tracking requires `transId` (UUID) and `transactionSrc` headers

**Test Data:**
- Test tracking numbers: `1Z5338FF0107231059`, `1Z12345E0205271688`
- UPS test environment returns consistent mock responses regardless of tracking number used

**Memory Management:**
- Tests have no cache hooks; `npm test` passes `--test-force-exit` so the runner exits