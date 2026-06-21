# GitHub Deployment & Live Hosting Guide

Since the upgraded **Dreamy Gift Studio Website** communicates directly with your Supabase database client-side, we can host the entire platform for **free** on **GitHub Pages**! 

Follow these steps to make your website live and get a shareable link for your clients:

---

## Step 1: Create a GitHub Repository

1. Log into your [GitHub account](https://github.com).
2. Click the **+** icon in the top-right corner and select **New repository**.
3. Set the **Repository name** to something like: `dreamy-gift-studio`
4. Keeps the repository **Public** (required for the free tier of GitHub Pages).
5. Do **NOT** initialize the repository with a README, `.gitignore`, or license (the codebase is already initialized locally).
6. Click **Create repository**.

---

## Step 2: Link and Push Your Code

Open your computer's terminal (or VS Code terminal) in the project folder `d:\Projects\Dreamy Gift Studio Website` and run the following commands (replace `<your-username>` with your actual GitHub username):

```bash
# 1. Rename the default branch to 'main'
git branch -M main

# 2. Link your local project to your newly created GitHub repository
git remote add origin https://github.com/<your-username>/dreamy-gift-studio.git

# 3. Push your files to GitHub
git push -u origin main
```

---

## Step 3: Enable GitHub Pages

Once your code is successfully uploaded to GitHub:

1. Open your repository on the GitHub website.
2. Click on the **Settings** tab in the top repository menu bar.
3. In the left sidebar under the "Code and automation" section, click on **Pages**.
4. Under the **Build and deployment** section, look for **Source**:
   - Keeps it set to **Deploy from a branch**.
5. Under the **Branch** dropdown:
   - Select **main** instead of `None`.
   - Leave the folder as `/ (root)`.
   - Click **Save**.
6. Wait 1–2 minutes, then refresh the page.
7. GitHub will display a notification banner at the top of the Pages screen with your public live URL:
   `Your site is live at https://<your-username>.github.io/dreamy-gift-studio/`

---

## Step 4: Access and test Your Site

You can now share this URL with your clients! They will be able to:
- Browse your products and place customized orders.
- Submit reviews and ratings (optionally attaching photos).
- Track their order statuses.

You can access your Admin Panel by visiting:
`https://<your-username>.github.io/dreamy-gift-studio/#admin`

---

## Technical Note: Supabase vs Fallback Mode
By default, the website runs in **Local Fallback Mode** which stores all orders and reviews inside the browser's `localStorage`. This allows you to test the entire client ordering, review submission, and admin dashboard workflows immediately without any database setup!

Once you are ready to connect a live database:
1. Open the [index.html](file:///d:/Projects/Dreamy%20Gift%20Studio%20Website/index.html) file.
2. Scroll to the `<script>` tag at the bottom (around line 1318).
3. Replace the `SUPABASE_URL` and `SUPABASE_ANON_KEY` placeholders with your actual keys from your Supabase dashboard settings.
4. Save, commit, and run `git push` to deploy it live!
