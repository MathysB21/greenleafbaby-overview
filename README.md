# GreenLeafBaby

An e-commerce platform for a specialty retailer: catalogue, cart, checkout, order management and an admin area, with a pre-launch waitlist mode.

## My role

Sole developer on a client contract. I handle the build, QA, testing and project management.

## Stack

Next.js 16 (App Router, Server Components, Server Actions), React 19, TypeScript in strict mode, Tailwind CSS 4, shadcn/ui, zod, Supabase (Postgres, Auth, Storage), Nodemailer, Vitest, GitHub Actions, Vercel.

## Architecture

One Next.js app. Server Components and Server Actions do all data access, and the browser never talks to the database. Every table has row-level security on with no policies, so only the server can read or write. Orders are written only through two transactional Postgres functions, which enforce the order lifecycle, re-check each line price against the catalogue, compute totals and append order events. The admin area covers catalogue editing, photo uploads, an explicit publish step for catalogue changes and order status updates, and every staff action goes to an append-only audit log.

## Hard problems

**Never charge a total the customer didn't see.** I rejected computing cart totals in the browser, since the browser can't be trusted with prices. The browser sends only product ids and quantities. The server quotes the cart, prices it again on submit, and if anything changed it shows the new total instead of placing the order. Checkout attempts are idempotent, so a retry can't create a second order or send a second confirmation email.

**Order rules that can't drift.** I rejected letting the app insert orders directly, because then the rules are only conventions in application code. Orders go through the database functions, which hold the lifecycle. The same lifecycle exists in TypeScript for the UI, and a test parses the SQL migrations and fails if the two disagree.

## Status

In development. The pre-launch site and waitlist are live. Checkout is built and sits behind a flag that is off by default, so launch is a config change rather than a new build.

Source is private. Full write-up: TODO (case study not written yet)
