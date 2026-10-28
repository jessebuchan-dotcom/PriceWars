# Build/test status

## Verified in the authoring environment

- Source files were written and exported, including the React UI, Node server, game engine, tests, deployment files and documentation.
- Every TypeScript and TSX source file passed a TypeScript transpiler **syntax** check.
- The standalone game engine passed **12 Node.js unit tests** using a temporary TypeScript-to-JavaScript transpilation harness. Tests cover financial conservation, price and promotion effects, capacity restrictions, ordering, shocks, event duration, invalid values, paused periods and strategic optimum.

## Not yet verified

- Dependency installation (`npm install`) was unavailable in the authoring environment because the npm registry DNS/network could not be reached.
- As a result, the full project has **not yet** completed a dependency-resolved TypeScript check, Vite build, multi-device browser test, or real Cloud Run/Firestore test.
- Cloud Run deployment requires configuring the school's/project owner's credentials, database, service account and teacher setup PIN. No deployment was attempted.

## Required next steps

Once a Node environment with npm access is available, run:

```bash
npm install
npm run check
npm test
npm run build
npm run dev
```

Resolve any package/version or type errors reported by these commands, then follow CLASSROOM_TEST_CHECKLIST.md after deployment. Do not consider this a verified production installation until those steps are complete.
