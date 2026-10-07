# Caldera Endurance on Vercel

This edition runs Next.js on Vercel with Neon PostgreSQL. It does not require the original Sites/Cloudflare runtime.

## Deployment

Project: caldera-endurance in the Caldera Endurance Vercel workspace.

Install dependencies with pnpm install, configure DATABASE_URL, AUTH_MODE=password, ADMIN_EMAILS, ADMIN_PASSWORD_HASH and ADMIN_SESSION_SECRET, then run pnpm db:migrate and pnpm build. Deploy with vercel --prod from this directory. The current credentials are managed as Vercel environment variables and are excluded from the source package.

The admin password uses a salted PBKDF2 hash. Sessions use signed, secure, HTTP-only cookies. The authorized admin email is vicycledph@gmail.com.

## Email provider: Resend

The app is wired for Resend as the production email provider. Confirmation emails, runner updates, and email blasts call Resend from server-side API routes only. Configure these Vercel environment variables in Production, Preview, and Development as needed:

- `EMAIL_PROVIDER=resend`
- `RESEND_API_KEY` — the server-side Resend API key; keep this secret.
- `EMAIL_FROM` — a verified sender, for example `Caldera Endurance <noreply@yourdomain.com>`. For initial testing you can use Resend's onboarding sender, but blasts should use a verified domain.
- `NEXT_PUBLIC_SITE_URL` — the public site URL used in confirmation-email status links.

Recommended setup on Vercel is the Resend Marketplace integration, or add `RESEND_API_KEY` manually in Project Settings → Environment Variables. In Resend, verify the sending domain and add the DNS records before sending live runner emails.

## Storage and migration

Events, registrations, audit records and rate limits are in Neon. Payment proof images are private database records, accessed only through the authenticated admin endpoint. Uploads are PNG/JPEG, limited to 4 MB to stay within Vercel request limits. All races remain test events; there is no real QRPH settlement integration.

The original published event and registration were copied to Neon. The old Sites website and the Vercel website are independent after the copy: new registrations do not synchronize. Use the Vercel website as the main registration link after launch.

## Verification

The Next.js production build and TypeScript checks passed. End-to-end checks covered protected admin APIs, event draft creation, publishing, registration, capacity enforcement, private proof upload and retrieval, runner verification, and registration lookup. Temporary QA records were removed after the checks.
