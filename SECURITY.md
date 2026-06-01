# Security

## Public deployment
This repository is published publicly via **GitHub Pages**. All files in it — including `index.html` — are world-readable. Treat anything committed here as public.

## Supabase anon key is public by design
The Supabase **anon / publishable** key embedded in `index.html` is meant to be exposed to browsers. It is **not** a secret. It only identifies the project and grants the `anon` role; it does not bypass authorization.

Security therefore relies entirely on **Row Level Security (RLS)** policies enforced server-side by Supabase. Any table reachable by the `anon` role must have RLS enabled with policies that expose only what is intended for the public client.

> Never commit the Supabase `service_role` key (or any other secret) to this repo.

## Content-Security-Policy
`index.html` ships a `Content-Security-Policy` `<meta>` tag that whitelists only the origins the app actually uses (jsDelivr for supabase-js, Google Fonts, and the Supabase REST + realtime endpoints). `'unsafe-inline'` is intentionally kept for `script-src`/`style-src` because the app is a single inline-heavy page.

## Remaining hardening checklist

### Done (DB-side, applied via Supabase)
- Enabled RLS on `inventory`, `order_drafts`, and `supplier_audit_log`.
- Removed anonymous `SELECT` / `UPDATE` / `DELETE` on lead PII tables (public `INSERT` kept so web forms still submit).

### TODO (manual — Supabase dashboard)
- Enable **Leaked Password Protection** in Auth settings.
- Review the **13 `SECURITY DEFINER` views** and **2 anon-executable functions** for unintended privilege escalation / data exposure.
- Restrict object listing on the public storage buckets `app`, `pages`, and `public-pages`.
- Migrate remaining apps off the `anon` role to real **Supabase Auth** (`authenticated`).
