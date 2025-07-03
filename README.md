# Renovate does not generate Go package pseudo-version value correctly from git commit digest ([`#36798`](https://github.com/renovatebot/renovate/discussions/36798))

A minimal repro for [Renovate `#36798`](https://github.com/renovatebot/renovate/discussions/36798).

## Renovate v41.17.2

Tested with:

```sh
env GITHUB_COM_TOKEN=$(gh auth token) RENOVATE_TOKEN=$(gh auth token) LOG_LEVEL=debug RENOVATE_LOG_FILE=debug.json RENOVATE_LOG_FILE_LEVEL=debug npx renovate@41.17.2 --platform=local --dry-run=lookup --allowed-env GOPROXY
```

### Current behavior

1. This project pins to the (at time of writing) latest commit hash on the HEAD branch for the following dependencies:
  - [`github.com/elastic/cloud-on-k8s/v2`](https://pkg.go.dev/github.com/elastic/cloud-on-k8s/v2)
    - Current Version: `v2.0.0-20250106074624-c4f5f812ea7c`

  - [`github.com/chrisnavar/renovate-go-bug-repro-module`](https://github.com/chrisnavar/renovate-go-bug-repro-module)
    - Current Version: `v1.0.1-0.20250701073539-59c769b1c4d2`

2. Renovate runs in Lookup
3. For the `cloud-on-k8s` dependency, Renovate gets correctly the `v2.16.1` and `v3.0.0` tags as update candidates.
4. For the `renovate-go-bug-repro-module` dependency, as there's no tag (at time of writing) after the current version of the dependency, Renovate choose the latest commit as update candidate, but even though it sets the `newDigest` property correctly with the latest commit digest, it can't generate the pseudo-version correctly and says that the `newValue` property value is the same as the current version of the dependency. 

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
                 "mostRecentTimestamp": "2025-06-05T00:00:00.000Z",
                 "currentVersion": "1.24.4",
                 "currentVersionTimestamp": "2025-06-05T00:00:00.000Z",
                 "currentVersionAgeInDays": 28,
                 "fixedVersion": "1.24.4"
               },
               {
                 "datasource": "go",
                 "depType": "require",
                 "depName": "github.com/elastic/cloud-on-k8s/v2",
                 "currentValue": "v2.0.0-20250106074624-c4f5f812ea7c",
                 "currentDigest": "c4f5f812ea7c",
                 "digestOneAndOnly": true,
                 "versioning": "loose",
                 "managerData": {"lineNumber": 4},
                 "updates": [
                   {
                     "bucket": "non-major",
                     "newVersion": "v2.16.1",
                     "newValue": "v2.16.1",
                     "newDigest": "1f74bdd90e8a0eb0f62df80edc2ae58d820e4e73",
                     "releaseTimestamp": "2025-01-17T16:21:02.000Z",
                     "newVersionAgeInDays": 166,
                     "newMajor": 2,
                     "newMinor": 16,
                     "newPatch": 1,
                     "updateType": "minor",
                     "isBreaking": false,
                     "branchName": "renovate/github.com-elastic-cloud-on-k8s-v2-2.x"
                   },
                   {
                     "bucket": "major",
                     "newVersion": "v3.0.0",
                     "newValue": "v3.0.0",
                     "newDigest": "89a41511a4bc8fdb4770cf1da60c37b8b8232b6d",
                     "releaseTimestamp": "2025-04-07T13:53:39.000Z",
                     "newVersionAgeInDays": 86,
                     "newMajor": 3,
                     "newMinor": 0,
                     "newPatch": 0,
                     "updateType": "major",
                     "isBreaking": true,
                     "branchName": "renovate/github.com-elastic-cloud-on-k8s-v2-3.x"
                   }
                 ],
                 "packageName": "github.com/elastic/cloud-on-k8s/v2",
                 "warnings": [],
                 "sourceUrl": "https://github.com/elastic/cloud-on-k8s",
                 "mostRecentTimestamp": "2025-04-07T13:53:39.000Z",
                 "currentVersion": "v2.0.0-20250106074624-c4f5f812ea7c",
                 "isSingleVersion": true,
                 "fixedVersion": "v2.0.0-20250106074624-c4f5f812ea7c"
               },
               {
                 "datasource": "go",
                 "depType": "require",
                 "depName": "github.com/chrisnavar/renovate-go-bug-repro-module",
                 "currentValue": "v1.0.1-0.20250701073539-59c769b1c4d2",
                 "currentDigest": "59c769b1c4d2",
                 "digestOneAndOnly": true,
                 "versioning": "loose",
                 "managerData": {"lineNumber": 6},
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

- Renovate will not try and downgrade the dependency
- Renovate will either:
  - Try and upgrade to the latest digest (Git commit) for the dependency
  - Only attempt to upgrade to a new tag when the tag is released after the digest (commit) was released

## Renovate v39.91.0

Tested with:

```sh
env GITHUB_COM_TOKEN=$(gh auth token) RENOVATE_TOKEN=$(gh auth token) LOG_LEVEL=debug RENOVATE_LOG_FILE=debug.log RENOVATE_LOG_FILE_LEVEL=trace npx renovate@39 --platform=local --dry-run=lookup
```

### Current behavior

1. This project pins to the (at time of writing) latest commit hash on the HEAD branch for the dependency [`github.com/elastic/cloud-on-k8s/v2`](https://pkg.go.dev/github.com/elastic/cloud-on-k8s/v2)
  - Current Version: `v2.0.0-20250106074624-c4f5f812ea7c`
1. Renovate runs in Lookup
1. Renovate sees the "latest" release as the latest released Git tag
  - New Version: `v2.16.0`

However, the latest tag is released after the latest commit.

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
                 "currentValue": "1.23.4",
                 "managerData": {"lineNumber": 2},
                 "updates": [],
                 "packageName": "go",
                 "warnings": [],
                 "sourceUrl": "https://github.com/golang/go",
                 "registryUrl": "https://raw.githubusercontent.com/golang/website",
                 "homepage": "https://go.dev/",
                 "currentVersion": "1.23.4",
                 "currentVersionTimestamp": "2024-12-03T00:00:00.000Z",
                 "currentVersionAgeInDays": 34,
                 "fixedVersion": "1.23.4"
               },
               {
                 "datasource": "go",
                 "depType": "require",
                 "depName": "github.com/elastic/cloud-on-k8s/v2",
                 "currentValue": "v2.0.0-20250106074624-c4f5f812ea7c",
                 "currentDigest": "c4f5f812ea7c",
                 "digestOneAndOnly": true,
                 "versioning": "loose",
                 "managerData": {"lineNumber": 4},
                 "updates": [
                   {
                     "bucket": "non-major",
                     "newVersion": "v2.16.0",
                     "newValue": "v2.16.0",
                     "newDigest": "fea1947f389ea80d6d65986a2659987438afec2d",
                     "releaseTimestamp": "2024-12-17T19:44:07.000Z",
                     "newVersionAgeInDays": 19,
                     "newMajor": 2,
                     "newMinor": 16,
                     "newPatch": 0,
                     "updateType": "minor",
                     "branchName": "renovate/github.com-elastic-cloud-on-k8s-v2-2.x"
                   }
                 ],
                 "packageName": "github.com/elastic/cloud-on-k8s/v2",
                 "warnings": [],
                 "sourceUrl": "https://github.com/elastic/cloud-on-k8s",
                 "currentVersion": "v2.0.0-20250106074624-c4f5f812ea7c",
                 "isSingleVersion": true,
                 "fixedVersion": "v2.0.0-20250106074624-c4f5f812ea7c"
               }
             ],
             "packageFile": "go.mod"
           }
         ]
       }
```

</details>

### Expected behavior

- Renovate will set the `nevValue` property value correctly with the expected pseudo-version of the latest commit.

## Link to the Renovate issue or Discussion

[Renovate `#36798`](https://github.com/renovatebot/renovate/discussions/36798)
