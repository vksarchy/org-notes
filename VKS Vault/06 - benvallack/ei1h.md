#  install Mbsync

On a mac you can use `mbsync` to sync a remote email server with a local folder. It can be installed with on Mac with `sudo port install isync`. This is then configured in `~/.mbsyncrc`. Folder will need to be manually created first. Do not run it with the dryrun `-y` flag as it will create duplicates! You can keep your remote server passwords out of the config file with [[m0tl|pass and gpg]]

[Flaport guide](https://blog.flaport.net/tags/linux/configuring-neomutt-for-email.html)

