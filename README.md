# GitHub Action - Releases API

![test](https://github.com/shogo82148/actions-upload-release-asset/workflows/test/badge.svg)

This GitHub Action uploads release assets using [Upload a release asset](https://developer.github.com/v3/repos/releases/#upload-a-release-asset).

## SYNOPSIS

### Upload assets when a release has been created

You can upload assets when [a release has been created](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/events-that-trigger-workflows#release-event-release).

```yaml
on:
  release:
    types:
      - created

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      # steps for building assets
      - run: echo "REPLACE ME!" > assets.txt

      - uses: shogo82148/actions-upload-release-asset@v1
        with:
          upload_url: ${{ github.event.release.upload_url }}
          asset_path: assets.txt
```

### Upload assets when a tag has been created

If you want to create a release in your workflow, you can use [actions/create-release](https://github.com/actions/create-release) GitHub Action.

```yaml
on:
  push:
    tags:
      - 'v*'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      # steps for building assets
      - run: echo "REPLACE ME!" > assets.txt

      - name: Create Release
        id: create_release
        uses: actions/create-release@v1.0.0
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          draft: false
          prerelease: false

      - uses: shogo82148/actions-upload-release-asset@v1
        with:
          upload_url: ${{ steps.create_release.outputs.upload_url }}
          asset_path: assets.txt
```

## Inputs

### github_token

The API token for GitHub.
`${{ github.token }}` is used by default.

### upload_url

The URL for uploading assets to the release.

### asset_path

The path to the asset you want to upload.
You can use [glob patterns](https://github.com/actions/toolkit/tree/master/packages/glob#patterns) here.

### asset_name

The name of the asset you want to upload.
The file name of `asset_path` is used by default.

### asset_content_type

The content-type of the asset you want to upload.
See the supported Media Types here: https://www.iana.org/assignments/media-types/media-types.xhtml for more information.

By default, the actions guess the content-type using the [mime-types](https://www.npmjs.com/package/mime-types) package.
