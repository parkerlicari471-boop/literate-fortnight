# literate-fortnight*** Begin Patch
*** Add File: README.md
+# MarketMindAI — AI Marketing Assistant (MVP scaffold)
+
+This repo is a starter scaffold for the MarketMindAI marketing assistant MVP.
+
+What's included
+- apps/web — Next.js (App Router) TypeScript frontend with Tailwind
+- apps/api — Next.js API route (simple serverless route for plan generation)
+- packages/llm — small provider-agnostic LLM wrapper (OpenAI example)
+- packages/supabase-client — Supabase client (browser & server)
+- .env.example — environment variables to configure
+
+Quick start (local)
+1. Install dependencies (root uses pnpm workspaces)
+   - pnpm install
+
+2. Create .env in repo root (see .env.example)
+   - Copy values and provide keys when available
+
+3. Run dev
+   - pnpm --filter web dev
+   - pnpm --filter api dev
+
+Notes
+- The API route /api/generate-plan uses the LLM wrapper. If OPENAI_API_KEY is not set, it returns a sample fixture.
+- Use this scaffold to iterate on onboarding, strategy generation, and asset flows.
+
+Recommended next steps
+- Create branch feature/mvp-scaffold and commit these files
+- Wire a Supabase project or Postgres if you want auth and DB quickly
+- Add an OpenAI API key for real generation or configure another provider in packages/llm
+
+License
+- Add your preferred license file.
+
*** End Patch
*** Begin Patch
*** Add File: .env.example
+# Server
+NEXT_PUBLIC_API_URL=http://localhost:3000
+
+# LLM (OpenAI example)
+OPENAI_API_KEY=
+OPENAI_MODEL=gpt-4o-mini
+
+# Supabase
+SUPABASE_URL=https://your-project.supabase.co
+NEXT_PUBLIC_SUPABASE_ANON_KEY=public-anon-key-here
+SUPABASE_SERVICE_ROLE_KEY=service-role-key-here
+
+# Optional DB (alternate)
+DATABASE_URL=
+
+# Object storage (S3-compatible)
+S3_BUCKET=
+S3_KEY=
+S3_SECRET=
+S3_REGION=
+S3_ENDPOINT=
+
+# Redis (for BullMQ / workers)
+REDIS_URL=
+
+# Stripe (optional)
+STRIPE_SECRET_KEY=
+
*** End Patch
*** Begin Patch
*** Add File: package.json
+{
+  "name": "marketmindai",
+  "private": true,
+  "version": "0.1.0",
+  "workspaces": [
+    "apps/*",
+    "packages/*"
+  ],
+  "scripts": {
+    "dev": "concurrently \"pnpm --filter web dev\" \"pnpm --filter api dev\"",
+    "dev:web": "pnpm --filter web dev",
+    "dev:api": "pnpm --filter api dev",
+    "build": "pnpm -w run -r build",
+    "lint": "pnpm -w run -r lint"
+  },
+  "devDependencies": {
+    "concurrently": "^8.0.0",
+    "pnpm": "^8.0.0"
+  }
+}
+
*** End Patch
*** Begin Patch
*** Add File: .gitignore
+node_modules
+.env
+dist
+.next
+.DS_Store
+
*** End Patch
*** Begin Patch
*** Add File: apps/web/package.json
+{
+  "name": "web",
+  "private": true,
+  "version": "0.1.0",
+  "scripts": {
+    "dev": "next dev -p 3000",
+    "build": "next build",
+    "start": "next start"
+  },
+  "dependencies": {
+    "@supabase/supabase-js": "^2.0.0",
+    "clsx": "^1.2.1",
+    "next": "14.2.0",
+    "react": "18.2.0",
+    "react-dom": "18.2.0"
+  },
+  "devDependencies": {
+    "tailwindcss": "^4.0.0",
+    "autoprefixer": "^10.4.0",
+    "postcss": "^8.0.0",
+    "typescript": "^5.0.0"
+  }
+}
+
*** End Patch
*** Begin Patch
*** Add File: apps/web/app/page.tsx
+import React from "react";
+
+export default function HomePage() {
+  return (
+    <main className="min-h-screen bg-gray-50 p-8">
+      <div className="max-w-4xl mx-auto">
+        <h1 className="text-3xl font-bold mb-4">MarketMindAI — AI Marketing MVP</h1>
+        <p className="mb-6 text-gray-700">
+          Starter scaffold for MarketMindAI. Use the onboarding wizard to create a marketing plan.
+        </p>
+
+        <section className="bg-white shadow rounded p-6 mb-6">
+          <h2 className="text-xl font-semibold mb-3">Onboarding (demo)</h2>
+          <form
+            id="onboard"
+            onSubmit={async (e) => {
+              e.preventDefault();
+              const form = e.currentTarget as HTMLFormElement;
+              const data = {
+                businessName: (form.elements.namedItem("businessName") as HTMLInputElement).value,
+                description: (form.elements.namedItem("description") as HTMLInputElement).value,
+                goal: (form.elements.namedItem("goal") as HTMLInputElement).value,
+                audience: (form.elements.namedItem("audience") as HTMLInputElement).value
+              };
+              const res = await fetch("/api/generate-plan", {
+                method: "POST",
+                headers: { "Content-Type": "application/json" },
+                body: JSON.stringify(data)
+              });
+              const json = await res.json();
+              const el = document.getElementById("plan")!;
+              el.innerText = JSON.stringify(json, null, 2);
+            }}
+          >
+            <div className="grid grid-cols-1 gap-3 mb-3">
+              <input name="businessName" placeholder="Business name" className="p-2 border rounded" />
+              <input name="description" placeholder="Short description" className="p-2 border rounded" />
+              <input name="goal" placeholder="Primary goal (e.g., leads, sales)" className="p-2 border rounded" />
+              <input name="audience" placeholder="Target audience" className="p-2 border rounded" />
+            </div>
+            <button type="submit" className="bg-blue-600 text-white px-4 py-2 rounded">
+              Generate Plan
+            </button>
+          </form>
+        </section>
+
+        <section className="bg-white shadow rounded p-6">
+          <h2 className="text-xl font-semibold mb-3">Generated plan</h2>
+          <pre id="plan" className="whitespace-pre-wrap text-sm text-gray-800"></pre>
+        </section>
+      </div>
+    </main>
+  );
+}
+
*** End Patch
*** Begin Patch
*** Add File: apps/web/styles/globals.css
+@tailwind base;
+@tailwind components;
+@tailwind utilities;
+
+html, body, #__next {
+  height: 100%;
+}
+
*** End Patch
*** Begin Patch
*** Add File: apps/web/tailwind.config.js
+module.exports = {
+  content: [
+    "./app/**/*.{js,ts,jsx,tsx}",
+    "./components/**/*.{js,ts,jsx,tsx}"
+  ],
+  theme: {
+    extend: {}
+  },
+  plugins: []
+};
+
*** End Patch
*** Begin Patch
*** Add File: apps/web/app/(auth)/sign-in/page.tsx
+import React, { useState } from "react";
+import { getBrowserSupabase } from "../../../../packages/supabase-client";
+
+export default function SignInPage() {
+  const [email, setEmail] = useState("");
+  const [message, setMessage] = useState("");
+  const supabase = getBrowserSupabase();
+
+  async function onSignIn(e: React.FormEvent) {
+    e.preventDefault();
+    setMessage("");
+    const { error } = await supabase.auth.signInWithOtp({ email });
+    if (error) {
+      setMessage(`Error: ${error.message}`);
+    } else {
+      setMessage("Check your email for a login link (magic link).");
+    }
+  }
+
+  return (
+    <main className="min-h-screen flex items-center justify-center p-6">
+      <div className="max-w-md w-full bg-white p-6 rounded shadow">
+        <h1 className="text-xl font-semibold mb-4">Sign in to MarketMindAI</h1>
+        <form onSubmit={onSignIn} className="space-y-3">
+          <input
+            value={email}
+            onChange={(e) => setEmail(e.target.value)}
+            placeholder="you@company.com"
+            className="w-full p-2 rounded border"
+            type="email"
+            required
+          />
+          <button className="w-full bg-blue-600 text-white p-2 rounded">Send magic link</button>
+        </form>
+        {message && <p className="mt-3 text-sm text-gray-700">{message}</p>}
+        <p className="mt-4 text-xs text-gray-500">
+          Signing in will create an account using Supabase Auth. After sign-in you'll be redirected to onboarding.
+        </p>
+      </div>
+    </main>
+  );
+}
+
*** End Patch
*** Begin Patch
*** Add File: apps/web/app/onboarding/page.tsx
+"use client";
+import React, { useEffect, useState } from "react";
+import { getBrowserSupabase } from "../../packages/supabase-client";
+import { useRouter } from "next/navigation";
+
+export default function OnboardingPage() {
+  const supabase = getBrowserSupabase();
+  const router = useRouter();
+  const [user, setUser] = useState<any>(null);
+  const [loading, setLoading] = useState(true);
+  const [saving, setSaving] = useState(false);
+  const [form, setForm] = useState({
+    businessName: "",
+    description: "",
+    goal: "",
+    audience: ""
+  });
+
+  useEffect(() => {
+    let mounted = true;
+    supabase.auth.getSession().then(({ data }) => {
+      if (!mounted) return;
+      setUser(data.session?.user ?? null);
+      setLoading(false);
+    });
+    const { data: listener } = supabase.auth.onAuthStateChange((_event, session) => {
+      setUser(session?.user ?? null);
+      setLoading(false);
+    });
+    return () => {
+      mounted = false;
+      listener.subscription?.unsubscribe();
+    };
+  }, [supabase]);
+
+  async function handleSubmit(e: React.FormEvent) {
+    e.preventDefault();
+    if (!user) {
+      alert("You must be signed in to continue.");
+      return;
+    }
+    setSaving(true);
+    const res = await fetch("/api/onboarding", {
+      method: "POST",
+      headers: { "Content-Type": "application/json" },
+      body: JSON.stringify({ user_id: user.id, ...form })
+    });
+    const json = await res.json();
+    setSaving(false);
+    if (res.ok) {
+      // Redirect to dashboard or generate plan
+      router.push("/");
+    } else {
+      alert("Error saving onboarding: " + (json.error || "unknown"));
+    }
+  }
+
+  if (loading) return <div className="p-6">Loading...</div>;
+
+  if (!user) {
+    return (
+      <div className="p-6">
+        <p className="mb-3">You must sign in first.</p>
+        <a href="/(auth)/sign-in" className="text-blue-600">Go to sign in</a>
+      </div>
+    );
+  }
+
+  return (
+    <main className="min-h-screen p-8 bg-gray-50">
+      <div className="max-w-lg mx-auto bg-white p-6 rounded shadow">
+        <h1 className="text-xl font-semibold mb-4">Welcome, finish onboarding</h1>
+        <form onSubmit={handleSubmit} className="space-y-3">
+          <input
+            value={form.businessName}
+            onChange={(e) => setForm({ ...form, businessName: e.target.value })}
+            placeholder="Business name"
+            className="w-full p-2 border rounded"
+            required
+          />
+          <input
+            value={form.description}
+            onChange={(e) => setForm({ ...form, description: e.target.value })}
+            placeholder="Short description"
+            className="w-full p-2 border rounded"
+          />
+          <input
+            value={form.goal}
+            onChange={(e) => setForm({ ...form, goal: e.target.value })}
+            placeholder="Primary goal (e.g., leads, sales)"
+            className="w-full p-2 border rounded"
+          />
+          <input
+            value={form.audience}
+            onChange={(e) => setForm({ ...form, audience: e.target.value })}
+            placeholder="Target audience"
+            className="w-full p-2 border rounded"
+          />
+          <button disabled={saving} className="w-full bg-green-600 text-white p-2 rounded">
+            {saving ? "Saving..." : "Save onboarding"}
+          </button>
+        </form>
+      </div>
+    </main>
+  );
+}
+
*** End Patch
*** Begin Patch
*** Add File: apps/api/package.json
+{
+  "name": "api",
+  "private": true,
+  "version": "0.1.0",
+  "scripts": {
+    "dev": "next dev -p 3001",
+    "build": "next build",
+    "start": "next start -p 3001"
+  },
+  "dependencies": {
+    "next": "14.2.0",
+    "react": "18.2.0",
+    "react-dom": "18.2.0",
+    "node-fetch": "^3.4.0",
+    "@supabase/supabase-js": "^2.0.0"
+  },
+  "devDependencies": {
+    "typescript": "^5.0.0"
+  }
+}
+
*** End Patch
*** Begin Patch
*** Add File: apps/api/pages/api/generate-plan.ts
+import type { NextApiRequest, NextApiResponse } from "next";
+import { generatePlan } from "../../../packages/llm";
+
+export default async function handler(req: NextApiRequest, res: NextApiResponse) {
+  try {
+    if (req.method !== "POST") {
+      return res.status(405).json({ error: "Method not allowed" });
+    }
+    const body = req.body || {};
+    const { businessName, description, goal, audience } = body;
+
+    const promptInput = {
+      businessName,
+      description,
+      goal,
+      audience
+    };
+
+    const plan = await generatePlan(promptInput);
+    return res.status(200).json(plan);
+  } catch (err: any) {
+    console.error(err);
+    return res.status(500).json({ error: err.message || "Internal error" });
+  }
+}
+
*** End Patch
*** Begin Patch
*** Add File: apps/api/pages/api/onboarding.ts
+import type { NextApiRequest, NextApiResponse } from "next";
+import { getServerSupabase } from "../../../packages/supabase-client";
+
+export default async function handler(req: NextApiRequest, res: NextApiResponse) {
+  try {
+    if (req.method !== "POST") return res.status(405).json({ error: "Method not allowed" });
+
+    const { user_id, businessName, description, goal, audience } = req.body;
+    if (!user_id || !businessName) {
+      return res.status(400).json({ error: "Missing required fields" });
+    }
+
+    const supabase = getServerSupabase();
+    // Insert onboarding record into "onboardings" table
+    const { data, error } = await supabase.from("onboardings").insert([
+      {
+        user_id,
+        business_name: businessName,
+        description,
+        goal,
+        audience
+      }
+    ]);
+
+    if (error) {
+      console.error("Supabase insert error:", error);
+      return res.status(500).json({ error: error.message });
+    }
+
+    return res.status(200).json({ ok: true, inserted: data });
+  } catch (err: any) {
+    console.error(err);
+    return res.status(500).json({ error: err.message || "Internal error" });
+  }
+}
+
*** End Patch
*** Begin Patch
*** Add File: apps/api/pages/api/session.ts
+import type { NextApiRequest, NextApiResponse } from "next";
+import { getServerSupabase } from "../../../packages/supabase-client";
+
+/**
+ * Optional: extant endpoint to verify session server-side if needed.
+ */
+export default async function handler(_req: NextApiRequest, res: NextApiResponse) {
+  try {
+    const supabase = getServerSupabase();
+    // Ping Supabase to ensure keys are valid
+    const { data, error } = await supabase.rpc("now");
+    if (error) return res.status(500).json({ ok: false, error: error.message });
+    res.status(200).json({ ok: true, data });
+  } catch (err: any) {
+    res.status(500).json({ ok: false, error: err.message });
+  }
+}
+
*** End Patch
*** Begin Patch
*** Add File: packages/llm/index.ts
+import fetch from "node-fetch";
+
+type PlanInput = {
+  businessName?: string;
+  description?: string;
+  goal?: string;
+  audience?: string;
+};
+
+export async function generatePlan(input: PlanInput) {
+  const apiKey = process.env.OPENAI_API_KEY;
+  if (!apiKey) {
+    // Fixture for local development when no key is present
+    return {
+      source: "fixture",
+      input,
+      plan: {
+        summary: `Sample 3-month marketing plan for ${input.businessName || "Your business"}.`,
+        months: [
+          { month: "Month 1", focus: "Brand + Awareness", actions: ["Run awareness ads", "Create landing page"] },
+          { month: "Month 2", focus: "Conversion", actions: ["Retargeting ads", "Email capture"] },
+          { month: "Month 3", focus: "Optimization", actions: ["A/B test creatives", "Scale best channels"] }
+        ],
+        kpis: { cpl: "Target", ctr: "Target", conversions: "Target" },
+        campaignConcepts: [
+          { name: "Paid Awareness", summary: "Run targeted social ads for initial traction" },
+          { name: "Organic Content", summary: "Leverage content + social for engagement" },
+          { name: "Partnerships", summary: "Collaborate with micro-influencers and partners" }
+        ]
+      }
+    };
+  }
+
+  // Example OpenAI call (POST to chat/completions)
+  const model = process.env.OPENAI_MODEL || "gpt-4o-mini";
+  const system = `You are an expert growth marketer. Given business details, produce a concise 3-month marketing plan with channels, KPIs and 3 campaign concepts.`;
+
+  const userPrompt = `Business: ${input.businessName || ""}\nDescription: ${input.description || ""}\nGoal: ${input.goal || ""}\nAudience: ${input.audience || ""}\n\nOutput JSON with fields: summary, months[], kpis, campaignConcepts[]`;
+
+  const payload = {
+    model,
+    messages: [
+      { role: "system", content: system },
+      { role: "user", content: userPrompt }
+    ],
+    temperature: 0.7,
+    max_tokens: 800
+  };
+
+  const response = await fetch("https://api.openai.com/v1/chat/completions", {
+    method: "POST",
+    headers: {
+      "Content-Type": "application/json",
+      Authorization: `Bearer ${apiKey}`
+    },
+    body: JSON.stringify(payload)
+  });
+
+  if (!response.ok) {
+    const text = await response.text();
+    throw new Error(`LLM error: ${response.status} ${text}`);
+  }
+
+  const data = await response.json();
+  const content = data.choices?.[0]?.message?.content || data.choices?.[0]?.text || "";
+  try {
+    const parsed = JSON.parse(content);
+    return { source: "openai", parsed, raw: content };
+  } catch (e) {
+    return { source: "openai", parsed: null, raw: content };
+  }
+}
+
*** End Patch
*** Begin Patch
*** Add File: packages/supabase-client/index.ts
+import { createClient as createBrowserClient, SupabaseClient } from "@supabase/supabase-js";
+
+const supabaseUrl = process.env.SUPABASE_URL || "";
+const anonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY || "";
+
+/**
+ * Client for use in browser (frontend). Uses NEXT_PUBLIC_SUPABASE_ANON_KEY.
+ */
+export function getBrowserSupabase(): SupabaseClient {
+  return createBrowserClient(supabaseUrl, anonKey);
+}
+
+/**
+ * Server-side supabase client using the service role key.
+ * Only use on server (API routes) and keep the service key secret.
+ */
+export function getServerSupabase() {
+  const { SUPABASE_SERVICE_ROLE_KEY } = process.env;
+  if (!SUPABASE_SERVICE_ROLE_KEY) {
+    throw new Error("Missing SUPABASE_SERVICE_ROLE_KEY in environment");
+  }
+  const { createClient } = require("@supabase/supabase-js");
+  return createClient(supabaseUrl, SUPABASE_SERVICE_ROLE_KEY);
+}
+
*** End Patch
*** End Patch
