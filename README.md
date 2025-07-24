# GPT Wrapper

## Overview

**GPT Wrapper** is a full-stack AI-powered market research and analytics platform for pharma/biotech. It includes:
- Multiple Next.js frontends (commercial dashboard, scientific dashboard, etc.)
- A robust backend API for model orchestration and enrichment
- Integration with LLMs (OpenAI, Perplexity, etc.)
- Modular, extensible architecture for rapid prototyping and research

---

## Directory Structure

```
GPT Wrapper/
├── GPT/                  # Main Next.js app (commercial/scientific dashboards)
│   ├── app/              # Next.js app directory (pages, API routes)
│   ├── components/       # React components (dashboard, UI, etc.)
│   ├── hooks/            # Custom React hooks
│   ├── lib/              # Utility libraries (e.g., supabaseClient, utils)
│   ├── public/           # Static assets
│   ├── styles/           # CSS/Tailwind styles
│   ├── ...
├── Commerical-Agent/     # Alternate or v0 frontend (legacy or prototype)
│   ├── app/
│   ├── components/
│   ├── ...
├── package.json          # Root package (if monorepo)
├── README.md             # This file
└── ...
```

---

## Quickstart

### 1. **Clone the Repo**
```sh
git clone https://github.com/Rahulvijayan123/gpt-wrapper.git
cd gpt-wrapper
```

### 2. **Install Dependencies**
- For the main app:
  ```sh
  cd GPT
  npm install  # or pnpm install
  ```
- For the alternate frontend (if needed):
  ```sh
  cd ../Commerical-Agent
  npm install  # or pnpm install
  ```

### 3. **Run the App**
- **Main app:**
  ```sh
  cd GPT
  npm run dev
  # App runs at http://localhost:3000
  ```
- **Alternate frontend:**
  ```sh
  cd ../Commerical-Agent
  npm run dev
  # App runs at http://localhost:3000 (or another port)
  ```

---

## How the System Works

### **Frontend (Next.js)**
- User interacts with dashboards (commercial/scientific) via forms and UI components.
- On submit, the frontend makes a POST request to `/api/market` (or similar API route).
- The UI displays parsed model output (market size, CAGR, competitors, etc.) and a debug section with raw API response.

### **Backend (API Route)**
- Receives POST requests with program details (e.g., indication, target, region).
- Calls LLM APIs (OpenAI, Perplexity, etc.) with a structured prompt.
- Parses the model output to extract structured fields (`marketSize`, `cagr`, `competitors`, `fullAnalysis`).
- Returns a JSON object with these fields to the frontend.
- Handles errors robustly and logs all responses.

### **Model API Calls**
- The backend sends a prompt to the LLM (OpenAI, Perplexity, etc.).
- Expects a structured response (or parses unstructured output).
- If Perplexity returns an error (e.g., invalid model), falls back to OpenAI.
- Always returns a structured object to the frontend.

---

## Debugging & Development

- **Debug section in UI:** Always shows raw API response and parsed values. This is critical for verifying what the backend returns and what the parser extracts. If the UI is not updating, check this section first.
- **Console logs:** Both backend and frontend log all key data and errors. Check your browser console and the terminal running `npm run dev` for details.
- **Error boundary:** UI will display any JavaScript errors instead of crashing. If you see a red error box, check the error message and stack trace.
- **API errors:** Shown in both browser console and terminal running `npm run dev`.
- **Network tab:** Inspect `/api/market` requests for payload and response. This is essential for debugging data flow.

---

## Current Debugging Step: "Unknown error from backend"

If you see **"Unknown error from backend"** in the UI, it means the backend API route threw an error that was not handled or logged clearly. To debug:

1. **Check the terminal running `npm run dev`** (in `GPT/`). Look for red error messages or stack traces right after you submit the form or trigger the API call.
2. **Check the browser Network tab** (DevTools → Network → `/api/market`). Look for the response body and status code. If it's a 500 error, the backend failed.
3. **Check the debug section in the UI** for any error messages or raw output.
4. **Common causes:**
   - Missing environment variables (API keys, etc.)
   - Model API errors (e.g., Perplexity model not available, OpenAI quota exceeded)
   - Parser errors (unexpected model output format)
   - Unhandled exceptions in the API route

**How to fix:**
- Make sure all required environment variables are set (see `.env.local.example`).
- Check the model API documentation for valid model names.
- Add more logging to the backend to capture the error details.
- If the error is from Perplexity, the backend will fall back to OpenAI, but if both fail, the error is returned.

---

## Agent UI/UX Nuances & Output Expectations

- **Agent output should always be structured:**
  - `marketSize` (string, e.g., "$5.5B")
  - `cagr` (string, e.g., "6.3%")
  - `competitors` (array of strings, e.g., `["Roche", "Merck", "Genentech"]`)
  - `fullAnalysis` (string, full model output)
- **UI should never show hardcoded or placeholder values** when real data is available.
- **Blurring:** Only placeholder content should be blurred. Real model output should always be unblurred and clearly visible. Inspect the DOM for `filter: blur()` or `opacity` CSS if you suspect blurring issues.
- **Debug section:** This is your single source of truth for what the backend returned and what the parser extracted. If the UI is not updating, check here first.
- **Error boundary:** If the UI crashes, you will see a red error box with the error message and stack trace. Use this to debug JavaScript errors.

---

## Agent Flow: Correct vs. Incorrect Results

- **Correct result:**
  - The UI displays the parsed `marketSize`, `cagr`, and `competitors` fields, and the full analysis.
  - The debug section shows the raw API response and the parsed values.
  - No fallback or placeholder values are shown.
- **Incorrect result:**
  - The UI shows "N/A" or placeholder values even though the API returned data.
  - The debug section shows an error, empty fields, or a raw string instead of a structured object.
  - The UI is blurred or hidden when it shouldn't be.
  - The UI crashes (red error box) or shows "Unknown error from backend".

---

## Continuing Development

- All code is in this repo (except `node_modules`).
- To add new features, create new components in `GPT/components/` or new API routes in `GPT/app/api/`.
- To update the model logic, edit `GPT/app/api/market/route.ts`.
- To add new dashboards, create new pages in `GPT/app/`.
- Use the debug section and logs to verify all data flow.
- If you encounter errors, follow the debugging steps above.

---

## Submodules & Caveats

- If you see warnings about "embedded git repositories" for `GPT/` or `Commerical-Agent/`, remove their `.git` folders and re-add them as regular folders if you want everything tracked in one repo.
- `node_modules` is not tracked; always run `npm install` after cloning.
- Environment variables (e.g., API keys) may be required; see `.env.local.example` or ask the maintainer.
- Perplexity model names change frequently; check their docs for valid models. If you get a model error, update the model name in the backend.
- If both Perplexity and OpenAI fail, the backend will return an error to the frontend.

---

## Contribution Guidelines

1. Fork the repo and create a feature branch.
2. Make your changes with clear commit messages.
3. Test thoroughly (UI, API, error handling).
4. Open a pull request with a description of your changes.

---

## Contact

- **Maintainer:** Rahul Vijayan ([@Rahulvijayan123](https://github.com/Rahulvijayan123))
- **Issues:** Please use GitHub Issues for bugs, questions, or feature requests.

---

## License

MIT (or specify your license here) 