---
layout: page
title: "Downloader"
description: "Instructions how to setup the downloader component with Home Assistant."
date: 2015-01-24 14:39
sidebar: false
comments: false
sharing: true
footer: true
---

The `downloader` component provides a service to download files. It will raise an error and not continue to set itself up when the download directory does not exist.

To enable it, add the following lines to your `configuration.yaml`:

```
downloader:
  download_dir: downloads
```

#### Service `downloader/download_file`

Download the specified url.

| Service data attribute | Optional | Description |
| ---------------------- | -------- | ----------- |
| `url`                  |       no | The url of the file to download.

