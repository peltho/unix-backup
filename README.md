### Backup any Unix fs

Usage :

`sudo rsync -aAXv --exclude-from=exclude-list.txt / <distant-path>`

Restore backup by inverting `/` and `<distant-path>`

*Tip*: distant path can be accessed through *ssh* or it can also be a local path.
