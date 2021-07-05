<img src="https://user-images.githubusercontent.com/32114380/103098481-daa70a00-4645-11eb-9be1-908620a368a9.png" align="right" width="150px" />

# clashup

🚀 A subscription updater and process supervisor for clash on Linux.

- [Installation](#installation)
  - [Arch Linux](#arch-linux)
  - [Ubuntu and Debian](#ubuntu-and-debian)
  - [Manual installation](#manual-installation)
- [Config](#config)
- [How it works](#how-it-works)
- [FAQ](#faq)

Why? **Because us Linux users don't deserve to have a well-maintained clash GUI!**

clashup manages remote clash subscription synchronization for you, as well as running clash itself in the background. You can think of clashup as a primitive CLI solution for running clash on Linux, although clashup doesn't distribute the actual clash executable.

## Installation

### Arch Linux

```sh
$ git clone https://github.com/felinae98/clashup.git && cd clashup && makepkg -si
```

or use aur

```sh
$ yay -Sy clashup
```

and just

```
$ systemctl --user start clash
```

### Ubuntu and Debian

### Manual installation

## Config

The config file is automatically created upon first run at: `~/.config/clash/clashup.json`

```json
{
    "clash_path": "/usr/local/bin/clash",
    "update_interval": 24,
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

* Default `clash_path` is at `/usr/local/bin/clash`.
* `update_interval` is measured **by the hour**.
* Optional `external_ui` is available with [yacd](https://github.com/haishanh/yacd), you could just download the [zip of its built web source](https://github.com/haishanh/yacd/archive/gh-pages.zip), unzip to path (defaults to `~/.config/clash/dashboard`), and go to `http://127.0.0.1:9090/ui/` after starting clashup.
* Set `is_subscribe_banned` true if your subscribe address was banned.
* `http_port` is required if `is_subscribe_banned` is true.
* If you both set `mmdb_file_url` and `mmdb_version_url` it will update `Country.mmdb` file each start up.

## How it works

clashup simply downloads your clash's subscription config file and override `http-port`, `socks-port`, `redir-port`, `mixed-port`, `allow-lan`, `external-controller`, and `external-ui` in the config file, and append `custom_rules` to `Rules`.

If your can access your subscription url directly, clashup will just download and update config file before clash start. Otherwise if your subscription url is banned, clashup will download and update the config file after clash start and then restart clash.

## FAQ

* How to see log

    `journalctl --user -u clash -e`

* How to add custom rules

    edit `~/.config/clash/clashup.json` and `systemctl --user restart clash`
