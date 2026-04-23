<wizard-report>
# PostHog post-wizard report

The wizard has completed a deep integration of PostHog analytics into the Recurrly subscription tracker app (Expo / React Native). Here is a summary of all changes made:

- **`lib/posthog.ts`** *(new)* — PostHog singleton client, configured via `expo-constants` extras. Reads `POSTHOG_PROJECT_TOKEN` and `POSTHOG_HOST` from `app.config.js` extras at build time. Disables itself gracefully if the token is missing.
- **`app.config.js`** *(new)* — Replaces `app.json` as the Expo config entry point. Extends `app.json` and adds `extra.posthogProjectToken` / `extra.posthogHost` from environment variables.
- **`.env`** *(updated)* — `POSTHOG_PROJECT_TOKEN` and `POSTHOG_HOST` written; `.gitignore` coverage ensured.
- **`app/_layout.tsx`** *(updated)* — Wrapped the app in `PostHogProvider` (outermost, around `ClerkProvider`). Added manual screen tracking with `posthog.screen()` via `usePathname` + `useGlobalSearchParams` inside a `useEffect`.
- **`app/(auth)/sign-in.tsx`** *(updated)* — Activated `usePostHog`, `posthog.identify()` on successful sign-in (both password and MFA paths), `posthog.capture('user_signed_in')`, and `posthog.capture('user_sign_in_failed')`.
- **`app/(auth)/sign-up.tsx`** *(updated)* — Activated `usePostHog`, `posthog.identify()` + `posthog.capture('user_signed_up')` on successful email verification, and `posthog.capture('user_sign_up_failed')` on error.
- **`app/(tabs)/settings.tsx`** *(updated)* — Activated `usePostHog`, `posthog.capture('user_signed_out')` before sign-out, and `posthog.reset()` after successful sign-out to clear the identity.
- **`app/(tabs)/index.tsx`** *(updated)* — Added `usePostHog`, captures `subscription_card_expanded` / `subscription_card_collapsed` with `subscription_name` and `subscription_id` properties on card press.
- **`app/subscriptions/[id].tsx`** *(updated)* — Added `usePostHog`, captures `subscription_details_viewed` with `subscription_id` on mount.
- **`app/onboarding.tsx`** *(updated)* — Added `usePostHog`, captures `onboarding_viewed` on mount (top of acquisition funnel).

## Events

| Event | Description | File |
|---|---|---|
| `user_signed_up` | User successfully completed sign-up and verified email | `app/(auth)/sign-up.tsx` |
| `user_sign_up_failed` | Sign-up attempt failed with an error | `app/(auth)/sign-up.tsx` |
| `user_signed_in` | User successfully signed in | `app/(auth)/sign-in.tsx` |
| `user_sign_in_failed` | Sign-in attempt failed with an error | `app/(auth)/sign-in.tsx` |
| `user_signed_out` | User signed out of their account | `app/(tabs)/settings.tsx` |
| `subscription_card_expanded` | User expanded a subscription card to view details | `app/(tabs)/index.tsx` |
| `subscription_card_collapsed` | User collapsed a subscription card | `app/(tabs)/index.tsx` |
| `subscription_details_viewed` | User navigated to the subscription details screen | `app/subscriptions/[id].tsx` |
| `onboarding_viewed` | User viewed the onboarding screen (acquisition funnel top) | `app/onboarding.tsx` |

## Next steps

We've built some insights and a dashboard for you to keep an eye on user behavior, based on the events we just instrumented:

- **Dashboard — Analytics basics**: https://us.posthog.com/project/394429/dashboard/1502731
- **Sign-up conversion funnel**: https://us.posthog.com/project/394429/insights/v1PcNtLB
- **Sign-in success vs failure**: https://us.posthog.com/project/394429/insights/Wc6RnL9n
- **New user sign-ups over time**: https://us.posthog.com/project/394429/insights/7ckT1sEv
- **Subscription card engagement**: https://us.posthog.com/project/394429/insights/uQQ7kUZ9
- **User churn — sign-outs over time**: https://us.posthog.com/project/394429/insights/1sQNKrS2

### Agent skill

We've left an agent skill folder in your project. You can use this context for further agent development when using Claude Code. This will help ensure the model provides the most up-to-date approaches for integrating PostHog.

</wizard-report>
