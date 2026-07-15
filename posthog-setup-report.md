# PostHog post-wizard report

The wizard has completed a deep integration of PostHog into the Skild TanStack Start application. The following changes were made:

- **Installed** `posthog-js` (client-side SDK) via npm.
- **Set up environment variables** `VITE_PUBLIC_POSTHOG_PROJECT_TOKEN` and `VITE_PUBLIC_POSTHOG_HOST` in `.env`.
- **Added `PostHogProvider`** to `src/routes/__root.tsx`, wrapping the entire app with autocapture, session recording, and exception capture enabled.
- **Added user identification** via a `PostHogUserIdentifier` component in the root that uses Clerk's `useUser()` hook to call `posthog.identify()` when a user signs in (and `posthog.reset()` when signed out).
- **Instrumented 5 events** across 4 components/routes.
- **Configured a reverse proxy** in `vite.config.ts` so PostHog events route through `/ingest` during local development.

| Event Name | Description | File |
|---|---|---|
| `skill_install_command_copied` | User copies a skill's install command from the registry card. | `src/components/SkillCard.tsx` |
| `browse_registry_clicked` | User clicks the Browse Registry CTA on the home page hero section. | `src/routes/index.tsx` |
| `create_skill_clicked` | User clicks the Create Skill CTA on the home page hero section. | `src/routes/index.tsx` |
| `sign_in_viewed` | User views the sign-in page, marking the top of the authentication funnel. | `src/routes/__auth/sign-in.$.tsx` |
| `sign_up_viewed` | User views the sign-up page, marking the top of the registration funnel. | `src/routes/__auth/sign-up.$.tsx` |

## Next steps

We've built some insights and a dashboard for you to keep an eye on user behavior, based on the events we just instrumented:

- **Dashboard**: [Analytics basics (wizard)](https://us.posthog.com/project/513388/dashboard/1851783)
- **Auth funnel** (sign-in page → identify): [Auth funnel: Sign-in & Sign-up pages (wizard)](https://us.posthog.com/project/513388/insights/o6XU0Oq5)
- **Skill install commands copied**: [Skill install commands copied (wizard)](https://us.posthog.com/project/513388/insights/k8MpK53U)
- **Homepage CTA clicks**: [Registry CTA clicks (wizard)](https://us.posthog.com/project/513388/insights/vKJRP7YJ)
- **Sign-in vs Sign-up trends**: [Sign-up vs Sign-in page views (wizard)](https://us.posthog.com/project/513388/insights/0kM3WYm6)
- **Registry engagement funnel** (browse → copy): [Registry engagement funnel (wizard)](https://us.posthog.com/project/513388/insights/cbyS5sKJ)

## Verify before merging

- [ ] Run a full production build (the wizard only verified the files it touched) and fix any lint or type errors introduced by the generated code.
- [ ] Run the test suite — call sites that were rewritten or instrumented may need updated mocks or fixtures.
- [ ] Add the exact PostHog env var names to `.env.example` and any monorepo/bootstrap scripts so collaborators know what to set: `VITE_PUBLIC_POSTHOG_PROJECT_TOKEN` and `VITE_PUBLIC_POSTHOG_HOST`.
- [ ] Wire source-map upload (`posthog-cli sourcemap` or your bundler's upload step) into CI so production stack traces de-minify.
- [ ] Confirm the returning-visitor path also calls `identify` — the `PostHogUserIdentifier` component runs on every page load using Clerk's `useUser()`, so returning sessions should be covered, but verify by signing in, refreshing, and checking that the same PostHog person receives both sessions' events.

### Agent skill

We've left an agent skill folder in your project. You can use this context for further agent development when using Claude Code. This will help ensure the model provides the most up-to-date approaches for integrating PostHog.
