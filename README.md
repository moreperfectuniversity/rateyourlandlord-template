# Rate Your Landlord

Rate Your Landlord is a website template for people to share reviews about landlords and rental properties. It includes example landlord and review data, account screens, and an admin area.

## Run the demo on Windows

These steps start a local demo on your own computer. The demo uses made-up examples; it does not connect to a real database or save changes.

1. **Download Node.js.** Open your web browser, click the address bar, type `https://nodejs.org/`, and press Enter. On the Node.js website, choose the Windows installer for Node.js 20 (LTS means long-term support) and download it.

   **You should see:** a Windows installer file ending in `.msi` in your Downloads folder. The website's buttons may change. **TO CONFIRM:** if the page does not offer Node.js 20, ask the project maintainer which version to use.

2. **Install Node.js.** In File Explorer, open **Downloads**. Double-click the Node.js `.msi` file you downloaded. In the setup window, use the default options and click **Next** through the pages, then **Install** and **Finish**. Approve the Windows permission prompt if it appears.

   **You should see:** the installer reports that setup is complete.

3. **Download the project.** Open [this repository on GitHub](https://github.com/moreperfectuniversity/rateyourlandlord-template). Click the green **Code** button, then click **Download ZIP**.

   **You should see:** a ZIP file for the project in your browser's downloads list.

4. **Unzip the project.** In File Explorer, open **Downloads**. Right-click the repository ZIP file and choose **Extract All**, then click **Extract**. Open the extracted folder.

   **You should see:** project files, including `package.json`. If you see another folder inside instead, open that inner folder.

5. **Open a terminal in the right folder.** In File Explorer, open the extracted project folder and look for the file named `package.json`. If you do not see that file, open the folder inside until you do. Right-click an empty area in the folder that contains `package.json` and choose **Open in Terminal**.

   **You should see:** a PowerShell window whose prompt names the project folder, not just `Downloads`. If the prompt says `C:\Users\YourName\Downloads>`, you are in the wrong folder: close that window and repeat this step from the folder containing `package.json`. If **Open in Terminal** is missing, see [If something goes wrong](docs/replication_guide.md#if-something-goes-wrong).

6. **Check Node.js.** Click after the PowerShell prompt, type `node --version` and press Enter.

   **You should see:** a version number starting with `v20`. npm is the package installer included with Node.js; we will use its Windows command file in the next step.

7. **Install the project packages.** Type exactly `npm.cmd install` and press Enter. Wait for it to finish. Do not type the surrounding backticks or any Markdown markers. `npm.cmd` runs npm without asking PowerShell to run a script.

   **You should see:** npm returns to the prompt without an error. If it reports an error, see [If something goes wrong](docs/replication_guide.md#if-something-goes-wrong).

8. **Start the demo.** Type exactly `npm.cmd run dev` and press Enter. Vite (the tool that runs the site during development) starts a local web server, a small program on your computer that makes the site available in your browser. Leave the terminal open.

   **You should see:** Vite prints a local address, usually `http://localhost:5000`.

9. **View the website.** Open a browser tab, click the address bar, type the exact local address shown in the terminal, and press Enter.

   **You should see:** the Rate Your Landlord website with demo data. To stop it, click the terminal and press **Ctrl+C**.

You do not need to create an account or an environment file to view the demo. Sign-in, registration, and saving information are not available in demo mode. Mapbox provides address suggestions, and Cloudflare Turnstile provides the security check; those features need their own settings, so they may not work in a plain local demo.

## If something goes wrong

- **PowerShell says running scripts is disabled when you type `npm`:** type `npm.cmd install` and `npm.cmd run dev` instead. Do not change PowerShell's execution policy for this project.
- **The prompt says `Downloads>` or `npm` says `package.json` is missing:** the terminal is not in the project folder. Close it, then open a terminal from the extracted folder where `package.json` is visible.
- If `node` is not recognized, close and reopen the terminal. If the command still fails, reinstall Node.js and restart Windows.
- If `npm.cmd install` reports an error, confirm the computer is online and that the terminal opened in the folder containing `package.json`.
- If the browser cannot open the site, check that the terminal is still running and use the exact address it printed.
- If a feature does not work in the demo, it may need a real backend or one of the external services described above.

See the [setup guide's troubleshooting steps](docs/replication_guide.md#if-something-goes-wrong) for more help.

## Make your own website

The [setup guide](docs/replication_guide.md) walks through making your own GitHub copy, preparing Supabase, publishing with Cloudflare Pages, and connecting a domain. The current project can be published as a demo, but it is **not ready for real users or data**: `init.sql` has apparent SQL errors, and the Turnstile widget is not validated on a server. Do not connect real user information until a developer has repaired and reviewed those parts.

## Project guides

- [Setup guide](docs/replication_guide.md): Windows demo instructions, Supabase and Cloudflare setup, domain connection, troubleshooting, and current production blockers.
- [How the project is organized](docs/architecture.md): a plain-English overview of the website's main parts.

## For maintainers

`npm run build` creates production files in the `dist/` folder. It does not publish or host the website. Before inviting people to deploy or contribute, maintainers should fix and verify the database setup and automated-test workflow, and add a license that matches the project's intended use. The project owner should choose the license; none is included yet.
