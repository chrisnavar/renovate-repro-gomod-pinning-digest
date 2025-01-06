# Renovate downgrades a Go module pinned to a SHA ([`#24366`](https://github.com/renovatebot/renovate/discussions/24366))

A minimal repro for [Renovate `#24366`](https://github.com/renovatebot/renovate/discussions/24366)).

This may be a recurrence of [Golang.org module dependency downgraded from pseudo version latest commit to latest tag (`#31133`)](https://github.com/renovatebot/renovate/discussions/31133) and [](https://github.com/renovatebot/renovate/discussions/24366)

## Renovate v37.425.1 (Mend Renovate Community Edition 7.5.0)

Tested with:

```sh
env GITHUB_COM_TOKEN=$(gh auth token) RENOVATE_TOKEN=$(gh auth token) LOG_LEVEL=debug RENOVATE_LOG_FILE=debug.log RENOVATE_LOG_FILE_LEVEL=trace npx renovate@37.425.1 --platform=local --dry-run=lookup
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

- Renovate will not try and downgrade the dependency
- Renovate will either:
  - Try and upgrade to the latest digest (Git commit) for the dependency
  - Only attempt to upgrade to a new tag when the tag is released after the digest (commit) was released

## Link to the Renovate issue or Discussion

[Renovate `#24366`](https://github.com/renovatebot/renovate/discussions/24366)).
