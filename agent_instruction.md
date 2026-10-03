# HEALTHWAY — AUTONOMOUS IMPLEMENTATION TASK

You are the primary software engineer responsible for implementing the complete MVP of **HealthWay** in this repository.

Work autonomously and take ownership of the implementation from the existing repository/files I provide. Do not repeatedly ask me to make routine technical decisions. Inspect the repository first, understand what already exists, reuse useful files, and implement the project end-to-end.

If something is missing, choose the simplest robust solution that satisfies the requirements below.

The goal is to leave the repository with a **working, deployable Gradio web application** suitable for Hugging Face Spaces.

---

## 1. PRODUCT

HealthWay is a **Hausa-first, voice-first healthcare accessibility platform**.

Core idea:

> Speak to ask. Speak to find.

The MVP has three user journeys:

### A. Health information

The user asks a primary-healthcare question in Hausa.

Example:

> "Menene alamomin malaria?"

The system converts speech to text, identifies it as a health-information request, retrieves relevant information from the provided healthcare knowledge base, and responds in clear Hausa.

This is an information/retrieval system, NOT an AI diagnostic system.

### B. Hospital indoor wayfinding

The user asks for a hospital destination.

Example:

> "Ina dakin gwaje-gwaje?"

The system identifies the requested destination and returns:

* destination name;
* Hausa directions;
* route;
* simulated hospital map;
* relevant landmark images.

The hospital is fictional/simulated for the MVP.

### C. Nearby hospital

The user asks:

> "Ina asibiti kusa da ni?"

The system should provide a clear Hausa action that opens Google Maps for nearby hospitals.

Do NOT build a nationwide hospital database.

---

# 2. IMPORTANT CHANGE: DO NOT USE N-ATLAS

Do NOT download, install, load, quantize, fine-tune, or host N-ATLAS.

The reason is that the model is too heavy for this MVP and the required inference setup is not conveniently available.

Instead, implement speech recognition through a **hosted API**.

Use a provider abstraction so the speech-to-text provider can easily be changed later.

Preferred prototype approach:

* Groq-hosted speech-to-text if an appropriate currently supported transcription model/API is available.
* Read the current provider documentation rather than assuming an obsolete model ID.
* Store the API key in an environment variable such as `GROQ_API_KEY`.
* NEVER hard-code API keys.
* NEVER commit `.env`.

Create a clean abstraction such as:

`services/speech.py`

with a simple interface conceptually equivalent to:

`transcribe_audio(audio_path) -> text`

The rest of HealthWay must not depend directly on the provider.

If the provider/API is unavailable during development, implement a graceful fallback and make the application still runnable through text input.

The architecture must therefore be:

Voice → hosted STT API → text → HealthWay routing → service → Hausa response.

The project must NOT depend on downloading a large local speech model.

---

# 3. LANGUAGE REQUIREMENT

This is critical.

The **user-facing application is Hausa-first**.

Do NOT create an English-first interface.

User-facing:

* headings;
* buttons;
* instructions;
* status messages;
* errors;
* healthcare answers;
* navigation instructions;
* confirmations;
* help text

should be in natural, understandable Hausa.

Examples:

"Yi magana"

"Rubuta tambayarka"

"Muna sauraronka..."

"Ban fahimci abin da ka faɗa ba. Don Allah ka sake magana."

"Buɗe Google Maps"

"Ga hanyar zuwa dakin gwaje-gwaje."

English may be used internally for:

* Python;
* variable names;
* JSON keys;
* technical documentation;
* code comments;
* API communication.

Do not translate technical code identifiers unnecessarily.

---

# 4. INTERFACE

Build the application with **Gradio**.

Use a modern, clean, responsive interface suitable for desktop and mobile browsers.

The interface should feel like a healthcare accessibility product, not a developer demo.

Include:

### Header

HealthWay

Short Hausa description such as:

> "Sauƙaƙa samun bayanan lafiya da wuraren sabis ta murya."

### Main interaction

Prominent microphone/audio input.

Allow:

* microphone recording;
* uploaded audio;
* text fallback.

### Conversation/output area

Show:

* user's recognized Hausa speech/text;
* HealthWay response;
* navigation information;
* relevant images where applicable.

### Main actions

Provide clear options for:

* Tambayar lafiya
* Neman sashen asibiti
* Neman asibiti kusa da ni

Do not overload the UI.

---

# 5. HEALTH INFORMATION SYSTEM

Inspect the files I provide for healthcare knowledge.

Do not invent medical claims when suitable source material is provided.

