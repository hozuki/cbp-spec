# Common Beatmap Package Specification

*- Draft version 1; February 2017*

The Common Beatmap Package (CBP) is an archive format designed for multiple rythm games (MUG). It shapes
a specification and norm to store beatmap and related resource files.

## 1. General

**CBP is not a completely new format**, but a derivation of standard PK-ZIP archive, just like JAR.

ZIP archive is a universal compression and storage format, supported on multiple platforms, by multiple
languages and libraries. Therefore, using ZIP format makes it easy for input / output, by different
software, on different platforms. And if you follow the CBP spec, you can even create such a package
by hand with little effort. It is both human-friendly and software-friendly.

## 2. File Structure

An example of file structure is displayed as below:

```plain
[root]
|- manifest.ini           [file]
`- data                   [directory]
 ` (beatmap format names) [directory/-ies]
```

As you can see, inside the ZIP archive, a `manifest.ini` is placed at root directory. This is the package
manifest used to describe the package, and to search for other resources inside this package. All other
resource files are placed inside `data` directory.

## 3. Package Manifest

```ini
[format]
version=1.0           ; Manifest version.
beatmap=osu cgss      ; Beatmap format. Corresponding sections must exist.
universal=1           ; Indicates if resources other than beatmaps are shared between
                      ;   different beatmap formats. (Optional)

[music]
file=bgm1.mp3         ; Located at data/bgm1.mp3.
cover=cover.jpg       ; Located at data/cover.jpg. Used as package cover.
artist=Miss Artist    ; Artist name.
album=Album Name      ; Album name.

[osu-meta]
version=1             ; OSU section format version.
mapper=Mr. Mapper     ; Beatmap mapper.
date=2017-01-01       ; Release date. (YYYY-MM-DD)
music-file=osubg.mp3  ; Located at data/osu/osubg.mp3

[osu]
rank=5
mode=taiko
...

[cgss-meta]
version=1
mapper=Mr. Mapper
date=2017-02-02

[cgss]
...
```

> For boolean options, these values are regarded as true: `true`, non-zero numbers. Other values are
> regarded as the option's default value (usually false).
>
> `universal`: If it is set to true, music files will be searched from path written in `file` option
> in `music` section. Otherwise it will be searched from path written in `music` option in [*-meta]
> section(s). The latter have a higher priority when both options exist.
>
> Resources of each beatmap format must be placed in a directory whose name corresponds to the beatmap
> format. For example, `cgss` resources should be placed in `/data/cgss/`.

Beatmaps included in the package is defined in `beatmap` option of `format` section, separated by space.
Since there are two types of beatmaps (`osu` and `cgss`), the manifest reader will try to find these sections:

- `osu`
- `osu-meta`
- `cgss`
- `cgss-meta`

The parsing actions of custom sections for beatmap is decided by format reader. Since INI does not include
references to other options or sections natively, features like that can be provided by readers, but still,
with proper format specification. These format-specific specs are to be discussed in their own section spec
description files.

> Although CBP is designed to support multiple beatmaps of multiple formats in a single package,
it is not suggested to include multiple beatmap formats in a single package.

The reason of choosing INI as the manifest format is that it is easy to write and read by human and software.
But why not JSON? JSON requires a strict bracket pairing, thus not very friendly to humans if the hierarchy
is too complex. For INI files, there are only 2 hierarchies, plain and simple.

## 4. Example



## 5. Remarks

1. **Avoid using non-ASCII characters in file names.** For example, see [here](https://marcosc.com/2008/12/zip-files-and-encoding-i-hate-you/).
In addition, file names in Asian languages will cause much more trouble than in the example above.
2. Encoding of the package manifest is UTF-8.
3. Line spacing of the package manifest is LF (`\n`).
