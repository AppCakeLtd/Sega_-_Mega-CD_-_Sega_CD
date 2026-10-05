# TITAN device integration

This fork serves Mega-CD / Sega CD game artwork to TITAN handhelds. Files
under `titan/` are ours; everything else stays merge-syncable with upstream
libretro-thumbnails.

- `serials-boxarts.json`: **GameID (serial) → boxart filename**.
  - The device reads a disc's serial from its header (the data track's first
    sector, offset 0x180: `GM T-93175 -00` → `T-93175`) for `.cue`/`.bin`
    and `.iso` images.
  - It looks the serial up here and fetches `Named_Boxarts/<filename>`, plus
    the same name from `Named_Snaps` and `Named_Titles` where they exist.
- `names-boxarts.json`: the same join keyed by **Redump full name**, for
  compressed `.chd` images (whose header the device can't read without
  decompressing): the image's file name, minus `.chd`, is looked up exactly.
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
serials), reaching 412 of 609 boxart files (9 mappings resolved through
symlinks). Unmapped discs fall back to the UI's generated cover.
