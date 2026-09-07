# State of Health — Blitzy Workspace

Parent workspace that groups the two repositories behind **State of Health** (SoH), a
fitness / nutrition tracking app. Each project stays in its own repository with its own
history; this repo references them as Git submodules so tooling can see the full system
(mobile client + API) at once and open pull requests against each repo separately.

## Layout

| Path      | Repository | Default branch | What it is |
|-----------|------------|----------------|------------|
| `mobile/` | [state-of-health-tracker](https://github.com/Kennygunderman/state-of-health-tracker) | `master` | React Native (Expo 57, New Architecture) iOS/Android app |
| `backend/`| [state-of-health-be](https://github.com/Kennygunderman/state-of-health-be) | `main` | TypeScript Express API + Prisma/Postgres |

## Getting the code

```bash
git clone --recurse-submodules https://github.com/Kennygunderman/state-of-health-blitzy.git

# already cloned without submodules:
git submodule update --init --recursive

# pull the latest commit of each project's default branch:
git submodule update --remote --merge
```

## mobile/ — State of Health tracker app

- **Stack:** Expo / React Native, TypeScript, React Navigation (native stack + bottom tabs)
- **Data:** TanStack Query (with AsyncStorage persistence) against the `backend/` API
- **Auth:** Firebase Auth (Google / Apple sign-in), Firebase Remote Config + Crashlytics
- **Native:** HealthKit integration, Expo dev client, `patch-package` postinstall patches
- **Source layout:** `src/{screens,components,queries,service,store,hooks,navigation,styles,data}`

```bash
cd mobile
npm install
npm run ios        # or: npm run android
npm test           # jest
npm run lint
```

## backend/ — State of Health API

- **Stack:** Node + Express 4, TypeScript, Prisma ORM against Postgres
- **Auth:** Firebase Admin verifies the ID tokens issued to the mobile client
- **Deploy:** Dockerfile; runs as a container behind Coolify
- **Source layout:** `src/{routes,controllers,services,middleware,prisma,types,utils}`

```bash
cd backend
npm install
npx prisma generate
npm run dev        # ts-node-dev on src/server.ts
npm run typecheck
npm run build && npm start
```

## How the two talk to each other

The mobile app authenticates with Firebase, then sends the Firebase ID token as a bearer
token on every request to the Express API. The API verifies the token with Firebase Admin,
resolves the user, and reads/writes their data in Postgres through Prisma. Any change to a
request or response shape therefore spans both repos: the route/controller/service in
`backend/src`, and the matching query/service module in `mobile/src`.

## Working in this repo

- Commit changes **inside** `mobile/` or `backend/` — those land in their own repositories.
- The parent repo only stores the commit pointer for each submodule; bumping a pointer is a
  separate commit here and is optional.
