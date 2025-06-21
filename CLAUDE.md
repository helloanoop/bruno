
This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Bruno is an open-source IDE for exploring and testing APIs, built as an Electron desktop application. It stores API collections directly in the filesystem using a plain text markup language (Bru), enabling Git-based collaboration without cloud sync.

## Development Commands

### Initial Setup
```bash
# Use Node.js v22.x or latest LTS
nvm use

# Install dependencies
npm i --legacy-peer-deps

# Quick setup (builds all packages and bundles JS sandbox)
npm run setup
```

### Development
```bash
# Run both web and electron apps concurrently
npm run dev

# Or run separately:
npm run dev:web      # Terminal 1: React app on http://localhost:3000
npm run dev:electron # Terminal 2: Electron app

# Run with custom electron userData path
ELECTRON_APP_NAME=bruno-dev npm run dev:electron
```

### Building
```bash
# Build individual packages (required before running)
npm run build:graphql-docs
npm run build:bruno-query
npm run build:bruno-common
npm run build:bruno-converters
npm run build:bruno-requests

# Bundle JS sandbox libraries
npm run sandbox:bundle-libraries --workspace=packages/bruno-js

# Build electron app
npm run build:electron
npm run build:electron:mac
npm run build:electron:win
npm run build:electron:linux
```

### Testing
```bash
# Run all tests across workspaces
npm test --workspaces --if-present

# Run tests for specific packages
npm run test --workspace=packages/bruno-schema
npm run test --workspace=packages/bruno-query
npm run test --workspace=packages/bruno-common
npm run test --workspace=packages/bruno-converters
npm run test --workspace=packages/bruno-app
npm run test --workspace=packages/bruno-electron
npm run test --workspace=packages/bruno-lang
npm run test --workspace=packages/bruno-toml

# Run e2e tests with Playwright
npm run test:e2e

# Run linter
npm run lint
```

## Architecture

### Monorepo Structure
Bruno uses npm workspaces with the following packages:

- **bruno-app**: React-based frontend UI (Redux, Tailwind, CodeMirror)
- **bruno-electron**: Electron desktop wrapper, handles file system and native OS features
- **bruno-cli**: Standalone CLI for running API tests in CI/CD
- **bruno-lang**: Parser for .bru file format using Ohm.js
- **bruno-schema**: Data validation schemas using Yup
- **bruno-common**: Shared utilities across packages
- **bruno-js**: JavaScript runtime for pre/post request scripts (VM2 sandbox)
- **bruno-query**: Query utilities
- **bruno-requests**: Request handling utilities (auth, cookies)
- **bruno-converters**: Format converters (Postman, Insomnia, OpenAPI)
- **bruno-graphql-docs**: GraphQL schema documentation viewer

### Key Technologies
- **Frontend**: React, Redux, Tailwind CSS, CodeMirror, Formik, i18next
- **Backend**: Electron, axios, chokidar
- **Build**: Rsbuild (for React app)
- **Testing**: Jest, Playwright
- **Language Parsing**: Ohm.js

### Data Flow
1. Collections are stored as .bru files in the filesystem
2. bruno-lang parses .bru files to/from JSON
3. Electron main process handles file operations and API requests
4. React app communicates with Electron via IPC
5. Pre/post request scripts run in sandboxed VM2 environment

## Development Guidelines

### Branch Naming
- `feature/[feature-name]`: New features (e.g., feature/dark-mode)
- `bugfix/[bug-name]`: Bug fixes (e.g., bugfix/bug-1)

### Pull Requests
- Keep PRs small and focused on one thing
- Write tests for new functionality
- Ensure all tests pass before submitting

### File Format
Bruno uses its own .bru format for API requests. The bruno-lang package handles parsing and conversion. When working with collection files, use the existing parser rather than manual string manipulation.

### Security Considerations
- Scripts run in sandboxed VM2 environment
- No cloud sync - all data stays local
- File system access is controlled through Electron main process