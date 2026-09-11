<h1>Pocket Stock</h1>

Pocket Stock is a mobile-first web application that lets you practice Warren Buffett–style value investing by uploading annual reports and broker statements, extracting financial metrics, and tracking a concentrated 5–10 company portfolio. It demonstrates end-to-end product thinking: a polished React/TanStack frontend, secure cloud storage, AI-powered document analysis, and a full-stack architecture with Row-Level Security.
<br>

[Link to Web Application](https://pocket-stockk.lovable.app/auth)

<br>
<h2>Purpose & Concept</h2>

The app is built around a clear investing thesis: hold 5–10 companies you genuinely understand, judge them on their published numbers rather than stock-price noise, and keep your portfolio concentrated enough to matter. Pocket Stock turns that philosophy into a practical workflow:

1. Upload a PDF annual report.
2. The app reads the Income Statement, Balance Sheet, and Cash Flow Statement in order.
3. It computes 14 Buffett-style rules of thumb and color-codes pass/fail.
4. You track your holdings, conviction rank, and allocation in one dashboard.

Everything is conceptual practice. No real money, brokerage accounts, or bank details!

<br>
<h2>Tech Stack</h2>

* Framework: TanStack Start v1 (React 19, full-stack SSR/edge-ready)
* Build tool: Vite 7
* Styling: Tailwind CSS v4, shadcn/ui component primitives
* Backend: Lovable Cloud (Supabase) => PostgreSQL, auth, private file storage
* State/Server functions: TanStack Query + createServerFn RPC
* File upload: Supabase Storage with signed URLs
* AI analysis: Lovable AI Gateway (OpenAI model) via structured JSON schema, streaming responses
* Drag-and-drop: dnd-kit for reorderable portfolio rows
* Language: TypeScript throughout

<br>
<h2>Key Features</h2>

1. Auth-gated experience

* Sign-up / sign-in required before accessing the app.
* Protected routes sit behind an _authenticated layout.
* Google OAuth support.

2. Portfolio dashboard

* Mobile-first, responsive design with a fixed bottom navigation bar (Portfolio, Upload Statements, Analysis, Account).
* Visual target banner: “5–10 companies” with a threshold slider showing where the current count sits.
* Table of holdings with stock name, % of wallet invested, and rank.
* Drag-and-drop rank reordering via the Rank handle.
* Inline percentage editing with validation (total cannot exceed 100%).
* Empty-state guidance when all companies are deleted.

3. Document upload & management

* Drag-and-drop or file-picker PDF upload (≤25 MB).
* Optional link to an existing company or “Add a new company from this PDF.”
* Document list with file name, company badge, size, upload date, and status chip.
* Delete with confirmation. Stored file removed alongside the database row.

4. AI-powered financial analysis

* After upload, the PDF is sent to an AI model with a strict JSON schema.
* Reads statements in order: Income Statement → Balance Sheet → Cash Flow Statement.
* Extracts 14 metrics across three statement groups.
* Detects whether the company is a product or service business and applies formula variants accordingly.
* Handles synonyms for line items (ex: “Net Sales,” “Total Revenue,” “Turnover” for revenue).
* Falls back: if Gross Profit is missing, derives it from Revenue - Cost of Goods Sold.
* Results are stored per document and per fiscal year.

5. Spreadsheet-style analysis results

* Each company’s analysis page renders a table with years as rows.
* Grouped sections for Income Statement, Balance Sheet, and Cash Flow Statement.
* Green cells = passed threshold, red = failed, grey = figure not found.
* Detail dialogs for any metric with exact wording extracted, amounts, source location, and notes.

6. Formula audit log

* Every analysis keeps a full audit trail: exact PDF wording, amount, source statement/page, formula, and result.
* Users can open “View formula log” to inspect and correct extracted figures.
* Corrections are saved as overrides and recalculate results in real time.
* EPS Growth and Retained Earnings growth are intentionally “half-complete”: only the current-year figure is extracted. The user supplies the prior-year figure to complete the comparison.

7. Company pages

* Market movement section (conceptual / coming soon, hyperlinked to a dedicated page).
* Company News & Analysis section (coming soon page).
* Live Notes section with its own page.
* Delete company action with cascading deletion of linked reports, analyses, and stored PDFs.

8. Information / About page

* Explains the app’s philosophy, every formula with thresholds, the service-company variants, the full synonym list, and why two growth metrics await manual input.
* Linked from the Portfolio tab.
<br>

<h2>Technical Highlights</h2>

* Full-stack type safety: TypeScript from the UI through createServerFn RPC to the database.
* Secure multi-tenant storage: Each user owns their documents bucket path. Row-Level Security policies ensure users can only read/write their own rows.
* Structured AI extraction: Uses a strict JSON schema, streaming response parsing, and exact-verbatim wording capture for auditability, not just an opaque summary.
* Resilient analysis lifecycle: Tracks uploaded → analysing → analysed | failed, supports retry, surfaces AI-gateway errors (rate limits, credits), and preserves partial results.
* Smart company creation: When “Add a new company from this PDF” is chosen, the intent is persisted so retries and detail-page actions still create the holding at 0% allocation.
* Account seeding guard: Starter portfolio is added only once per account via a portfolio_setup marker. Deleting all companies keeps the portfolio empty.
* Document rename cascade: Changing a company name/ticker updates linked documents automatically.
* Manual override architecture: User corrections live in a separate metric_overrides column and resolve on top of AI output without destroying the original reading.
<br>

<h2>What This Demonstrates</h2>

* Designing a product around a real-world workflow (value-investing document analysis).
* Building a polished mobile-first UI with accessibility considerations (ARIA labels, keyboard reordering).
* Integrating generative AI safely and transparently through schemas, audit trails, and manual overrides.
* Implementing production-grade backend patterns: auth middleware, RLS, signed storage URLs, transactional deletes, and idempotent seeding.
* Writing maintainable TypeScript across a modern React full-stack framework.