Create a simple retrieval system appropriate for the size of the supplied knowledge base.

Prefer a lightweight approach for the MVP:

* structured JSON/document knowledge;
* normalization;
* keyword/phrase matching;
* optionally semantic retrieval if it materially improves results without adding unnecessary infrastructure.

Do not introduce a vector database unless the supplied dataset genuinely requires it.

The system should retrieve relevant information and return a concise Hausa answer.

Include a safety boundary:

HealthWay provides general health information and does not diagnose disease or replace healthcare professionals.

---

# 6. HOSPITAL SIMULATION

Use the hospital data/assets I provide.

If hospital assets are provided, integrate them instead of generating replacements.

The simulated hospital may contain:

* Main Entrance
* Reception
* Laboratory
* Pharmacy
* Emergency
* Outpatient Department
* Antenatal Clinic
* Paediatrics

Do not create unnecessary complexity.

Represent destinations and routes in structured data.

Each destination should be able to contain:

* display name;
* Hausa name where appropriate;
* aliases;
* floor;
* route steps;
* landmark images;
* optional map location.

Example user query:

"Where is the laboratory?"

or Hausa equivalent:

"Ina dakin gwaje-gwaje?"

The application should identify the destination despite reasonable wording variations.

---

# 7. GENERATED/STATIC HOSPITAL CONTENT

The hospital environment is explicitly a **simulated demonstration environment**.

Use provided/generated:

* hospital map;
* floor plan;
* landmark photographs;
* directional descriptions.

Keep them internally consistent.

Display an unobtrusive notice such as:

> "Wannan taswirar asibiti ce ta kwaikwayo domin nuna yadda tsarin yake aiki."

Do not imply that the simulated hospital is a real facility.

---

# 8. NAVIGATION RESPONSE

For a destination query, provide:

1. Destination
2. Hausa route description
3. Route steps
4. Hospital map if available
5. Relevant landmark images

Example style:

> **Dakin Gwaje-Gwaje**
>
> Daga babbar kofar shiga, je wajen karɓar baƙi. Daga nan ka bi babban corridor. Ka wuce Pharmacy, sannan ka ci gaba har ka ga alamar dakin gwaje-gwaje.

Do not invent routes that are absent from the supplied hospital data.

---

# 9. NEARBY HOSPITAL

Implement a nearby-hospital action that opens Google Maps.

Use a proper Google Maps search URL for hospitals near the user's location.

Do not build location tracking or a custom map system.

If browser geolocation is not available, simply open a general Google Maps hospital search or provide a clear fallback.

The user-facing action must be in Hausa.

---

# 10. STATIC FILES

The application must work with static assets stored in the repository.

Expected categories include:

* hospital map;
* landmark images;
* icons;
* logos;
* healthcare content.

Use relative paths robustly.

Do not depend on absolute local-machine paths.

Ensure the application works when deployed to Hugging Face Spaces.

---

# 11. PROJECT STRUCTURE

Use or adapt this structure:

healthway/

```
app.py

requirements.txt

README.md

.env.example

services/
    speech.py
    router.py
    health.py
    navigation.py
    maps.py

data/
    health/
        knowledge.json

    hospital/
        hospital.json
        destinations.json
        routes.json

assets/
    hospital/
        map.png
        entrance.jpg
        reception.jpg
        corridor.jpg
        pharmacy.jpg
        laboratory.jpg
        emergency.jpg
```

Adapt the structure to the actual files in the repository instead of blindly overwriting existing work.

---

# 12. REQUEST ROUTING

Create a lightweight request router.

It should distinguish at minimum:

### HEALTH

Examples:

"Menene alamomin malaria?"

"Yaya ake kare kai daga malaria?"

### NAVIGATION

Examples:

"Ina laboratory?"

"Ina pharmacy?"

"Ina emergency?"

### NEARBY_HOSPITAL

Examples:

"Ina asibiti kusa da ni?"

"Ka nemo min asibiti kusa da ni."

Do not rely solely on exact string matching.

Support reasonable Hausa variations and aliases.

Use deterministic routing where possible.

Avoid unnecessary LLM calls for simple classification tasks.

---

# 13. LLM USE

An external LLM may be used only where it genuinely improves the system.

Do not make the entire application dependent on an LLM.

If an LLM is used:

* use a hosted API;
* use environment variables for credentials;
* create a provider abstraction;
* use structured output where appropriate;
* keep prompts short;
* prevent the model from inventing hospital routes;
* prevent the model from inventing medical facts when retrieval data is available.

For the prototype, a free/low-cost hosted model such as a currently available Groq model can be used for language understanding or response formatting if necessary.

