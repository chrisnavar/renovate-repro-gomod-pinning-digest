# Renovate does not generate Go package pseudo-version value correctly from git commit digest ([`#36798`](https://github.com/renovatebot/renovate/discussions/36798))

A minimal repro for [Renovate `#36798`](https://github.com/renovatebot/renovate/discussions/36798).

## Renovate v41.17.2

Tested with:

```sh
env GITHUB_COM_TOKEN=$(gh auth token) RENOVATE_TOKEN=$(gh auth token) LOG_LEVEL=debug RENOVATE_LOG_FILE=debug.json RENOVATE_LOG_FILE_LEVEL=debug npx renovate@41.17.2 --platform=local --dry-run=lookup --allowed-env GOPROXY
```

### Current behavior

1. This project pins to the (at time of writing) latest commit hash on the HEAD branch for the following dependencies:

  - [`github.com/chrisnavar/renovate-go-bug-repro-module`](https://github.com/chrisnavar/renovate-go-bug-repro-module)
    - Current Version: `v1.0.1-0.20250701073539-59c769b1c4d2`

2. Renovate runs in Lookup
3. For the `renovate-go-bug-repro-module` dependency, as there's no tag (at time of writing) after the current version of the dependency, Renovate choose the latest commit as update candidate, but even though it sets the `newDigest` property correctly with the latest commit digest, it can't generate the pseudo-version correctly and says that the `newValue` property value is the same as the current version of the dependency. 

<details>

<summary>Debug logs</summary>

```
DEBUG: packageFiles with updates (repository=local)
       "config": {
         "gomod": [
           {
             "deps": [
               {
                 "datasource": "golang-version",
                 "versioning": "go-mod-directive",
                 "depType": "golang",
                 "depName": "go",
                 "currentValue": "1.24.4",
                 "managerData": {"lineNumber": 2},
                 "updates": [],
                 "packageName": "go",
                 "warnings": [],
                 "sourceUrl": "https://github.com/golang/go",
                 "registryUrl": "https://raw.githubusercontent.com/golang/website",
                 "homepage": "https://go.dev/",
                 "mostRecentTimestamp": "2025-07-08T00:00:00.000Z",
                 "currentVersion": "1.24.4",
                 "currentVersionTimestamp": "2025-06-05T00:00:00.000Z",
                 "currentVersionAgeInDays": 57,
                 "fixedVersion": "1.24.4"
               },
               {
                 "datasource": "go",
                 "depType": "require",
                 "depName": "github.com/chrisnavar/renovate-go-bug-repro-module",
                 "currentValue": "v1.0.1-0.20250701073539-59c769b1c4d2",
                 "currentDigest": "59c769b1c4d2",
                 "digestOneAndOnly": true,
                 "versioning": "loose",
                 "managerData": {"lineNumber": 4},
                 "updates": [
                   {
                     "updateType": "digest",
                     "newValue": "v1.0.1-0.20250701073539-59c769b1c4d2",
                     "newDigest": "0597bbde9c98a33e304ab7869a0fd9000463573a",
                     "branchName": "renovate/github.com-chrisnavar-renovate-go-bug-repro-module-digest"
                   }
                 ],
                 "packageName": "github.com/chrisnavar/renovate-go-bug-repro-module",
                 "warnings": [],
                 "sourceUrl": "https://github.com/chrisnavar/renovate-go-bug-repro-module",
                 "mostRecentTimestamp": "2025-07-01T07:11:25.000Z",
                 "currentVersion": "v1.0.1-0.20250701073539-59c769b1c4d2",
                 "fixedVersion": "v1.0.1-0.20250701073539-59c769b1c4d2"
               }
             ],
             "packageFile": "go.mod"
           }
         ]
       }
```

</details>

### Expected behavior

- Renovate will set the `newValue` property value correctly with the expected pseudo-version of the latest commit.

As we can see in the debug logs printed above, the `currentValue` is equals to `newValue`:

```diff
...
-"currentValue": "v1.0.1-0.20250701073539-59c769b1c4d2",
"currentDigest": "59c769b1c4d2",
"digestOneAndOnly": true,
"versioning": "loose",
"managerData": {"lineNumber": 4},
"updates": [
  {
    "updateType": "digest",
-    "newValue": "v1.0.1-0.20250701073539-59c769b1c4d2",
    "newDigest": "0597bbde9c98a33e304ab7869a0fd9000463573a",
    "branchName": "renovate/github.com-chrisnavar-renovate-go-bug-repro-module-digest"
  }
],
...
```

The expected output would be the following, where the `currentValue` is different from `newValue`:

```diff
...
+"currentValue": "v1.0.1-0.20250701073539-59c769b1c4d2",
"currentDigest": "59c769b1c4d2",
"digestOneAndOnly": true,
"versioning": "loose",
"managerData": {"lineNumber": 4},
"updates": [
  {
    "updateType": "digest",
+    "newValue": "v1.0.1-0.20250701073617-0597bbde9c98",
    "newDigest": "0597bbde9c98a33e304ab7869a0fd9000463573a",
    "branchName": "renovate/github.com-chrisnavar-renovate-go-bug-repro-module-digest"
  }
],
...
```

This problem only happens in log output, but does not get reflected on PRs created by Renovatebot https://github.com/chrisnavar/renovate-repro-gomod-pinning-digest/pull/7/files as here in the `go.mod` file the correct pseudo-version of the Go package is generated.

## Link to the Renovate issue or Discussion

[Renovate `#36798`](https://github.com/renovatebot/renovate/discussions/36798)
