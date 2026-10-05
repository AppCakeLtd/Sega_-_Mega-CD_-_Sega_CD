# TITAN device integration

This fork serves Mega-CD / Sega CD game artwork to TITAN handhelds. Files
under `titan/` are ours; everything else stays merge-syncable with upstream
libretro-thumbnails.

- `serials-boxarts.json`: **GameID → boxart filename**, two key kinds:
  - **Redump serials**, spaces removed and uppercased (`T-93175`). The device
    reads the disc header (the data track's first sector, offset 0x180:
    `GM T-93175 -00`) and tries a fixed list of forms, because headers and
    Redump disagree on suffixes: the full form, the European `-50` form when
    the header's region field has `E`, the form without the revision suffix,
    the number without leading zeros, and the bare number (`MK-4430` → `4430`).
  - **`name-<crc32>`**: zlib crc32 of the Redump full name. Used for `.chd`
    images (the device would have to decompress them to read the header) and
    as the last try for any disc: the image's file name, minus the extension,
    is hashed.
  - The device then fetches `Named_Boxarts/<filename>`, plus the same name
    from `Named_Snaps` and `Named_Titles` where they exist.
- Lookups are exact; there is no fuzzy name matching on the device. That
  matching happens offline in the generator, where its output can be audited.
- `generate-mapping.py` regenerates both JSON files from libretro-database's
  Mega-CD Redump dat, joined against the actual `Named_Boxarts/` tree.
  - It reads the tree from git, so a blob-less partial clone is enough.
  - Run it after each upstream sync.
  - Curated fixes go in `MANUAL_EXTRA`, or edit the JSON directly via a PR.

Thumbnails that are git symlinks are resolved to their real file by the
generator: raw GitHub serves a symlink as text, never the image.

Coverage at generation (2026-10-05): 412 of 506 Redump releases mapped (416
serials, 412 name keys), reaching 412 of 609 boxart files (9 mappings resolved through
symlinks). Unmapped discs fall back to the UI's generated cover.