However, deterministic application logic and the provided knowledge/data should remain the source of truth.

---

# 14. API KEY AND CONFIGURATION

Create:

`.env.example`

with placeholders such as:

GROQ_API_KEY=

MODEL_NAME=

Do not commit real credentials.

Read configuration through environment variables.

The application must show a useful error/fallback if the API key is missing.

The text-input version of the application should still be testable without an API key.

---

# 15. ERROR HANDLING

The application must gracefully handle:

* missing API key;
* invalid audio;
* unsupported audio;
* failed transcription;
* empty transcription;
* unknown request;
* unknown hospital destination;
* missing image;
* missing hospital data;
* Google Maps opening failure.

Never expose raw stack traces to normal users.

User-facing errors should be in Hausa.

Developer errors may be logged in English.

---

# 16. PERFORMANCE

Optimize for a small Hugging Face Space.

Do NOT:

* download large ML models;
* load unnecessary frameworks;
* add heavyweight databases;
* add unnecessary packages;
* perform expensive processing on every request.

Speech recognition must use hosted inference.

Static hospital files should be loaded efficiently.

---

# 17. MOBILE-FIRST DESIGN

The web interface will eventually be placed inside a mobile WebView.

Therefore:

* responsive layout;
* large touch targets;
* simple navigation;
* minimal scrolling;
* prominent microphone control;
* readable text;
* lightweight assets;
* no desktop-only interaction.

Do NOT build a native Android application for this task.

The deliverable is the responsive web application.

---

# 18. HUGGING FACE DEPLOYMENT

Make the project directly deployable to a Hugging Face Gradio Space.

Ensure:

* `requirements.txt` is complete;
* `app.py` can be launched by the Space;
* assets use repository-relative paths;
* environment variables are documented;
* no local-only dependencies are required.

If appropriate, include the required Hugging Face Space metadata/configuration.

---

# 19. TESTING

Create lightweight tests for:

* request classification;
* destination matching;
* route retrieval;
* health retrieval;
* nearby-hospital action generation;
* missing-data handling.

At minimum test representative Hausa queries.

Examples:

"Menene alamomin malaria?"

"Ina laboratory?"

"Ina dakin gwaje-gwaje?"

"Ina pharmacy?"

"Ina asibiti kusa da ni?"

Do not create an unnecessarily large test suite.

---

# 20. AUTONOMOUS EXECUTION

You are authorized to:

1. Inspect the entire repository.
2. Inspect all provided files/assets.
3. Determine what already exists.
4. Create missing directories/files.
5. Refactor weak existing code.
6. Implement the complete MVP.
7. Install only necessary dependencies in the development environment.
8. Run tests.
9. Run the application or appropriate validation checks.
10. Fix errors you discover.
11. Improve the UI where necessary.
12. Update README/documentation.
13. Verify Hugging Face deployment requirements.
14. Leave the repository in a clean, coherent state.

Do not stop after creating a plan.

Do not merely provide code snippets.

Actually implement the files.

If something is ambiguous, choose the simplest implementation consistent with this specification and continue.

Only stop when the MVP is implemented and validated or when an external credential/service that cannot be supplied programmatically is genuinely required.

---

# 21. AVOID SCOPE CREEP

Do not add:

* WhatsApp;
* authentication;
* admin dashboards;
* databases requiring servers;
* native mobile apps;
* indoor GPS;
* AR;
* computer vision;
* 3D maps;
* autonomous agents;
* medical diagnosis;
* unnecessary RAG infrastructure.

The priority is a polished, working MVP.

---

# 22. FINAL VALIDATION

Before declaring completion, verify:

1. The application launches.
2. The Gradio interface renders correctly.
3. Text input works without external speech credentials.
4. Audio input is connected to the hosted transcription service.
5. Hausa is the default user-facing language.
6. Health questions retrieve appropriate knowledge.
7. Hospital destinations are matched correctly.
8. Routes and images display correctly.
9. Nearby hospital opens Google Maps.
10. Missing API credentials produce a graceful fallback.
11. No secrets are committed.
12. Requirements are deployable on Hugging Face Spaces.
13. README explains setup and deployment.
14. Tests pass.

Finally, provide a concise completion report listing:

* files created/changed;
* major functionality implemented;
* tests performed;
* required environment variables;
* how to deploy to Hugging Face Spaces;
* any remaining limitation that genuinely cannot be solved without an external credential.

Do not rewrite the whole project description in the completion report.

BUILD THE PRODUCT, NOT JUST THE SCAFFOLD.
