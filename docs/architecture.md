## How the project works

This project is a website built with React and TypeScript. Vite runs the site while it is being developed and creates the files used for a production build. The site is designed as a single-page application: its different screens are shown at different web addresses without loading a separate page from the server each time.

When the site starts, it chooses one of two ways to get data:

- **Demo mode:** used when either Supabase setting is missing. The site shows example landlords and reviews and uses mock account services. This is for viewing the interface, not for managing real users or keeping data.
- **Supabase mode:** used when both `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` are set. The website then tries to use the configured Supabase project. This connection exists in the code, but the database setup and security have not been verified for public use; see the [setup guide](replication_guide.md).

## Where to find things

- `App.tsx` connects the screens, navigation, and data services.
- `pages/` contains the website's screens, such as landlord details, login, and registration.
- `components/` contains reusable parts used by those screens, such as forms and cards.
- `context/` shares account, data, and service information with the screens.
- `services/` contains the code that supplies data: mock services for the demo and Supabase services for a configured backend.
- `types.ts` describes the shapes of landlord and review records used by the application.
- `init.sql` is intended to prepare a Supabase database. It is not currently verified or safe to use as-is; see the warning in the [setup guide](replication_guide.md).
- `tests/` contains test files, but the current package scripts and GitHub Actions workflow do not agree on how to run them. Do not rely on automated test results until maintainers fix and verify that setup.

## External services

Some features rely on settings from services outside this repository:

- Supabase provides the real database and account system.
- Mapbox provides address suggestions.
- Cloudflare Turnstile provides the security-check widget used by some forms.

The local demo can be opened without setting these up, but real-user features need their configuration and a tested deployment plan.
