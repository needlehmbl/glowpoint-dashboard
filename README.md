# Glow Point — Admin Dashboard

The **admin dashboard** for **Glow Point**, an online appointment booking system
for a beauty and wellness business. It is the operator-facing counterpart to the
customer booking app and is responsible for **all appointment data and CRUD
operations**, plus the business analytics used to run the shop.

Built with **Next.js 15 (App Router)**, **TypeScript**, **Supabase**, and
**shadcn/ui**, it lets staff log in securely, manage appointments, payments,
services, and users, view the calendar and live queue, and track business
performance.

> Customer-facing booking app:
> [`glowpoint-client`](https://github.com/needlehmbl/glowpoint-client).

## Features

### Authentication & security
- Email/password login through Supabase Auth.
- **Two-factor authentication (TOTP)** — users with MFA enabled are redirected to
  a verification step after login.
- Forgot-password / update-password flows and auth callback routes.
- Role-aware access (`isAdmin` on the `Profiles` table) and a protected
  `(app)` route group that redirects unauthenticated users to `/login`.
- Session refresh via Supabase middleware.
- Sensitive fields are **AES-encrypted at rest** (`crypto-js`) using a server-side
  `ENCRYPTION_KEY`.

### Dashboard home (`/home`)
- KPI charts: bar, pie, radar, and horizontal bar (`recharts`).
- **Date-range filter** and **top-services filter**.
- Recent activity and recent feedback panels.
- **Appointment verification** widget and a live **queue widget**.
- Excel export of report data.

### Calendar (`/calendar`)
- Calendar view of appointments for scheduling and day-to-day operations.

### Payments (`/payments`)
- Payments data table with sorting, filtering, and pagination
  (`@tanstack/react-table`).
- Payment detail popover, status verification, and per-appointment details.
- Transactional email delivery through a server route that calls the **Brevo**
  API, plus a Supabase Edge Function (`send-reminders`) for reminders.

### Services (`/services`)
- Create, edit, and delete **service categories** and **services**.
- These records drive the catalog shown in the customer booking app.

### Users (`/users`)
- Create, edit, and deactivate user accounts.
- User activity popovers and a per-user detail page (`/users/[username]`).

## Tech stack

| Layer       | Technology                                                             |
| ----------- | --------------------------------------------------------------------- |
| Framework   | Next.js 15 (App Router, Turbopack) + React 19                         |
| Language    | TypeScript                                                            |
| Backend     | Supabase (Postgres, Auth, Realtime) via `@supabase/ssr`               |
| UI          | shadcn/ui + Radix UI primitives, Tailwind CSS 4, lucide-react         |
| Tables      | TanStack Table                                                        |
| Charts      | Recharts                                                              |
| Forms       | React Hook Form + Zod                                                 |
| Email       | Brevo (`BREVO_API_KEY`) + Supabase Edge Function for reminders        |
| Utilities   | crypto-js (AES), date-fns, xlsx export, sonner (toasts), next-themes  |

## Project structure

```
src/
  app/
    (app)/            Authenticated area (redirects to /login if signed out)
      home/           Analytics dashboard
      calendar/       Appointment calendar
      payments/       Payments table + detail views
      services/       Service & category management
      users/          User management (+ [username] detail)
    (auth)/           Login, verify-2fa, forgot/update password
    api/              Server routes (transactional email)
    auth/             Supabase auth callbacks (incl. password reset)
  components/         Feature components + shadcn/ui primitives (ui/)
  actions.ts          Server actions (auth, CRUD, users, queue, etc.)
  hooks/              use-admin, useAdminQueue, use-mobile, useMediaQuery
  utils/              supabase clients/middleware, encryption, excel export, queue
supabase/
  config.toml         Local Supabase config
  functions/
    send-reminders/   Edge Function that sends appointment reminders
```

## Deployment

1. Set the required environment variables (`NEXT_PUBLIC_SUPABASE_URL`,
   `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `ENCRYPTION_KEY`, `BREVO_API_KEY`) on
   your host.
2. Run `npm run build`.
3. Start with `npm run start` (the `PORT` env var is honoured), or deploy the
   `standalone` output.
