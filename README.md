# Adama Tova – Activity Registration & Management Platform

A mobile-first web app (PWA) for **Adama Tova**, a community space, that lets members sign up for workshops and activities and gives staff one place to manage activities, capacity, waitlists and member approvals.

Built in a team of 3 during the **Product Development Jam** (Hebrew University × Bezalel Academy, 2025).

**Live demo:** https://adama-tova.vercel.app

<img width="3840" height="2160" alt="image" src="https://github.com/user-attachments/assets/51051972-2398-43ec-98b6-5122fb21c694" />

<img width="3840" height="2160" alt="image" src="https://github.com/user-attachments/assets/f9697565-bee3-4f91-9f5d-2d15d495ea05" />


---

## Features

**Members**
- Sign up with email/password or Google, through a multi-step onboarding wizard
- Browse upcoming activities, register in one tap and cancel when needed
- Join a **waitlist** when an activity is full and get **promoted automatically** when a spot opens
- Personal calendar, notifications and profile
- Hebrew UI that adapts its wording to the user's gender (via [Ivrita](https://github.com/AlefAlefAlef/ivrita))

**Admins**
- Approve or reject new members before they can register
- Create, edit and cancel activities, including **recurring weekly series** that can be edited as a group
- Set capacity limits and choose whether registration needs approval
- Dashboard of pending approvals and upcoming registrations
- Send notifications to members

**Automated emails** are sent for account approval, registration approval and waitlist promotion.

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 14 (App Router), React 18, TypeScript, CSS Modules |
| Backend | Supabase (PostgreSQL, Auth, Storage), Next.js API routes |
| Auth | Supabase Auth: email/password + Google OAuth |
| Email | Nodemailer (SMTP) |
| Hosting | Vercel |

## Data Model

Main tables in Supabase (PostgreSQL):

- **users**: profile, role (`participant` / `admin`), approval status, onboarding answers
- **activities**: title, location, guide, date and time, `max_participants`, `requires_approval`, `series_id` for recurring groups
- **registrations**: links users to activities, with `if_confirmed` status and `wait_list_place` for waitlist order
- **notifications**: messages from admins to members

## Interesting Problems

- **Capacity and waitlist logic.** Confirmed registrations and waitlist positions are counted live per activity. When an admin raises capacity, users on the waitlist are promoted in order across every activity in the series and get an email.
- **Recurring activities.** A weekly group is created as several activities that share a `series_id`, so one edit updates the whole series.
- **Two approval flows.** New members need admin approval, and some activities also need per-registration approval. Each flow has its own status and email.
- **Image uploads.** Activity images are compressed in the browser before upload to keep storage and load times low.

## My Role

I was responsible mainly for the **backend and data layer**:
- Designed the PostgreSQL schema (users, roles, activities, registrations, waitlists)
- Wrote the data-access layer (`app/services/db_api.js`): registration, cancellation, capacity counting, waitlist promotion and series updates
- Connected React screens to the backend and worked on parts of the UI

## Running Locally

```bash
git clone https://github.com/Yonatan-Gan/adama-tova.git
cd adama-tova
npm install
cp .env.local.template .env.local   # fill in Supabase, Google and SMTP values
npm run dev                         # http://localhost:3000
```

Required environment variables:

| Variable | Purpose |
|---|---|
| `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase project |
| `NEXT_PUBLIC_GOOGLE_CLIENT_ID` | Google sign-in |
| `EMAIL_ADDRESS`, `EMAIL_PASSWORD`, `EMAIL_HOST`, `EMAIL_PORT` | SMTP for notification emails |

## Project Structure

```
app/
├── AdminScreens/      # admin home, calendar, add/edit activity, notifications, admin management
├── UserScreens/       # member home, calendar, notifications
├── ApplicationForm/   # onboarding wizard
├── login/             # login, Google sign-in, about
├── api/               # email routes (approval, registration, waitlist promotion)
├── services/          # Supabase data access, auth and user services
└── contexts/          # user session, gender-aware text, page transitions
```

## Development Workflow

The team worked with feature branches merged into a `dev` integration branch, with `main` kept for production deploys. See [WORKFLOW.md](WORKFLOW.md).
