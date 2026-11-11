# spinsitter

Keeps the external hard drives on your Mac from spinning down.

Many external hard drives go to sleep after a few minutes of inactivity. The
next time you open a file, everything freezes while the drive spins back up,
and some drives wear out sooner from the constant stop/start. spinsitter keeps
them awake.

It's a single shell script plus a macOS background job (launchd). There's no
app, nothing stays running between checks, and nothing needs updating when
macOS changes.

## Install

```bash
git clone https://github.com/partytimeexcellent/spinsitter.git
cd spinsitter
./spinsitter install
```

That's it. It starts right away, and again every time you log in. To check
more or less often than every 60 seconds, give it a number of seconds:
`./spinsitter install 120`.

If you used the **Keep Drive Spinning** app before, the installer turns off its
background job for you.

## Check what it's doing

```bash
./spinsitter status
```

```
spinsitter is on, checking every 60 seconds.
  Archive                      kept awake (last write: 2026-11-11 10:49:30 PST)
  Macintosh HD                 skipped: internal
  Fast SSD                     skipped: SSD
  RAID Array                   kept awake (last write: 2026-11-11 10:49:31 PST)
```

## Which drives it keeps awake

Every attached drive that is:

- external and writable,
- not a disk image or a network share, and
- not an SSD. If a drive doesn't report whether it's an SSD (common with RAID
  enclosures and some USB adapters), it's treated as a hard drive when it's
  larger than 64 GB.

New drives are picked up automatically within one interval of plugging them in.

**To let a drive sleep**, put an empty file named `.spinsitter-sleep` on it:

```bash
touch "/Volumes/My Drive/.spinsitter-sleep"
```

Delete that file to start keeping the drive awake again.

## How it works

Every interval, launchd runs `spinsitter run`, which writes the current time to
a hidden file named `.spinsitter` at the top of each drive. That small write is
enough to reset the drive's sleep timer. Each check takes well under a second.

Installing puts a copy of the script in
`~/Library/Application Support/spinsitter/` and the job in
`~/Library/LaunchAgents/io.github.partytimeexcellent.spinsitter.plist`.
It may show up as "spinsitter" under System Settings › General › Login Items.

## Uninstall

```bash
./spinsitter uninstall
```

## Thanks

Inspired by Keep Drive Spinning by Jon Stovell, a free app that used the same
hidden-file approach for years.

## License

MIT. See [LICENSE](LICENSE).
