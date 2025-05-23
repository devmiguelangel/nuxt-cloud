# Nuxt Minimal Starter

Look at the [Nuxt documentation](https://nuxt.com/docs/getting-started/introduction) to learn more.

## Setup

Make sure to install dependencies:

```bash
# npm
npm install

# pnpm
pnpm install

# yarn
yarn install

# bun
bun install
```

## Development Server

Start the development server on `http://localhost:3000`:

```bash
# npm
npm run dev

# pnpm
pnpm dev

# yarn
yarn dev

# bun
bun run dev
```

## Production

Build the application for production:

```bash
# npm
npm run build

# pnpm
pnpm build

# yarn
yarn build

# bun
bun run build
```

Locally preview production build:

```bash
# npm
npm run preview

# pnpm
pnpm preview

# yarn
yarn preview

# bun
bun run preview
```

Check out the [deployment documentation](https://nuxt.com/docs/getting-started/deployment) for more information.

## Deploying to Cloudflare Pages

The following steps are required to deploy a Nuxt application to Cloudflare Pages.

- A Cloudflare account
- A Cloudflare Pages project
- A Nuxt application
- A GitHub repository
- A GitHub Actions workflow
- A Cloudflare API token

### Steps

1. Create a new Cloudflare Pages project
2. Create a new GitHub Actions workflow
3. Add the following to the workflow:

  See the following example of a GitHub Actions workflow that deploys a Nuxt application to Cloudflare Pages.

```yaml
name: CI

on:
  pull_request:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      deployments: write
      pull-requests: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Bun
        uses: oven-sh/setup-bun@v2
        with:
          bun-version: latest
      - name: Install dependencies
        run: bun install
      - name: Build
        run: bun run build

      - name: Branch name
        run: echo ${{ env.BRANCH_NAME }}

      - name: Deploy
        uses: cloudflare/wrangler-action@v3
        id: deploy
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          accountId: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
          command: pages deploy dist/ --project-name=mmapp
      - name: Print Deployment URL
        env:
          DEPLOYMENT_URL: ${{ steps.deploy.outputs.deployment-url }}
        run: echo $DEPLOYMENT_URL

      - name: Comment on PR
        env:
          DEPLOYMENT_URL: ${{ steps.deploy.outputs.deployment-url }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          comment="🚀 Preview deployment available at: $DEPLOYMENT_URL"
          curl -X POST \
            -H "Authorization: token $GITHUB_TOKEN" \
            -H "Accept: application/vnd.github.v3+json" \
            https://api.github.com/repos/${{ github.repository }}/issues/${{ github.event.pull_request.number }}/comments \
            -d "{\"body\":\"$comment\"}"
```

### Notes

- Wrangler is used to deploy the application to Cloudflare Pages.
- The `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID` are required to deploy the application to Cloudflare Pages.
- The `GITHUB_TOKEN` is required to comment on the pull request.

This PR https://github.com/devmiguelangel/nuxt-cloud/pull/2 has a preview deployment available.
