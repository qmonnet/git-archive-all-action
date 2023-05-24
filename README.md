# Git-archive-all - GitHub Action

This repository contains a GitHub Action based on [git-archive-all], to archive
a git project along with all its submodules, taking into account any exclusion
specified in `.gitattributes`.

## Action Inputs

### Required

- `output-files`:
  Output file names, for example: `project.zip project.tar.gz`. The output
  format for the archive is deduced from the file extension. The formats
  currently supported are: `tar`, `bz2`, `tgz`, `txz`, `bz2`, `gz`, `xz`, and
  `zip`.

### Optional

- `base-repo`:
  Main Git repository to archive, defaults to the path of the current working
  directory.

- `prefix`:
  Prefix to prepend to each filename in the archive, empty by default.

- `export-ignore` (boolean):
  Follow `export-ignore` attributes from `.gitattributes`, enabled by default.

- `force-submodules` (boolean):
  Force `git submodule init && git submodule update` at each level before
  iterating submodules, enabled by default.

- `extra-includes`:
  Additional files to include in the archive, empty by default.

- `compression-level`:
  Compression level to use; interpretation depends on the output format. Set to
  `-9` by default. Currently incompatible with `txz` and `xz` output formats.

- `verbose` (boolean):
  Set verbose mode, enabled by default.

## Other parameters

This GitHub Action produces no output.

It uses no secret.

It does not use any particular environment variable.

## Example use

```yaml
name: release

on:
  push:
    tags:
      - '**'

jobs:
  draft-release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout project source code
        uses: actions/checkout@v3
        with:
          path: project

      - name: Package source code including submodules
        uses: qmonnet/git-archive-all-action@v1
        with:
          output-files: srcs-full-${{ github.ref_name }}.tar.gz srcs-full-${{ github.ref_name }}.zip
          base-repo: project

      - name: Create draft release and add artifacts
        uses: softprops/action-gh-release@v1
        with:
          draft: true
          files: srcs-full-*
```

## Notes

- Contrarily to `git archive`, the `.gitattributes` rules using a directory
  name to exclude some paths from exports do not work, a wildcard must be
  specified after the directory name and the `/`.

  For example, the following will result in `git archive` skipping all files in
  the `assets` directory, but will be ignored by `git_archive_all.py`:

  ```gitattributes
  assets/ export-ignore
  ```

  The same thing can be achieved with this GitHub Action by using a wildcard
  instead:

  ```gitattributes
  assets/** export-ignore
  ```

## Contributing

I'd rather not maintain a separate version of the script, so please consider
submitting any contribution for `git_archive_all.py` to [its upstream
repository][git-archive-all], and we can pull the changes here after that.

## Credits

Credits for the `git_archive_all.py` script go to Ilya Kulakov and to
[the other contributors][git-archive-all-contrib] of that project.

[git-archive-all]: https://github.com/Kentzo/git-archive-all
[git-archive-all-contrib]: https://github.com/Kentzo/git-archive-all/graphs/contributors
