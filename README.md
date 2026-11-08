# Soft-A4-Aldamuratov
Smart Campus Room Availability Checker
A small front-end prototype that checks whether a campus room is free for a chosen time interval. Users can filter a catalog of fictional rooms by capacity and equipment, select a room, and run an availability check against that room's own bookings.
Built for Software Engineering, Assignment #04 (AI-assisted evolution, refactoring and quality improvement).
Scope: fictional data only. No backend, no login, no payments, no live APIs, no database. Checking availability never creates or changes a booking.
Tech stack
React 18 + TypeScript (strict mode)
Vite 5
Tailwind CSS 3, lucide-react icons
Vitest 2 for automated tests
ESLint 9 (typescript-eslint)
Prerequisites
Node.js 18 or newer (20 LTS recommended)
npm (comes with Node.js)
Check your versions:
node -v
npm -v
Setup
# 1. Go to the project folder (the one that contains package.json)
cd project

# 2. Install dependencies exactly as locked in package-lock.json
npm ci
If npm ci is not available for some reason, npm install works too.
Run
# Start the development server (hot reload)
npm run dev
Open the URL printed in the terminal (usually http://localhost:5173).
Other run commands:
# Production build into dist/
npm run build

# Serve the production build locally
npm run preview
Test
# Run all automated tests once
npm run test
Expected result for the final version: 3 test files, 44 tests, all passed.
Test file	What it covers	Tests
src/validation.test.ts	AC1–AC5 availability rules and boundaries (exactly 120 vs 121 minutes, touching vs overlapping intervals, read-only data)	26
src/roomFilters.test.ts	F1–F5 filtering and room selection (exact vs insufficient capacity, equipment, empty state)	15
src/data.test.ts	Room catalog fixtures, independent bookings per room, immutable data	3
Useful variations:
# Show every test name with its result
npx vitest run --reporter=verbose

# Run one file
npx vitest run src/validation.test.ts

# Watch mode while developing
npx vitest
Other quality checks
# Type-check the app (no output files)
npm run typecheck

# Lint the project
npm run lint
Features
Availability rules (original behavior, preserved)
ID	Rule
AC1	Valid interval: 0 ≤ start < end ≤ 1440 minutes, and start must be after the current time
AC2	Maximum booking duration is 120 minutes (120 passes, 121 fails)
AC3	A blocked room is rejected
AC4	No overlap with the same room's bookings; touching endpoints are allowed
AC5	The check is read-only: booking and room data are never mutated
AC6	Clear, responsive feedback for every result
Room catalog and filtering (added in Assignment #04)
ID	Rule
F1	Capacity filter: room capacity ≥ requested attendees
F2	Equipment filter: the room must contain all selected required items
F3	A blocked room can never be reported as available
F4	A clear empty state is shown when no rooms match
F5	Changing the selected room refreshes the availability result and schedule
Each of the five fictional rooms has an ID, name, building, capacity, equipment list, blocked status and its own bookings.
Project structure
project/
├── AGENTS.md              Rules and constraints given to the AI assistant
├── package.json           Scripts: dev, build, preview, test, typecheck, lint
├── vite.config.ts         React plugin and "@" -> src/ alias
├── tsconfig.app.json      Strict TypeScript config
└── src/
    ├── main.tsx           App entry point
    ├── App.tsx            Layout and state coordination
    ├── components/
    │   ├── RoomFilterPanel.tsx   Capacity and equipment filter UI
    │   └── TimeSelect.tsx        Reusable start/end time dropdown
    ├── constants.ts       Shared limits and labels (120 min, 1440 min)
    ├── types.ts           Room, Booking, Decision and related types
    ├── data.ts            Fictional rooms and per-room bookings
    ├── validation.ts      Pure AC1–AC5 availability checks
    ├── roomFilters.ts     Pure F1–F4 filtering and room selection logic
    ├── validation.test.ts
    ├── roomFilters.test.ts
    └── data.test.ts
Engineering notes
Validation and filtering are pure functions: they take plain inputs and return new values, so they are tested without rendering the UI.
Limits (120 minutes, 1440 minutes) live only in constants.ts.
The selected room is derived from the filtered list on each render, so it can never point to a room that is no longer shown.
Room and booking fixtures are never mutated; tests check this explicitly.
Known limitations
Front-end only: data is static in-memory fixtures, so nothing persists after a page refresh.
No component-level UI tests yet; rendering and state behavior were checked manually with npm run dev.
Troubleshooting
npm ci fails: make sure you run it in the folder that contains package.json and package-lock.json, and that Node.js is 18 or newer.
Port already in use: Vite picks the next free port automatically; use the URL it prints.
Tests behave differently around midnight: the availability check compares against the current time of day, so tests pass a fixed "current time" instead of reading the clock.
