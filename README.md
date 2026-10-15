<h1 align="center" width="100%">
  <img src="https://raw.githubusercontent.com/surishortlink/suri/HEAD/logo.png" width="200" alt="Suri" />
</h1>

<h3 align="center" width="100%">
  <i>Your own short links as an easily deployed static site on GitLab Pages</i>
</h3>

You're viewing a template repository tailored for deploying Suri to
[GitLab Pages](https://docs.gitlab.com/user/project/pages/) with
[GitLab CI/CD](https://docs.gitlab.com/ci/). Head over to
[the main repository](https://github.com/surishortlink/suri) to learn more about
Suri, including additional deployment options.

## Setup: Step By Step

1. On [GitLab](https://gitlab.com/), hit "Create new" > "New project/repository"
   in the upper-right corner, then "Import project" and "Repository by URL".
2. Enter `https://github.com/surishortlink/suri-deploy-gitlab.git` as the "Git
   repository URL". Choose the namespace, project name, and visibility level for
   your new project, then hit "Create project".
3. If your new project is private, go to its "Settings" and head to the
   "General" section. Expand "Visibility, project features, permissions", change
   "Pages" to "Everyone", and hit "Save changes". Otherwise, only members of
   your project can see your site.
4. Go to "Build" > "Pipelines" of your new project and hit "New pipeline". Run
   it for the `main` branch to build and deploy your site for the first time.
5. Go to "Deploy" > "Pages" to find your site's URL.

GitLab.com may ask you to verify your identity before running your first
pipeline.

### Auto-Deploy

Any commits to the `main` branch of your new project will trigger a new build
and deploy. Merge requests are built without deploying, so a mistake in
`links.json` is caught before it's merged. You can change this by editing
[`.gitlab-ci.yml`](.gitlab-ci.yml), which is the GitLab CI/CD pipeline for
building the site and deploying to GitLab Pages.

### Custom Domain

Without a custom domain, GitLab Pages serves your site from a unique domain,
like `https://my-links-123456.gitlab.io/`, so your short links are at the root
of the domain.

To use a custom domain, follow GitLab's guide:
[Custom domains and SSL/TLS certificates](https://docs.gitlab.com/user/project/pages/custom_domains_ssl_tls_certification/).

### Updating Suri

To update Suri to its latest version:

```bash
npm install @surishortlink/suri@latest
```

Then commit the updated `package.json` and `package-lock.json`. Before updating
to a new major version, check
[the release notes](https://github.com/surishortlink/suri/releases) for breaking
changes.

## How It Works

### Manage Links

At the heart of Suri is the [`links.json`](src/links.json) file, located in the
`src` directory, where you manage your links. All of the template repositories
include this file seeded with a few examples:

```json
{
  "/": "https://www.youtube.com/watch?v=CsHiG-43Fzg",
  "1": "https://fee.org/articles/the-use-of-knowledge-in-society/",
  "gh": "https://github.com/surishortlink/suri"
}
```

It couldn't be simpler: the key is the "short link" path that gets redirected,
and the value is the target URL. Keys can be as short or as long as you want,
and can include `/` for nested paths, like `gh/suri`. Avoid `?` and `#`, which
browsers treat as the start of a query string or fragment. `/` is a special
entry for redirecting the root path.

### Build Static Site

Suri ships with a `suri` executable file that generates the static site from the
`links.json` file. The static site is output to a directory named `build`.

All of the template repositories are configured with a `build` script that
invokes this executable, making the command you run simple:

```bash
npm run build
```

When you make a change to the `links.json` file, simply re-run this command to
re-generate the static site, which can then be re-deployed. This template
repository is configured to do this automatically.

### Config

Configuration is handled through the [`suri.config.json`](suri.config.json) file
in the root directory. There is only one option at this point:

| Option | Description                                                        | Type    | Default |
| ------ | ------------------------------------------------------------------ | ------- | ------- |
| `js`   | Whether to redirect with JavaScript instead of a `<meta>` refresh. | Boolean | `false` |

### Public Directory

Finally, any files in the `public` directory will be copied over to the `build`
directory without modification when the static site is built. This can be useful
for files like `favicon.ico` or `robots.txt` (that said, Suri provides sensible
defaults for both).
