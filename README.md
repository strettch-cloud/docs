# Strettch Cloud docs

The source of [docs.cloud.strettch.com](https://docs.cloud.strettch.com), built with [Mintlify](https://mintlify.com).

## Writing a page

The rules for writing a page are in [AGENTS.md](AGENTS.md), under "Writing a page". Read them before you write or edit a page.

## Preview your changes

Install the [Mintlify CLI](https://www.npmjs.com/package/mint):

```
npm i -g mint
```

Run it in the folder that holds `docs.json`:

```
mint dev
```

The preview is at `http://localhost:3000`. To check for broken links, run `mint broken-links`.

## Publishing

Changes are published when they are merged into the default branch.
