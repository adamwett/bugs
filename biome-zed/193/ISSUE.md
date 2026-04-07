---
title: 🐛 root biome.json not respected when not in workspace
---

### Zed version

Zed 0.230.2

### Extension version

0.2.2

### Biome version

2.4.10

### Operating system

- [ ] Windows
- [x] macOS
- [ ] Linux

### Description

when working in a monorepo, my root `biome.json` is only respected when that file is included in the workspace. when I open a package/app as a workspace, the root biome config is not respected

there's quite a lot of files involved so i put my reproduction into a public GitHub repo, but here are the `biome.json` files:

**monorepo/biome.json**
```json
{
	"$schema": "https://biomejs.dev/schemas/2.4.10/schema.json",
	"root": true,
	"files": {
		"includes": ["**/*.ts"]
	},
	"formatter": {
		"enabled": true,
		"formatWithErrors": false,
		"indentStyle": "tab",
		"indentWidth": 2,
		"lineWidth": 120
	},
	"javascript": {
		"formatter": {
			"quoteStyle": "single",
			"jsxQuoteStyle": "single",
			"semicolons": "always",
			"arrowParentheses": "always"
		}
	}
}
```

**monorepo/apps/my-app/biome.json**
```json
{
	"$schema": "https://biomejs.dev/schemas/2.4.10/schema.json",
	"root": false,
	"extends": "//",
	"files": {
		"includes": ["**/*.ts"]
	}
}
```

### Steps to reproduce

1. clone the repo linked below
2. open `biome-zed/145/monorepo` as a Zed workspace & install dependencies
3. in the workspace, open `biome-zed/145/monorepo/apps/my-app/src/file.ts`
4. save the file, note the quotes change from `""` to `''` as expected
5. restore the file with `git reset --hard HEAD`
6. now open `biome-zed/145/monorepo/apps/my-app` as a Zed workspace
7. in the workspace, open `src/file.ts`
8. save the file, and the quotes do not change

### Expected behavior
The Zed extension should recursively traverse parent folders, find `monorepo/biome.json`, and inhert the rules.

### Does this issue occur when using the CLI directly?

No, running `pnpm biome check` in both `monorepo` and `my-app` both produce:
```
  ✖ Formatter would have printed the following content:

    13 13 │   // export const quoteStyle = "aa";
    14 14 │
    15    │ - export·const·quoteStyle·=·"aa";
       15 │ + export·const·quoteStyle·=·'aa';
    16 16 │
```

### Link to a minimal reproduction

[https://github.com/adamwett/bugs/tree/main/biome-zed/145/monorepo](https://github.com/adamwett/bugs/tree/main/biome-zed/145/monorepo)
