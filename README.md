keeplog
--
**keeplog** is a simple two-way synchronization tool to sync a local daily log with [Google Keep](https://keep.google.com). 
When run, keeplog parses the local file and synchronizes each entry to individual Keep notes. Updates within Keep are 
populated to the local file as well.

_keeplog is work-in-progress, so use with caution!_

I mainly wrote it to learn Python and to support my workflow: I keep a local file in `~/LOG` with one entry 
per day, like this:

```
11/5/20 Friday
--
Todo
- More stuff

11/8/20 Sunday
--
Done
- Stuff
``` 

I love the simplicity of a single text file and I absolutely hate the Google Keep UI in the browser. My notes are very
extensive and often include code, so I like my text editor much more. But naturally, text files don't sync well, which 
is why keeplog exists -- so I can access and edit my notes from my phone if I have a brilliant idea and I'm not at 
the computer.

Installation
--
1. Create a [Google App Password](https://myaccount.google.com/apppasswords) for keeplog
2. Create a config file `~/.keeplog/config` from [this template](config). Be sure to edit at least 
   `user=`, `pass=` and `file=`.
3. Install [Poetry](https://python-poetry.org) if you don't have it.
4. Install the application via Poetry into a virtualenv: `poetry install`
5. Optionally (but highly recommended), you may either regularly run `poetry run keeplog sync` as a cron, or `poetry run keeplog watch` 
   as a long running process, such as a systemd user service:
   
```
$ cat > ~/.config/systemd/user/keeplog.service <<EOF
[Unit]
Description=Keeplog watch daemon

[Service]
ExecStart=poetry run keeplog watch
WorkingDirectory=/path/to/keeplog

[Install]
WantedBy=default.target
EOF
$ systemctl enable --user keeplog
$ systemctl start --user keeplog
$ journalctl --user -f -u keeplog
``` 

Usage
--
keeplog has two commands:

- `keeplog sync` synchronizes local file with Google Keep
- `keeplog watch` monitors the local file for changes and syncs when changed

Manually (or via cron):

```
$ poetry run keeplog sync
2020-11-08 13:33:50,038 [INFO] Logging in with token
2020-11-08 13:33:59,221 [INFO] Successfully logged in
2020-11-08 13:33:59,221 [INFO] Comparing remote and local
2020-11-08 13:33:59,234 [INFO] - Updating locally: 11/5/20 Friday
2020-11-08 13:33:59,234 [INFO] - Updating remotely: 11/8/20 Sunday
2020-11-08 13:33:59,616 [INFO] Writing local notes
```

Using file system monitoring:

```
$ poetry run keeplog watch
2020-11-08 21:55:23,901 [INFO] Watching local log file for changes
2020-11-08 21:55:31,728 [INFO] File modified, triggering sync
...
``` 




Copyright & recognition
--
Philipp C. Heckel, licensed under the [Apache 2.0 License](LICENSE).

Thanks very much to [kiwiz](https://github.com/kiwiz) for his excellent [gkeepapi](https://github.com/kiwiz/gkeepapi),
and to [chrisjbillington](https://github.com/chrisjbillington) for his wonderfully easy [inotify_simple](https://github.com/chrisjbillington/inotify_simple).
