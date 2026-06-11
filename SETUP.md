# FieldOps – Supabase Setup

## Step 1: Run this SQL in Supabase

Go to your Supabase project → **SQL Editor** → paste and run this entire block:

```sql
-- Work Orders table
create table if not exists work_orders (
  id uuid default gen_random_uuid() primary key,
  wo_number text,
  client_name text,
  client_address text,
  client_phone text,
  client_email text,
  location text,
  customer_po text,
  project text,
  work_type text,
  description text,
  priority text default 'Normal',
  status text default 'Open',
  assigned_techs text[] default '{}',
  scheduled_date text,
  line_items jsonb default '[]',
  time_logs jsonb default '[]',
  notes jsonb default '[]',
  invoice_notes text,
  created_by uuid references auth.users(id),
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

-- Settings table
create table if not exists settings (
  id integer primary key default 1,
  company_name text default 'Desert Link Technologies LLC',
  company_address text default '11733 W. Chase Ln, Avondale, AZ 85323',
  company_phone text default '',
  company_email text default '',
  invoice_seq integer default 21
);

-- Insert default settings
insert into settings (id) values (1) on conflict (id) do nothing;

-- Profiles table (stores role per user)
create table if not exists profiles (
  id uuid references auth.users(id) primary key,
  email text,
  role text default 'tech',  -- 'admin' or 'tech'
  name text
);

-- Auto-create profile on signup
create or replace function handle_new_user()
returns trigger as $$
begin
  insert into public.profiles (id, email, role)
  values (new.id, new.email, 'tech')
  on conflict (id) do nothing;
  return new;
end;
$$ language plpgsql security definer;

drop trigger if exists on_auth_user_created on auth.users;
create trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure handle_new_user();

-- Row Level Security
alter table work_orders enable row level security;
alter table settings enable row level security;
alter table profiles enable row level security;

-- Policies: authenticated users can read/write everything
create policy "Allow all for authenticated" on work_orders
  for all using (auth.role() = 'authenticated');

create policy "Allow all for authenticated" on settings
  for all using (auth.role() = 'authenticated');

create policy "Allow all for authenticated" on profiles
  for all using (auth.role() = 'authenticated');

-- Real-time: enable for work_orders
alter publication supabase_realtime add table work_orders;
```

---

## Step 2: Make yourself admin

After running the SQL, sign up in the app once. Then go to Supabase → **Table Editor → profiles** → find your email → change `role` from `tech` to `admin`. Save.

Sign out and back in — you'll now have full admin access.

---

## Step 3: Add your team

Have each tech go to the same URL and sign up with their email. Their account starts as `tech` role — they can only see WOs assigned to them, update status, log time, and add notes. They cannot create WOs, see billing, or access settings.

To assign a WO to a tech, enter their signup email in the "Assign Technicians" field when creating the WO.

---

## Step 4: Deploy to GitHub Pages

Upload index.html, manifest.json, sw.js to your existing fieldops GitHub repo.
Your URL stays the same: https://urielloera-dot.github.io/fieldops/

---

## Roles Summary

| Feature | Admin (you) | Tech |
|---|---|---|
| Create work orders | ✅ | ❌ |
| See all work orders | ✅ | ❌ (only assigned) |
| Update status | ✅ | ✅ |
| Log time | ✅ | ✅ |
| Add notes | ✅ | ✅ |
| Billing / line items | ✅ | ❌ |
| Generate invoices | ✅ | ❌ |
| Settings | ✅ | ❌ |
| Real-time sync | ✅ | ✅ |
