# UpSnap: wake sleeping Macs for remote control

**The Mac answers ping, but your remote-control tool still cannot connect.** This fork contributes a fix and a setup guide for that gap.

**中文：Mac 能 Ping 通，不代表已经完全唤醒。这里的改进让完整唤醒命令执行完，并教你把它配置成可用于远程控制的方案。**

Contributions by [nanomassai](https://github.com/nanomassai), based on [seriousm4x/UpSnap](https://github.com/seriousm4x/UpSnap). Original project attribution and MIT licensing are retained.

## What improved?

| The actual problem | Our contribution | Where to find it |
| --- | --- | --- |
| A custom wake command is still working, but UpSnap sees a successful ping, stops the command and reports success. | **Let the wake command finish.** Require command success and reachability; retain failure reporting and the configured timeout. | [Code fix: PR #1777](https://github.com/seriousm4x/UpSnap/pull/1777) |
| Ordinary WOL can leave some Macs in DarkWake: the network responds, but third-party remote-desktop/remote-control tools remain unavailable. | **Complete the wake on supported Macs.** Configure restricted SSH to run a short `caffeinate` user-activity command, with persistence and real remote-control verification. | [Read the complete guide](https://github.com/nanomassai/UpSnap/blob/docs/macos-full-wake-guide/docs/macos-full-wake.md) · [Documentation PR #1778](https://github.com/seriousm4x/UpSnap/pull/1778) |

The backend fix is generic: it helps any custom wake command that must finish after the device becomes reachable. The Mac recipe is optional configuration, not a new WOL protocol or a claim that every Mac behaves the same way.

## Start here

- **Review or test the code:** [fix/wait-for-custom-wake-command](https://github.com/nanomassai/UpSnap/tree/fix/wait-for-custom-wake-command).
- **Configure a Mac:** [Mac full-wake guide](https://github.com/nanomassai/UpSnap/blob/docs/macos-full-wake-guide/docs/macos-full-wake.md). It covers the restricted key, host-key verification, persistent storage, custom command, an authenticated API option, logs and rollback.
- **Follow upstream review:** [#1777](https://github.com/seriousm4x/UpSnap/pull/1777) and [#1778](https://github.com/seriousm4x/UpSnap/pull/1778). Both were open and unmerged when this overview was updated on 2026-10-12.

**This default branch presents the contributions; the changes are on the linked branches.** Checking out `master` or pulling an original upstream release image does not automatically include #1777. The guide requires that fix or an equivalent build.

The separate frontend improvement, **sending wake even when a device is already shown as online**, has been tested in the local deployment but is **not yet submitted in these PRs**. Until an explicit action is available in your version, the guide describes the existing authenticated wake API. It does not recommend faking an offline state.

## What do you configure yourself?

Supply your own Mac IP/DNS name, account, SSH port, device MAC/netmask, dedicated key, verified host key and allowed source address. Examples use placeholders or interactive input. No private deployment addresses, account credentials or keys are included.

The dedicated key is restricted on the Mac to `caffeinate -u -t 5`. It is not intended for an interactive shell, forwarding, file access or desktop unlocking. The guide explains both the restrictions and the implications of storing it in a persistent data directory and backups.

## Evidence and limits

- Five regression tests cover command completion, delayed failure, reachability after completion, offline timeout and stopping an overlong command.
- The two main regression cases fail on unmodified upstream and pass with the fix.
- Backend tests, `go vet` and targeted race checks pass; the networking package cross-compiles for Windows/amd64 and macOS/arm64. These compile checks are not runtime tests on those platforms.
- A local deployment successfully restored third-party remote control after several minutes of Mac sleep; the last captured sleep interval was **4 minutes 20 seconds**. Container-restart verification also passed. Whole-host/router power-cycle recovery has not been verified.

Code, tests and documentation were prepared with OpenAI Codex assistance. Exact test details and compatibility notes are recorded in the PRs.

<details>
<summary>Original UpSnap README — upstream features and installation</summary>

The original documentation below describes upstream UpSnap. Its release/image links do not imply that the pending contributions above are included.

<div align="center" width="100%">
    <img src="frontend/static/gopher.svg" width="150" />
</div>

<div align="center" width="100%">
    <h2>UpSnap</h2>
    <p>A simple wake on lan web app written with SvelteKit, Go and PocketBase.</p>
    <div>
        <a target="_blank" href="https://github.com/seriousm4x/upsnap"><img src="https://img.shields.io/github/stars/seriousm4x/UpSnap?style=flat&label=Stars" /></a>
        <a target="_blank" href="https://hub.docker.com/r/seriousm4x/upsnap"><img src="https://img.shields.io/docker/pulls/seriousm4x/upsnap?label=docker%20hub%20pulls" /></a>
        <a target="_blank" href="https://github.com/seriousm4x/UpSnap/releases"><img src="https://img.shields.io/github/downloads/seriousm4x/upsnap/total?label=binary%20downloads" /></a>
    </div>
    <div>
        <a target="_blank" href="https://github.com/seriousm4x/UpSnap/releases"><img src="https://img.shields.io/github/go-mod/go-version/seriousm4x/UpSnap?filename=backend/go.mod" /></a>
        <a target="_blank" href="https://github.com/seriousm4x/UpSnap/releases"><img src="https://img.shields.io/github/v/release/seriousm4x/upsnap?display_name=tag&label=Latest%20release" /></a>
        <a target="_blank" href="https://github.com/seriousm4x/UpSnap/actions"><img src="https://github.com/seriousm4x/upsnap/actions/workflows/release.yml/badge.svg?event=push" /></a>
        <a target="_blank" href="https://github.com/seriousm4x/UpSnap/commits/master"><img src="https://img.shields.io/github/last-commit/seriousm4x/upsnap" /></a>
    </div>
</div>

## ✨ Features

- 🚀 One-Click Device Wake-Up Dashboard
- ⏰ Timed Events via Cron for Automation
- 🔌 Ping Any Port You Choose
- 🔍 Discover Devices with Network Scanning (nmap required)
- 📌 Optional IP Address Tracking per Device via periodic ARP scan (nmap required, no extra privileges beyond network scanning)
- 📡 Support using a Fully Qualified Domain Name for a device (refer to the Wiki: [Use FQDN](https://github.com/seriousm4x/UpSnap/wiki/Use-FQDN))
- ❎️ Shutdown Devices with a Custom Command
- 👤 Secured User Management
- 📋 Superuser Logs Page
- 🌐 i18n support for [these](/frontend/translations) languages
- 🎨 35 Themes
- 🐳 [Docker images](https://github.com/seriousm4x/UpSnap/pkgs/container/upsnap) for amd64, arm64, arm/v7, arm/v6
- 🏠 Self-Hostable

## 📸 Screenshots

| Silk                               | Dim                               |
| ---------------------------------- | --------------------------------- |
| ![](/assets/home-light.webp)       | ![](/assets/home-dark.webp)       |
| ![](/assets/account-light.webp)    | ![](/assets/account-dark.webp)    |
| ![](/assets/new-manual-light.webp) | ![](/assets/new-manual-dark.webp) |
| ![](/assets/new-scan-light.webp)   | ![](/assets/new-scan-dark.webp)   |
| ![](/assets/settings-light.webp)   | ![](/assets/settings-dark.webp)   |
| ![](/assets/users-light.webp)      | ![](/assets/users-dark.webp)      |

## 🚀 Run the binary

Just download the latest binary from the [release page](https://github.com/seriousm4x/UpSnap/releases) and run it.

```bash
sudo ./upsnap serve --http=0.0.0.0:8090
```

To run as non-root (Linux only), refer to the Wiki: [Use non-root user](https://github.com/seriousm4x/UpSnap/wiki/Use-non%E2%80%90root-user)

For more options check `./upsnap --help` or visit [PocketBase documentation](https://pocketbase.io/docs).

## 🐳 Run in Docker

Just pull and run the image: `docker run --network=host seriousm4x/upsnap:latest` or you can use the [docker-compose](docker-compose.yml) example. See the comments in the file for customization.

To run as non-root, refer to the Wiki: [Use non-root user](https://github.com/seriousm4x/UpSnap/wiki/Use-non%E2%80%90root-user)

Set `UPSNAP_LOG_LEVEL` to `DEBUG`, `INFO`, `WARN`, `ERROR`, or `OFF` to control UpSnap console logs. Default is `INFO`. PocketBase log persistence and retention remain configurable in PocketBase settings. PocketBase's `--dev` mode prints all logs regardless of this setting.

### Change port (Docker)

If you want to change the port from 8090 to something else, change the following (5000 in this case):

```yml
environment:
  - UPSNAP_HTTP_LISTEN=0.0.0.0:5000
```

## ❎️ Shutting Down Devices

To shutdown devices, refer to the Wiki: [How to use shutdowns](https://github.com/seriousm4x/UpSnap/wiki/How-to-use-shutdowns)

## Reverse Proxy

**Caddy example**

```
upsnap.example.com {
    reverse_proxy localhost:8090
}
```

## Run in sub path

You can run UpSnap on a different path than `/`, e.g. `/upsnap-sub-path/`. To do this in caddy, set the following:

```
http://localhost:8091 {
    handle /upsnap-sub-path/* {
        uri strip_prefix /upsnap-sub-path
        reverse_proxy localhost:8090
    }
}
```

Or nginx:

```
http {
    server {
        listen 8091;
        server_name localhost;
        location /upsnap-sub-path/ {
            proxy_pass http://localhost:8090/;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

Paths must end with a trailing `/`.

## 🐧 Install from the [AUR](https://aur.archlinux.org/packages/upsnap-bin)

```bash
yay -Sy upsnap-bin
```

## 🔒 User permissions

UpSnap offers unique access for each user, per device. While admins have all permissions, they can assign specific rights to users such as displaying/hiding a device, accessing device editing, deleting and powering devices on/off. See the last screenshot in the [📸 Screenshots section](#-screenshots).

## 🌍 Exposing to the open web

Although UpSnap has user authorisation, it is **not recommended to expose it to the open web** and make it accessible by everyone!

**Reason**: The shutdown device command is basically a command piped to #sh (possibly as root user). If anyone gains unauthorized access and can abuse this API route in any way, the attacker has access to a (root) shell on your local network.

**Recommended**: If you need access from outside your network, please use a VPN. Wireguard or OpenVPN is your way to go.

## 🌐 Help translating

UpSnap is available in the following languages so far:

- 🇸🇦 **Arabic (Saudi Arabia)** (ar-SA)
- 🇧🇬 **Bulgarian (Bulgaria)** (bg-BG)
- 🇨🇿 **Czech (Czech republic)** (cs-CZ)
- 🇩🇪 **German (Germany)** (de-DE)
- 🇬🇷 **Greek (Greece)** (el-GR)
- 🇺🇸 **English (United States)** (en-US)
- 🇪🇸 **Spanish (Spain)** (es-ES)
- 🇫🇷 **French (France)** (fr-FR)
- 🇮🇳 **Hindi (India)** (hi-IN)
- 🇮🇩 **Bahasa (Indonesia)** (id-ID)
- 🇮🇹 **Italian (Italy)** (it-IT)
- 🇯🇵 **Japanese (Japan)** (ja-JP)
- 🇰🇷 **Korean (Republic of Korea)** (ko-KR)
- 🇳🇴 **Norwegian (Norway)** (nb-NO)
- 🇳🇱 **Dutch (Netherlands)** (nl-NL)
- 🇵🇱 **Polish (Poland)** (pl-PL)
- 🇧🇷 **Portuguese (Brazil)** (pt-BR)
- 🇵🇹 **Portuguese (Portugal)** (pt-PT)
- 🇷🇺 **Russian (Russia)** (ru-RU)
- 🇺🇦 **Ukrainian (Ukrain)** (uk-UA)
- 🇻🇳 **Vietnamese (Vietnam)** (vi-VN)
- 🇨🇳 **Chinese (China)** (zh-CN)
- 🇹🇼 **Chinese (Taiwan)** (zh-TW)

**If you want to contribute and help translating, check the wiki: [How to add languages](https://github.com/seriousm4x/UpSnap/wiki/How-to-add-languages)**

## 🔧 Help developing

Fork this branch and clone it.

1. Start backend

```bash
cd backend
go mod tidy
go run main.go serve
```

2. Start frontend

```bash
cd frontend
pnpm i
pnpm run dev
```

Open up [http://localhost:5173/](http://localhost:5173/), create an admin user and add some devices.

</details>
