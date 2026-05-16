# PromptIQ — Local Setup Guide (Windows + VS Code)

## Prerequisites

- [Node.js 20+](https://nodejs.org/)
- [PostgreSQL](https://www.postgresql.org/download/windows/) (or use [Supabase](https://supabase.com) for a free hosted DB)
- [VS Code](https://code.visualstudio.com/)
- An [OpenAI API Key](https://platform.openai.com/api-keys)

---

## Step 1: Clone the Project

```bash
git clone <your-repo-url>
cd promptiq
```

---

## Step 2: Install Dependencies

```bash
npm install
```

---

## Step 3: Configure Environment Variables

Copy the example env file and fill in your values:

```bash
copy .env.example .env
```

Open `.env` and set:

```env
DATABASE_URL=postgresql://postgres:yourpassword@localhost:5432/promptiq
SESSION_SECRET=some-long-random-string
OPENAI_API_KEY=sk-...
PORT=5000
```

Generate a secure session secret with:

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

---

## Step 4: Update package.json Scripts for Windows

Open `package.json` and replace the `scripts` section with:

```json
"scripts": {
  "dev": "cross-env NODE_ENV=development tsx server/index.ts",
  "start": "cross-env NODE_ENV=development tsx server/index.ts",
  "build": "tsx script/build.ts",
  "serve": "cross-env NODE_ENV=production node dist/index.cjs",
  "check": "tsc",
  "db:push": "drizzle-kit push"
},
```

> `cross-env` is already installed. This change makes the scripts work on Windows.

---

## Step 5: Create the Database

Make sure PostgreSQL is running, then create the database:

```bash
psql -U postgres -c "CREATE DATABASE promptiq;"
```

Then push the schema:

```bash
npm run db:push
```

---

## Step 6: Start the App

```bash
npm start
```

Open your browser at: **http://localhost:5000**

---

## First Time Use

1. Click **Get Started** on the landing page.
2. Create an account with a username and password.
3. Start optimizing your prompts!

---

## VS Code Tips

- Install the **ESLint** and **Prettier** extensions for code formatting.
- Install the **Tailwind CSS IntelliSense** extension for class autocomplete.
- Use the built-in terminal (`Ctrl + \``) to run commands.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `DATABASE_URL must be set` | Make sure `.env` exists and has a valid `DATABASE_URL` |
| `EADDRINUSE` (port in use) | Change `PORT=5001` in `.env` |
| `Invalid username or password` | Register a new account first — there's no default account |
| DB connection refused | Ensure PostgreSQL is running (`pg_ctl start`) |
| OpenAI errors | Check your `OPENAI_API_KEY` is valid and has credits |

---

## Project Structure

```
promptiq/
├── client/          # React frontend (Vite)
├── server/          # Express backend
├── shared/          # Shared types and schemas
├── .env.example     # Environment variable template
└── SETUP.md         # This file
```
