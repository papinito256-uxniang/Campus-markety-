# Campus-markety-
Projet de marché du campus 
campusmarket/
├── .env
├── package.json
├── index.html
├── vite.config.js
├── netlify.toml
├── src/
│   ├── main.jsx
│   ├── supabase.js
│   └── style.css
└── supabase/
    └── schema.sql
    {
  "name": "campusmarket",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "@supabase/supabase-js": "^2.57.0",
    "@vitejs/plugin-react": "latest",
    "@tailwindcss/vite": "latest",
    "vite": "latest",
    "tailwindcss": "latest",
    "react": "latest",
    "react-dom": "latest",
    "lucide-react": "latest"
  }
}import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  plugins: [react(), tailwindcss()]
});
<!doctype html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>CampusMarket</title>

  <link rel="preconnect" href="https://fonts.googleapis.com">

  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=Outfit:wght@600;700;800&display=swap" rel="stylesheet">
</head>
<body>
  <div id="root"></div>
  <script type="module" src="/src/main.jsx"></script>
</body>
</html>
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
  VITE_SUPABASE_URL=https://yoenigdfaujokbskmyvi.supabase.co
VITE_SUPABASE_ANON_KEY=COLLE_TA_CLE_PUBLISHABLE_ICI
import { createClient } from '@supabase/supabase-js';

const url = import.meta.env.VITE_SUPABASE_URL;
const anonKey = import.meta.env.VITE_SUPABASE_ANON_KEY;

export const supabaseConfigured = Boolean(url && anonKey);

export const supabase = supabaseConfigured
  ? createClient(url, anonKey, {
      auth: {
        persistSession: true,
        autoRefreshToken: true,
        detectSessionInUrl: true
      }
    })
  : null;
  @import "tailwindcss";

:root {
  font-family: Inter, sans-serif;
  color: #241b31;
  background: #faf9fc;
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
}

h1, h2, h3, .brand {
  font-family: Outfit, sans-serif;
}

button, input, select, textarea {
  font: inherit;
}

button {
  cursor: pointer;
}

.container {
  max-width: 1180px;
  margin: auto;
  padding: 0 22px;
}

.card {
  background: #fff;
  border: 1px solid #eee9f4;
  border-radius: 18px;
  box-shadow: 0 5px 20px #28104a08;
}

.btn {
  border: 0;
  border-radius: 12px;
  padding: 11px 16px;
  font-weight: 700;
}

.primary {
  background: #6d28d9;
  color: white;
}

.soft {
  background: #f2eafe;
  color: #6525c7;
}

.field {
  width: 100%;
  border: 1px solid #e5deef;
  border-radius: 11px;
  padding: 12px;
  background: white;
  outline: none;
}

.pill {
  font-size: 11px;
  font-weight: 800;
  padding: 5px 9px;
  border-radius: 99px;
  background: #f1eafe;
  color: #6525c7;
}

.product-img {
  height: 175px;
  width: 100%;
  object-fit: cover;
  background: #f3eff8;
}

.navitem {
  padding: 10px 12px;
  border-radius: 12px;
  color: #766b83;
  font-weight: 600;
}

.navitem.active,
.navitem:hover {
  background: #f2eafe;
  color: #6525c7;
}

@media(max-width:700px) {
  .container {
    padding: 0 14px;
  }

  .product-img {
    height: 145px;
  }
}

input[type=file]::file-selector-button {
  border: 0;
  border-radius: 8px;
  padding: 7px 10px;
  background: #f2eafe;
  color: #6525c7;
  font-weight: 700;
  margin-right: 8px;
}

video {
  max-width: 100%;
}
create extension if not exists pgcrypto;

create table if not exists public.listings (
  id uuid primary key default gen_random_uuid(),
  owner_id uuid not null references auth.users(id) on delete cascade,
  title text not null,
  price numeric(12,2) not null check (price >= 0),
  category text not null,
  subcategory text,
  condition text not null,
  listing_type text not null default 'Vente',
  negotiable boolean not null default false,
  city text not null,
  views integer not null default 0,
  seller_name text,
  faculty text,
  image_url text,
  media jsonb not null default '[]'::jsonb,
  description text not null,
  brand text,
  model text,
  color text,
  quantity integer not null default 1 check (quantity > 0),
  characteristics text,
  created_at timestamptz not null default now()
);

create index if not exists listings_created_at_idx
on public.listings (created_at desc);

create index if not exists listings_category_idx
on public.listings (category);

create index if not exists listings_owner_idx
on public.listings (owner_id);

alter table public.listings enable row level security;

drop policy if exists "Listings are public to read"
on public.listings;

create policy "Listings are public to read"
on public.listings
for select
using (true);

drop policy if exists "Signed-in users can create their own listings"
on public.listings;

create policy "Signed-in users can create their own listings"
on public.listings
for insert to authenticated
with check (auth.uid() = owner_id);

drop policy if exists "Owners can update their listings"
on public.listings;

create policy "Owners can update their listings"
on public.listings
for update to authenticated
using (auth.uid() = owner_id)
with check (auth.uid() = owner_id);

drop policy if exists "Owners can delete their listings"
on public.listings;

create policy "Owners can delete their listings"
on public.listings
for delete to authenticated
using (auth.uid() = owner_id);

insert into storage.buckets (
  id,
  name,
  public,
  file_size_limit,
  allowed_mime_types
)
values (
  'listing-media',
  'listing-media',
  true,
  52428800,
  array[
    'image/jpeg',
    'image/png',
    'image/webp',
    'image/gif',
    'video/mp4',
    'video/webm',
    'video/quicktime'
  ]
)
on conflict (id)
do update set
  public = true,
  file_size_limit = 52428800,
  allowed_mime_types = excluded.allowed_mime_types;

drop policy if exists "Public can view listing media"
on storage.objects;

create policy "Public can view listing media"
on storage.objects
for select
using (bucket_id = 'listing-media');

drop policy if exists "Users upload media to their own folder"
on storage.objects;

create policy "Users upload media to their own folder"
on storage.objects
for insert to authenticated
with check (
  bucket_id = 'listing-media'
  and (storage.foldername(name))[1] = auth.uid()::text
);

drop policy if exists "Users delete media from their own folder"
on storage.objects;

create policy "Users delete media from their own folder"
on storage.objects
for delete to authenticated
using (
  bucket_id = 'listing-media'
  and (storage.foldername(name))[1] = auth.uid()::text
);
