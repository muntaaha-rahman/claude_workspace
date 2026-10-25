---
name: redux-rtk-conventions
description: Use when working on the frontend-to-backend API layer in a project using Redux Toolkit / RTK Query — adding or editing API slices, store wiring, auth headers, or cache invalidation. Applies to both Vite and Next.js projects that use RTK.
---

# Redux Toolkit / RTK Query conventions

Grounded in the actual pattern used across these frontends (e.g. `aargon_frontend/src/api/`, `aargon_frontend/src/store/store.ts`).

## One `createApi` per domain, not one giant API

Each resource gets its own API slice file (`authApi.ts`, `clientsApi.ts`, `invoiceApi.ts`, ...), each with its own `reducerPath`. Don't merge unrelated resources into one `createApi` call.

In `store.ts`, every slice is registered individually — both its reducer and its middleware:
```ts
reducer: { [authApi.reducerPath]: authApi.reducer, [clientsApi.reducerPath]: clientsApi.reducer, ... },
middleware: (getDefaultMiddleware) => getDefaultMiddleware().concat(authApi.middleware).concat(clientsApi.middleware)...
```
Adding a new API slice means adding both lines here, not just creating the file.

## Auth header pattern

Every slice's `fetchBaseQuery` reads the bearer token from `localStorage` in `prepareHeaders`:
```ts
prepareHeaders: (headers) => {
  const token = localStorage.getItem('token');
  if (token) headers.set('authorization', `Bearer ${token}`);
  return headers;
},
```
This is currently copy-pasted into every API slice file rather than shared. Match this pattern when adding a new slice — don't silently refactor it into a shared helper unless asked to, since that's a cross-cutting change affecting every existing slice.

## Request bodies: camelCase → snake_case by hand

The frontend's TypeScript interfaces use camelCase (`contactPerson`, `createdAt`), but request bodies sent to the FastAPI backend are manually converted to snake_case inside the `query` function:
```ts
body: { name: clientData.name, contact_person: clientData.contactPerson, ... }
```
There's no automatic case transformer — every mutation/query that sends a body does this conversion explicitly. Do the same for new endpoints; don't assume the backend accepts camelCase.

## Cache invalidation

Use `tagTypes` + `providesTags` (on queries) / `invalidatesTags` (on mutations) for resources that need cache invalidation after a mutation (see `clientsApi`'s `Client` tag). Endpoints that don't need cross-query invalidation (e.g. `authApi`'s login/logout) skip tags entirely — don't add tags where nothing needs invalidating.

## Hooks export

Each slice exports its generated hooks at the bottom: `export const { useLoginMutation, useGetMeQuery } = authApi;`. Consumers import hooks directly from the slice file, not from a central re-export.

## Base URL / config — known gap, don't copy it into new code without flagging

In the Vite projects, `SERVER_CONFIG.BASE_URL` is currently **hardcoded** in `src/api/config.ts` (not read from `import.meta.env`), sometimes with a stale commented-out alternative left in the file. The Next.js project does this correctly via `NEXT_PUBLIC_API_URL`. When adding a new Vite project's API config or touching `config.ts`, prefer `import.meta.env.VITE_API_URL` and mention the gap rather than extending the hardcoded pattern further — but don't silently rewrite an existing project's config as a side effect of an unrelated task.
