# Supabase Database Setup Guide

Follow these steps to configure your free Supabase database. This handles your orders, reviews, photo storage, and security.

---

## Step 1: Create the Database Tables & Triggers

1. Go to your [Supabase Dashboard](https://supabase.com).
2. Open your project, click on the **SQL Editor** in the left sidebar, and click **New Query**.
3. Copy and paste the entire script below into the editor, then click **Run**:

```sql
-- 1. Create REVIEWS table
create table public.reviews (
  id uuid default gen_random_uuid() primary key,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null,
  name text not null,
  rating integer check (rating >= 1 and rating <= 5) not null,
  text text not null,
  photo_url text,
  status text default 'pending'::text check (status in ('pending', 'approved', 'rejected')) not null
);

-- 2. Create ORDERS table
create table public.orders (
  id text primary key, -- E.g., DGS-MZG-1024
  created_at timestamp with time zone default timezone('utc'::text, now()) not null,
  customer_name text not null,
  whatsapp_number text not null,
  address text not null,
  items jsonb not null,
  custom_instructions text,
  total_price numeric not null,
  status text default 'pending'::text check (status in ('pending', 'confirmed', 'delivered', 'cancelled')) not null
);

-- 3. Security Trigger: Ensure newly submitted reviews are ALWAYS 'pending'
create or replace function public.handle_new_review()
returns trigger as $$
begin
  new.status := 'pending';
  return new;
end;
$$ language plpgsql security definer;

create trigger on_review_insert
  before insert on public.reviews
  for each row execute function public.handle_new_review();

-- 4. Enable Row-Level Security (RLS)
alter table public.reviews enable row level security;
alter table public.orders enable row level security;

-- 5. RLS Policies for REVIEWS
create policy "Allow public read of approved reviews"
  on public.reviews for select
  using (status = 'approved');

create policy "Allow public insert of reviews"
  on public.reviews for insert
  with check (true);

create policy "Allow admins full access to reviews"
  on public.reviews for all
  using (auth.role() = 'authenticated')
  with check (auth.role() = 'authenticated');

-- 6. RLS Policies for ORDERS
create policy "Allow public insert of orders"
  on public.orders for insert
  with check (true);

create policy "Allow public tracking of their own order"
  on public.orders for select
  using (true); -- Clients can look up orders, but they must know the exact Order ID.

create policy "Allow admins full access to orders"
  on public.orders for all
  using (auth.role() = 'authenticated')
  with check (auth.role() = 'authenticated');
```

---

## Step 2: Set Up Storage for Review Photos

Customers can optionally upload photos with their reviews. We need a Storage Bucket in Supabase to host these images:

1. Click on the **Storage** icon in the Supabase sidebar.
2. Click **New Bucket**.
3. Set the Bucket Name to exactly: `review-photos`
4. Toggle **Public** to **ON** (so clients can load the photos on the home page).
5. Click **Save**.
6. Open **Policies** under the storage tab:
   - Create a policy for **Insert**: Allow anyone to upload images (anonymous upload allowed).
   - Create a policy for **Select**: Allow public read access to images.

---

## Step 3: Create an Admin Account

To log into your admin dashboard securely:

1. Go to the **Authentication** tab in Supabase.
2. Click **Users** -> **Add User** -> **Create User**.
3. Enter your admin email and a strong password.
4. Use these credentials to sign in directly on the website's admin page.
