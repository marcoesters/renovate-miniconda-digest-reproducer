# Renovate Digest Miniconda reproducer

A minimal example for a [digest bug](https://github.com/renovatebot/renovate/discussions/28343#discussioncomment-9509852) in renovate.

Pretty-printed `matchString`:

```
# renovate:\s*datasource=(?<datasource>[\w\.]+?)\s
INSTALLER_URL=["']https:\/\/repo\.anaconda\.com\/\w+\/\w+-(?<currentValue>(py\d+_)?[\d\.]+\-\d+?)-(?<depName>[\w\-\.]+?)["']\s
SHA256SUM=["'](?<currentDigest>[a-f\d]+?)["']\s
```

Pretty-printed `transformTemplate`:

```
{
  "releases": [
    $map($sift($, function($v, $k) {
      $not($k.$contains("latest"))
      and $v.sha256 = $lookup($, "Miniconda3-latest-{{ packageName }}").sha256
    }).$keys(), function($v) {
      {
        "digest": $lookup($, $v).sha256, "version": $contains($v.$split("-")[2], /^[0-9]+$/) ? $v.$split("-")[1] & "-" & $v.$split("-")[2] : $v.$split("-")[1],
        "changeLogUrl": "https://docs.anaconda.com/free/miniconda/miniconda-release-notes/#miniconda-" & ($contains( $v.$split("-")[2], /^[0-9]+$/) ? $v.$split("-")[1].$split("_")[1] & "-" & $v.$split("-")[2] : $v.$split("-")[1].$split("_")[1]).$replace(".", "-"),

        "sourceUrl": "https://repo.anaconda.com/miniconda/" & $v, releaseTimestamp": $fromMillis($lookup($, $v).mtime * 1000, "[Y0001]-[M01]-[D01]T[H01]:[m01]", "-6")
      }
    }
  )],
  "sourceUrl": "https://repo.anaconda.com/miniconda/",
  "homepage": "https://anaconda.com"
}
```

