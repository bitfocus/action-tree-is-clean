# Notes for developers

By Chris Black writing to all project contributors, who are: Chris Black.

I got tired of forgetting the entire process in between releases and decided to write it down.

## Release Process

* merge all relevant updates to main
* update changelog
* make sure working tree is clean
* `npm version [major|minor|patch]`
	- bumps versions in package.json and package-lock.json
	- creates tag
* push tag
* create GitHub release
* GHA will run .github/workflows/publish.yml, which
	- checks out the release tag and runs `npm ci && npm run pack`, which
		+ builds `dist/index.js` (an ESM bundle, plus `dist/package.json`)
	- commits `dist/` on top of the tag (it is gitignored on main) and
	  force-pushes the release tag to point at that commit
	- force-pushes the major version tag (e.g. v2) to match, unless the release
	  is marked as a prerelease



### Release Troubleshooting

* the publish workflow needs the GHA token to have write permission to the repo
  (it requests `contents: write`, but repo/org settings can still restrict it)
* the project is ESM (`"type": "module"`), because `@actions/core` and
  `@actions/exec` v3+ are ESM-only. Don't switch it back to CommonJS.
* remember `npm version` bumps numbers for you; don't start manually updating them beforehand.
* the node_modules directory *does not* need to be checked in;
  ncc bundles everything into `dist/index.js`


## npm Cheatsheet

(Yes, I really do touch npm rarely enough that I forget all of this stuff)

Basic check for packages needing security updates:

```
npm ci
npm audit fix --force
```

Bumping to a new node version:

```
nvm install 24
```

npm won't auto-bump a dependency past a major version change.
To override, need to list packages by name and (I think) version:
(This set of packages probably unique to today's update, but shows the idea)
```
npm install --save @actions/core@latest @actions/exec@latest
```
