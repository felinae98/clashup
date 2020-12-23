# clashup

An updater for Clash on Linux.

- [Installation](#installation)
  - [For Arch Linux](#for-arch-linux)
  - [Manually enable for other distros](#manually-enable-for-other-distros)
- [Configurations](#configurations)
- [How it works](#how-it-works)
- [FAQ](#faq)

## Installation

### For Arch Linux

```sh
$ git clone https://github.com/felinae98/clashup.git && cd clashup && makepkg -si
```

or use aur

```sh
$ yay -Sy clashup
```

and just

```sh
$ systemctl --user start clash
```

### Manually enable for other distros

Clash on Linux is generally more managable with PM2 as a service manager. We will leverage PM2 to run both clash and clashup here. (See [Clash as a daemon](https://github.com/Dreamacro/clash/wiki/clash-as-a-daemon).)

An active system-wide installation of Python 3 is required as well as extra Python dependencies including `python3-requests` and `python3-daemon` (primarily for updating mmdb as a subprocess):

```sh
# On Ubuntu for instance:
# 1. Use Python 3 as system default Python
$ sudo apt install python-is-python3

# 2. Install libraries for system-installed Python 3
$ sudo apt install python3-requests python3-daemon python3-schedule

# 3. Install PM2 and start clash
$ wget -qO- https://getpm2.com/install.sh | bash
$ pm2 start clash
```

Then simply copy `clashup` to `/usr/local/bin`:

```sh
$ git clone https://github.com/felinae98/clashup && cd clashup

# Make script executable
$ chmod +x clashup

# Copy script to /usr/local/bin
$ sudo cp clashup /usr/local/bin/

# Verify that clashup is in $PATH, executable, and works as expected
$ clashup --update
```

You will need to edit configuration file `~/.config/clash/clashup.json`, specifially:

* Set `pm2` as true.
* Set `periodically_update` as true.

Finally enable `clashup` using PM2:

```sh
# Start clash with PM2 first
$ pm2 start clash

# Then start clashup
$ pm2 start /usr/local/bin/clashup --interpreter python
```

You might want to also enable PM2 on startup:

```sh
$ pm2 startup
# Which generates a command to paste and execute, you'll need to execute the command manually

$ pm2 save
```

## Configurations

The config file is `~/.config/clash/clashup.json`:

```json
{
    "http_port": 7890,
    "socks5_port": 7891,
    "redir_port": 7892,
    "mixed_port": 7893,
    "allow_lan": true,
    "external_controller": "127.0.0.1:9090",
    "external_ui": "dashboard",
    "subscribe_url": "",
    "is_subscribe_banned": false,
    "custom_rules": [],
    "mmdb_file_url": "http://www.ideame.top/mmdb/Country.mmdb",
    "mmdb_version_url": "http://www.ideame.top/mmdb/version"
}
```

* Set `is_subscribe_banned` to true if your subscribe address was banned.
* `http_port` is required if `is_subscribe_banned` is true.
* If you use external dashboard such as [yacd](https://github.com/haishanh/yacd), you can define `external_ui` as the folder name of the static webpage (default is `dashboard`) and put your downloaded static dashboard page inside directory `./dashboard`.
* If you both set `mmdb_file_url` and `mmdb_version_url` it will update `Country.mmdb` file every start up time.

## How it works

`clashup` simply downloads the remote subscription file and override `http-port`, `socks-port`, `redir-port`, `mixed-port`, `allow-lan`, `external-controller`, and `external-ui` in the config file, and append `custom_rules` to `Rules`.

If your can access your subscription url directly, it will download and update config file before clash start. Otherwise (if your subscription url is banned), `clashup` will download and update the config file after starting clash and then restart clash.

## FAQ

* How to see log:
    * For Arch Linux:
        ```sh
        journalctl --user -u clash -e
        ```
    * For other distros using PM2:
        ```sh
        pm2 logs
        ```

* How to add custom rules:

    Edit `~/.config/clash/clashup.json` and:

    * For Arch Linux:
        ```sh
        systemctl --user restart clash
        ```
    * For other distros using PM2:
        ```sh
        pm2 restart clash
        pm2 restart clashup
        ```
