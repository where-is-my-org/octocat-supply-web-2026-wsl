---
mode: 'agent'
description: 'Update frontend and backend configurations to allow communication between the frontend application and the backend API on user defined ports.'
tools: ['edit/editFiles', 'search', 'runCommands', 'runTasks', 'usages', 'problems', 'changes', 'testFailure', 'openSimpleBrowser', 'fetch', 'githubRepo']
---

# Port Configuration Changes

## Context
You are working with the OctoCAT Supply Chain Management System - a modern TypeScript web application with separate API and Frontend (React) projects.
There are multiple users that are running the application on the same environment and need to configure different ports. The frontend application is running on port 5137 and the backend API is running on port 3000. The user wants to update the port configuration to allow the frontend application to communicate with the backend API.

## Current State
- The backend API is running on port 3000
- The frontend application is running on port 5137
- The frontend needs to communicate with the backend API for data fetching and other operations
- There are CORS configuration to prevent the frontend from accessing the backend API

## File that Need Changes (Do not modify any other files)
Update port configuration based on user input including:
1. Backend API Port Changes
    - Changed API server port from 3000 to <user-input-backendport> in:
        - `/api/main.py`: Updated the port in the uvicorn run command
        - `package.json`: Updated the `start:api` script to use the new backend port

2. Frontend Configuration
    - Updated Vite server port from 5137 to <user-input-frontendport> in:
        - `/frontend/vite.config.ts`: Updated the port in the server configuration

3. API Connection Configuration
    - Updated API connection references in:
        - `/frontend/src/api/config.ts`: Default localhost API URL now points to port <user-input-backendport>
        - `/frontend/entrypoint.sh`: Updated `EFFECTIVE_API_PORT` to <user-input-backendport>
        - `/api/app/main.py`: Updated CORS configuration to allow connections from the new frontend port

### Frontend Architecture and Building
- Refer to the existing Architecture Doc (../docs/architecture.md) for frontend structure
- Refer to the Building Doc (../docs/building.md) for build instructions

## Success Criteria
After implementation, users should be able to:
1. Run the frontend application on the specified port
2. Run the backend API on the specified port
3. The application should function correctly with the new port configurations

## Implementation Instructions
1. Ask user for the new port numbers for the backend API and frontend application
    - Could you please provide:
        - The new port number for the backend API (currently <currently port>)
        - The new port number for the frontend application (currently <currently port>)
2. Analyze the existing codebase structure
3. Update new port configurations in the specified files:
   - `/api/main.py`
   - `package.json`
   - `/frontend/vite.config.ts`
   - `/frontend/src/api/config.ts`
   - `/frontend/entrypoint.sh`
   - `/api/app/main.py`

4. Test the application to ensure it works correctly with the new port settings

## Notes
- Besides the port changes, do not modify any other functionality or code in the application
- Ensure that the application is still functional after the port changes

Begin implementation by analyzing the current codebase structure and then proceed with the cart functionality development.