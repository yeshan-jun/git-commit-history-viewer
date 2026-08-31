# Git Commit History Viewer

## Project Introduction

Git Commit History Viewer is a browser-based explorer for the history of public GitHub repositories. It turns a repository address into a locally stored stream of commit metadata, then gives you a calm workspace for looking at change rhythm, contributor activity, and the shape of a project's timeline. The application is designed for repository discovery, onboarding, code review preparation, and anyone who wants a quick visual answer to the question: how has this project evolved?

## What It Does

Paste a GitHub repository URL or an `owner/repository` value. The app checks the repository, finds its default branch, and requests the commit list in pages of up to 100 records. Each successful page is written to IndexedDB immediately. You can watch the count grow while the scan is running, cancel a long scan, and resume it later from the browser. The application stores lightweight commit metadata rather than source files or patches: full SHA, short SHA, commit headline, author information, timestamps, parent SHAs, and a link back to GitHub.

When the scan is complete, all analysis is local. Reverse the order from newest-first to oldest-first, search commit headlines, narrow the visible range by author or date, and inspect the results in a searchable table. The timeline shows commit positions across the selected period. Activity bars summarize commits by day, while the contributor view ranks authors by the number of visible commits. Exports use the current filtered and sorted view, so JSON and CSV files can be kept with a research note or used in another local workflow.

## How To Use

1. Open the tool and enter a public GitHub repository such as `facebook/react` or a full GitHub URL.
2. Select **Load history** to start a sequential scan of the default branch.
3. Watch the progress card for pages saved, records downloaded, and any API rate-limit information.
4. Use **Pause scan** if you want to stop. The downloaded pages remain available and the next visit can resume the snapshot.
5. Once data is available, change sort direction, use the filters, and explore the timeline and summary charts.
6. Select a commit link to open the original GitHub page in a new tab. Use the export controls to save the current local view.

The tool also lists local snapshots. Reopen a completed or partial snapshot to work without requesting the same records again, or delete a snapshot when it is no longer useful.

## Supported Formats

Repository input accepts `owner/repository`, `https://github.com/owner/repository`, and the same URL with a `.git` suffix. The first release reads public repositories and the history reachable from the repository's default branch. JSON exports contain normalized commit records. CSV exports contain the SHA, short SHA, headline, author, login, authored date, committed date, GitHub URL, and parent SHAs. The app does not download diffs, changed-file lists, blobs, or other source content.

## Technical Details

The interface is built with React, TypeScript, and Vite. GitHub's REST API is called directly from the browser, using sequential pagination and the public repository endpoint. The application uses the response `Link` header to find the next page and reads rate-limit headers for progress feedback. IndexedDB holds snapshot metadata and individual commit records, which keeps the UI responsive while a larger history is being downloaded. Sorting, filters, aggregation, SVG charts, and export serialization are pure local operations. No application backend, account system, analytics service, or bundled GitHub token is used.

## Project Structure

The `src/domain` directory contains types, input parsing, commit normalization, local sorting, filtering, aggregation, and export helpers. `src/api` contains the GitHub client and sequential scan coordinator. `src/storage` owns the IndexedDB repository. `src/components` contains the accessible UI and SVG visualizations, while `src/app` coordinates scan state and page composition. Static PWA assets live in `public`, and Vite emits the deployable site into `dist`.

## Deployment

Install Node.js dependencies with `npm install`. Use `npm run dev` for local development, `npm test -- --run` for the test suite, and `npm run build` to create the production bundle. GitHub Pages can serve the generated `dist` directory from the `main` branch or through a Pages workflow. The production bundle uses relative asset paths, so the same `dist` directory works from a local web-server root and from `/git-commit-history-viewer/` on GitHub Pages. The manifest and service worker resolve against the page's actual scope.

## Repository

The source repository is [git-commit-history-viewer](https://github.com/yeshan-jun/git-commit-history-viewer). The published site is [yeshan-jun.github.io/git-commit-history-viewer](https://yeshan-jun.github.io/git-commit-history-viewer/).

## Privacy

Repository addresses and normalized commit metadata are stored in the browser's IndexedDB on the device where the tool is used. The application does not upload them to a project-owned server. Public repository requests go directly to GitHub. Clearing the browser's site data removes local snapshots. Because public anonymous API requests have a shared rate limit, a very large history may need to be resumed later.

## License

This project is released under the MIT License.

## Reference

The implementation follows the public GitHub REST API documentation for commit listing, pagination, CORS, and rate limits. See the [commit endpoint reference](https://docs.github.com/en/rest/commits/commits), [pagination guide](https://docs.github.com/en/rest/using-the-rest-api/using-pagination-in-the-rest-api), and [REST API rate-limit guide](https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api) for the service behavior that shapes the client-side scan.
