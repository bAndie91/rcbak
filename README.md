# NAME

rcbak - Incremental backup mechanism based on tar,
configured by dotfiles and supporting gpg-encryption

# DESCRIPTION

Navigate to a directory containing required `.rcbak.*` files, 
then call rcbak(1).
First time the programm generates an initial tar(1) archive
and a meta-file named `increment.snar`.
Subsequent run of rcbak(1) makes a timestamped archive file: `YYYY-mm-dd_HHMM.tar.gz`.
If you already have archives, but want to do a full backup as initially,
just rename `increment.snar`.
A list of files to be archived is also generated, 
it is in NTS format (null terminated strings of file paths relative to base directory), `YYYY-mm-dd_HHMM.nts`.
It gives the chance to restore only those files from the incremental archives, 
which were exist on the given time,
not the deleted ones. 
File listing preceeds creating the archive, 
so NTS list and archived files may be inconsistent,
depending on usage of files and folders.
However even tar(1) itself is not atomic.
Consider using filesystem snapshots.
On every incremental backup it saves `increment.snar` to `increment.snar.old`,
so you can rollback manually to the previous state after a failed backup process.

# EXAMPLE

    cd backups/personal
    echo '~/documents' > .rcbak.ba
    rcbak

# FILES

## `.rcbak.ba` (required)

Contains base directory of files and folders to be archived.
Tilde (`~`) substituted to HOME directory.

## `.rcbak.ls`

List of files (separated by newline) within the base directory to be archived.
Default is the whole folder, if `.rcbak.ls` is missing.

## `.rcbak.ex`

List of files and patterns (separated by newline) to be excluded from the archive. Patterns are recognized by tar(1), see option `--exclude`.

## `.rcbak.ar`

Archiver compressor name. One of these: compress(1), gzip(1), bzip2(1), xz(1).

## `.noBackup`, `.rcbak.no`

Skip entire directory containing such a file. See tar(1) option `--exclude-tag`.

## `.rcbak.su`

Contains a username in 1st line. Programm reads files in name of this user using sudo(1). Write operations are not done by this user, so archive file created by the user started rcbak.

## `.rcbak.to`

Encrypt resulting archive for the named GPG user and signed by the default GPG key. See gpg(1) option `--recipient`.

## `.rcbak.pr`

Pre- and post-run script, running in the main namespace of rcbak(1), if exists.
Strings "pre" or "post" passed in "$1".

## `.rcbak.lk`

Lock file.

## `increment.snar`

Meta file of incremented backup. See tar(1) option `--listed-incremental`.

## `increment.snar.old`

A copy of `increment.snar` made before overwritten. This makes user able to restore the previous state of backup archives, if something goes wrong.

## `*.fail`

Archives, NTS files, and meta file renamed after an unsuccessful call.
