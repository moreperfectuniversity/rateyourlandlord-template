# Make your own Rate Your Landlord website

This guide explains how to make a copy of the project, prepare its services, publish it with Cloudflare Pages, and connect a domain you own.

**Important: the current project is not ready for real users or real information.** You can publish a demo website with sample data, but the supplied database setup has SQL errors and the security checks have not been fully implemented. The guide marks the steps that need a developer before you use the site as a real service. Do not put real user information into a demo or an unreviewed setup.

Dashboard layouts and button names may change. If your screen differs from these steps, use the linked official product guide or ask the project maintainer rather than guessing.

## What you need

- A Windows computer and an internet connection.
- A GitHub account, to keep your own copy of the project.
- A Cloudflare account, to publish the website. Cloudflare Pages hosts the built website.
- A Supabase account, to provide the database and user accounts. Supabase is **not ready to connect** until a developer fixes and reviews `init.sql`.
- A domain name you own, if you want an address such as `mywebsite.com`. You can buy one through a domain registrar, a company that sells domain names, or use a domain you already own.
- Node.js 20 to run the project on your computer. npm is included with Node.js and installs the project's packages.

## 1. Make your own copy on GitHub

1. In your browser, open the [project repository](https://github.com/moreperfectuniversity/rateyourlandlord-template).
2. Sign in to GitHub. Click **Fork** near the top of the repository.
3. On the fork page, leave the suggested owner and repository name or choose your own, then click **Create fork**.

**You should see:** your own repository page, with your account name near the repository name. This is your copy; changes to the original repository will not automatically become your changes.

## 2. Run the demo on Windows

This first runs the site on your computer with made-up examples. It does not connect to a real database or save changes.

1. Open `https://nodejs.org/` in your browser. Download the Windows installer (`.msi`) for Node.js 20, then open the downloaded file. Use the installer's default choices and click **Next**, **Install**, and **Finish** as prompted.

   **You should see:** the installer reports that Node.js setup is complete. **TO CONFIRM:** the download page layout and offered versions can change. If it does not offer Node.js 20, check with the project maintainer which supported version to use.

2. Open your GitHub copy. Click **Code**, then **Download ZIP**. In File Explorer, open **Downloads**, right-click the downloaded ZIP, choose **Extract All**, and click **Extract**.

   **You should see:** a new folder containing the project files. Open it until you can see `package.json`.

3. In that folder, right-click an empty area and choose **Open in Terminal**.

   **You should see:** a PowerShell window with the project folder in its prompt. The prompt should not end in `Downloads>`.

4. Click after the PowerShell prompt, type `node --version`, and press Enter.

   **You should see:** a version number starting with `v20`.

5. Type exactly `npm.cmd install` and press Enter. Wait for it to finish and the prompt to return.

   **You should see:** npm finishes installing packages. Do not type the backticks or other formatting marks around commands in written instructions.

6. Type exactly `npm.cmd run dev` and press Enter. Leave the terminal window open.

   **You should see:** Vite (the development tool that runs the site) prints a local address, usually `http://localhost:5000`.

7. Open that address in your browser.

   **You should see:** the website with demo data. To stop it, click the terminal and press **Ctrl+C**.

## 3. Prepare Supabase for real data — blocked until the project is fixed

Supabase supplies the database and account system. You can create your own Supabase project, but **do not connect it to this application yet**:

- **The reviews table contains invalid SQL.** In `init.sql`, the line `votes` appears by itself between the `beds` and `comment` columns. PostgreSQL needs a column name and data type there. A developer must decide whether `votes` is needed, define it correctly, or remove the line.
- **Both seed-data inserts have an extra comma.** In the landlord and review `INSERT ... VALUES` statements near the end of `init.sql`, the final example row has a comma immediately before `ON CONFLICT`. Remove those commas so PostgreSQL accepts the statements.
- **The bedroom field name does not match the app.** The SQL defines `beds`, but the app reads and writes `bedrooms`. Choose one name and make the database and app agree, then test saving and displaying a review with a bedroom count.
- **The file says it creates storage, but does not.** The opening comment promises a storage bucket and policies, but there are no storage setup statements in the file. Either add and test the required storage setup or correct the comment and document any separate steps. Do not assume file uploads are configured.
- **The admin list exposes email addresses.** The policy `Anyone can view admins` permits public reads of the `admins` table if the role has table access. That table contains admin email addresses. A developer must remove broad public access and provide a safer admin check.
- **Admin review actions may be blocked by row-level security.** The script defines an update policy for a user's own reviews, but not an admin update policy for moderation. Add a narrowly scoped policy for admin moderation and test approving, verifying, deleting, and restoring records.
- **The policy setup is not safe to run twice.** The `CREATE POLICY` statements do not handle policies that already exist. Running the script again can fail. Make it safely repeatable or document and enforce that it only runs once on a new test database.

**A developer must repair and test the database setup and security before you continue with real data.** Do not paste the current `init.sql` into a production database. Once the project maintainer confirms a corrected, tested setup script is available, follow that script's reviewed instructions in a new Supabase test project first.

### How the developer should verify the SQL fixes

1. Use a **separate, disposable Supabase test project** with no real user information.
2. Run the corrected setup there and confirm that it finishes without SQL errors and creates the expected tables, indexes, functions, policies, and sample records.
3. Confirm that review bedroom counts save and display correctly, and that any advertised file upload feature has a real, tested storage bucket and access rules.
4. Test database access as a signed-out visitor, a regular signed-in user, and an administrator. Confirm that each can only read or change the records intended for that role, and that admin emails are not publicly readable.
5. Run the setup a second time if it is documented as repeatable. Otherwise, clearly document that it must only be run once on a fresh project.
6. Test the website's registration, email verification, sign-in, password reset, review submission and editing, and moderation flows against test data before documenting the setup as ready.

Only after these checks pass should the maintainer replace this warning with instructions for the verified setup. If the code and database schema disagree, fix both together rather than changing the database alone.

When the database setup is ready, the general connection steps are:

1. Sign in to Supabase and create a new project for your website. Choose a secure database password and keep it private.
2. In the project dashboard, open **Connect** or the API settings and copy the Project URL and the public `anon`/publishable key. The app currently expects the setting named `VITE_SUPABASE_ANON_KEY`.
3. Never use or publish the `service_role` key. That key has powerful server-side access and must not go in a browser app.
4. In the project folder on your computer, create a file named `.env.local` (not `.env.local.txt`) and add the two lines below, replacing the sample values with your own:

   ```text
   VITE_SUPABASE_URL=https://your-project.supabase.co
   VITE_SUPABASE_ANON_KEY=your-public-anon-key
   ```

5. Save the file. Run `npm.cmd run dev` again and check that the yellow demo-mode message is gone.

**You should see:** the app attempts to connect to your Supabase project. This is not proof that the database is correctly or securely configured; that depends on the repaired schema and security policies.

The `anon` key is intended for use by the website, but database access must be protected by correctly configured row-level security (rules that limit which records users may read or change). Treat the database password and any service-role key as private.

## 4. Set up the website's other services

### Mapbox address suggestions (optional)

The address fields use Mapbox Search. Without a Mapbox token, address suggestions will not work.

1. Create a Mapbox account and a public access token in its account dashboard.
2. Restrict the token to the website domains you control, following [Mapbox's token security instructions](https://docs.mapbox.com/accounts/guides/tokens/).
3. For local testing, add `VITE_MAPBOX_TOKEN=your-public-token` to `.env.local`. For the published site, add the same variable and token in its Cloudflare Pages environment settings, as described in step 5.

**You should see:** address suggestions when entering an address on a page that uses the Mapbox address field.

### Cloudflare Turnstile checks — server validation is missing

The registration and submission forms display a Turnstile widget, which is Cloudflare's anti-bot check. The current application does not send the widget's token to a server for validation. Cloudflare requires that server-side validation to make the check meaningful.

You may create a Turnstile widget and use its **site key** as `VITE_TURNSTILE_SITE_KEY`, but **do not treat the current widget as protection against bots**. A developer must implement and test server-side token validation using the Turnstile secret before this can be relied on. Never put the Turnstile secret in a `VITE_` variable or in browser code.

See Cloudflare's [Turnstile setup guide](https://developers.cloudflare.com/turnstile/get-started/) for the distinction between a public site key and private secret key.

## 5. Publish a demo copy with Cloudflare Pages

Cloudflare Pages can build the project from your GitHub copy and publish the files. Until Supabase is repaired and connected, this publishes the **demo mode** with sample data, not a functioning real-user service.

1. Sign in to Cloudflare and open **Workers & Pages**.
2. Choose **Create application**, then **Pages**, then **Connect to Git**. Connect your GitHub account if asked, and grant Cloudflare permission to access your fork.
3. Select your forked repository and begin setup.
4. Set the build command to `npm run build` and the build output directory to `dist`. Leave the root directory at its default because `package.json` is in the repository root.
5. Choose the production branch for your fork, usually `main`, then choose **Save and Deploy**.

   **You should see:** Cloudflare installs the project packages, runs the build, and shows a `*.pages.dev` address when the deployment succeeds. Open that address to see the published website.

The settings above match this repository's `package.json` and Cloudflare's [React (Vite) build settings](https://developers.cloudflare.com/pages/configuration/build-configuration/). Git-connected deployments rebuild when changes are pushed to the selected branch.

### Add the website's settings to Cloudflare

Once Supabase and any optional service settings have been prepared and reviewed:

1. In Cloudflare, open **Workers & Pages**, select your Pages project, then open **Settings** and **Environment variables**.
2. Add each setting the app needs: `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` for Supabase; `VITE_MAPBOX_TOKEN` for address suggestions; and `VITE_TURNSTILE_SITE_KEY` for the widget.
3. Save the settings, then start a new deployment from the Pages project.

**You should see:** the new deployment completes with the configured values. Values beginning with `VITE_` are included in the browser build. Only use public client keys there; never add database passwords, Supabase `service_role` keys, or Turnstile secret keys.

## 6. Connect a domain you own

First deploy the site and confirm that its `*.pages.dev` address works. Then decide whether the address you want is the main domain (for example, `example.com`) or a subdomain (for example, `www.example.com`).

1. Buy a domain from a registrar, or use a domain you already own. Keep access to the account where that domain's DNS settings are managed.
2. In Cloudflare, open **Workers & Pages**, select your Pages project, and choose **Custom domains** > **Set up a domain**. Enter the domain or subdomain you want and continue.
3. Follow the dashboard's instructions to connect the domain.

For a **main domain** such as `example.com`, Cloudflare Pages requires that the domain be added to the same Cloudflare account as a DNS zone. If another company manages it, Cloudflare will show Cloudflare nameservers to enter at your domain registrar. Changing nameservers affects all DNS for the domain, not only this website. If you use email or other services on the domain, have the owner or a knowledgeable person confirm those DNS records are preserved before changing nameservers.

For a **subdomain** such as `www.example.com`, you can keep DNS at your current provider and create the CNAME record Cloudflare Pages instructs you to add. Add the domain through the Pages **Custom domains** screen first; a DNS record by itself is not enough to attach the domain to a Pages project.

Cloudflare's [custom domain instructions](https://developers.cloudflare.com/pages/configuration/custom-domains/) cover the current dashboard flow and DNS requirements.

**You should see:** the custom domain is marked active in Pages and opens the same site as the `pages.dev` address. DNS and HTTPS certificate setup can take time. If it does not activate, use the error shown in Cloudflare's dashboard rather than guessing at DNS changes.

## 7. Configure sign-in redirects and test before sharing

Once your own domain is active:

1. In Supabase, open **Authentication** > **URL Configuration**.
2. Set the **Site URL** to the main address visitors will use, including `https://` (for example, `https://example.com`).
3. Add the exact production redirect addresses for your site's `/verify-email` and `/reset-password` pages to the allowed redirect URLs. Add your local development address, `http://localhost:5000/**`, if you also test sign-in locally. Use only domains you control.
4. Save the settings. Test account creation, email verification, sign-in, password reset, reading records, and submitting records using test accounts and test data only.

The app redirects email verification to `/verify-email` and password reset to `/reset-password`. Supabase explains these settings in its [redirect URL guide](https://supabase.com/docs/guides/auth/redirect-urls).

**Do not invite real users until** a developer has repaired and reviewed the SQL, verified the database access rules, implemented server-side Turnstile validation, and tested the complete account and data flows on the published domain.

## If something goes wrong

- **PowerShell says running scripts is disabled:** use `npm.cmd install` and `npm.cmd run dev`, not `npm`. Do not change PowerShell's execution policy for this project.
- **The prompt says `Downloads>` or `package.json` is missing:** close the terminal and open it again from the extracted folder where `package.json` is visible.
- **npm reports security vulnerabilities after installing:** packages may still have installed. Do not run `npm audit fix` automatically; it can change package versions. Send the audit summary to the project maintainer so they can review and update dependencies safely.
- **npm warns that an install script, such as `esbuild`, is not approved:** npm is blocking a package setup script. Do not approve it unless the project maintainer confirms it is expected and safe. If the site will not start, send the full warning and error to the maintainer.
- **The site shows a demo-mode message after you added Supabase settings:** check that both setting names and values are spelled correctly. Restart the local dev server after changing `.env.local`. For Cloudflare, add the settings in the Pages project and start a new deployment.
- **Cloudflare's build fails:** open the failed deployment's build log. Confirm the build command is `npm run build` and the output directory is `dist`. Copy the error text and ask the project maintainer for help; do not share private keys.
- **Your domain does not open:** check the Pages **Custom domains** status and the DNS instructions Cloudflare gives for that exact domain. DNS changes can take time. Do not delete existing DNS records without checking whether they are used for email or other services.
- **Accounts or forms do not work:** the current SQL setup is not ready, and Turnstile has no server-side validation in this project. Use demo mode or test data only until a developer has fixed and reviewed those parts.

## Project readiness items

- `init.sql` must be repaired, tested in a separate Supabase project, and reviewed for safe access rules.
- The repository's GitHub Actions workflow calls `npm run test:run`, but `package.json` does not currently define that command. A developer should fix the mismatch and verify the checks pass.
- The owner should choose and add an appropriate `LICENSE` before inviting others to reuse or contribute code.
