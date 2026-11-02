# JDC Mortuary System

A React and Vite portal for managing mortuary service requests, user accounts, notifications, and admin approvals.

## Accounts

New customer accounts can register immediately. A person registering through **Register as Admin** is stored as a pending request and cannot log in as an admin until the Head Admin approves it. Approved admins can manage customer bookings; only the Head Admin can manage admin registrations and approved admin accounts.

To create the initial Head Admin, set the `ADMIN_EMAIL` and `ADMIN_PASSWORD` environment variables in the Netlify project settings (optionally `ADMIN_NAME` and `ADMIN_PHONE`). The account is created automatically the first time the API runs and no Head Admin exists yet. Use a strong, unique password of at least 12 characters.

## Backend and data storage

The API runs as a Netlify Function (`netlify/functions/api.mts`) served at `/api/*`, and all data is stored in Netlify Database (managed Postgres):

- `users`: portal accounts and roles
- `bookings`: service requests, addresses, cause of death, body location city, cold-storage duration, statuses, and dates
- `notifications`: request updates for each user
- `admin_requests`: pending administrator registrations
- `sessions`: login sessions

The schema lives in `db/schema.ts`. After changing it, generate a migration with `npx drizzle-kit generate --name <change>`; Netlify applies migrations automatically on deploy.

The browser does not store account credentials, role information, or a copy of the records. Authentication uses an `HttpOnly` session cookie (marked `Secure` over HTTPS) backed by the `sessions` table. Passwords are hashed with Node.js `scrypt`. The API authenticates users, checks roles on protected operations, limits customers to their own bookings and notifications, and validates submitted booking fields.

## Run locally

```bash
npm install
netlify dev
```

`netlify dev` serves the frontend together with the API function and a local database.

## Build

```bash
npm run build
```

This template provides a minimal setup to get React working in Vite with HMR and some Oxlint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react) uses [Oxc](https://oxc.rs)
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react-swc) uses [SWC](https://swc.rs/)

## React Compiler

The React Compiler is not enabled on this template because of its impact on dev & build performances. To add it, see [this documentation](https://react.dev/learn/react-compiler/installation).

## Expanding the Oxlint configuration

If you are developing a production application, we recommend using TypeScript with type-aware lint rules enabled. Check out the [TS template](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts) for information on how to integrate TypeScript and Oxlint's TypeScript related rules in your project.
