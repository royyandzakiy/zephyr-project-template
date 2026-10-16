# Zephyr Project Template

[![Build](https://github.com/royyandzakiy/zephyr-project-template/actions/workflows/build.yml/badge.svg?branch=main)](https://github.com/royyandzakiy/zephyr-project-template/actions/workflows/build.yml)
[![Tests](https://github.com/royyandzakiy/zephyr-project-template/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/royyandzakiy/zephyr-project-template/actions/workflows/tests.yml)
[![Renode](https://github.com/royyandzakiy/zephyr-project-template/actions/workflows/renode.yml/badge.svg?branch=main)](https://github.com/royyandzakiy/zephyr-project-template/actions/workflows/renode.yml)
![Zephyr](https://img.shields.io/badge/Zephyr-v4.4.0-blue)
![Zephyr SDK](https://img.shields.io/badge/Zephyr%20SDK-1.0.1-blue)
[![Open in Dev Containers](https://img.shields.io/static/v1?label=Dev%20Containers&message=Open&color=blue&logo=visualstudiocode)](https://vscode.dev/redirect?url=vscode%3A%2F%2Fms-vscode-remote.remote-containers%2FcloneInVolume%3Furl%3Dhttps%3A%2F%2Fgithub.com%2Froyyandzakiy%2Fzephyr-project-template)

A starting point for Zephyr projects, built around a devcontainer. Each folder under
[`apps/`](apps/) is a standalone Zephyr application, so adding a project means adding a
folder.

Works out of the box in the container: west, Twister, pytest, Renode, QEMU and the
nRF Connect extension for VS Code. Other SDKs, such as nRF Connect SDK, another Zephyr
version or a private Zephyr fork, can be added alongside the default one.

| App | What it shows |
|---|---|
| [`emul-shell-gpio`](apps/emul-shell-gpio) | Button toggles an LED. A test-only shell command presses an emulated button, and pytest checks the result through Twister |
| [`renode-test`](apps/renode-test) | The same button and LED on the nRF52840 DK in Renode, tested by a Robot Framework suite |

## Getting started

1. Clone the repo and open it in VS Code.
2. Run **Dev Containers: Reopen in Container**. The first start downloads the default
   Zephyr tree and SDK into shared volumes, so later projects reuse them.
3. On macOS, pick the configuration in `.devcontainer/macos/` instead. It runs under
   emulation and has no USB passthrough.

All commands below run from the repo root, inside the container.

## native_sim

Build and run:

```bash
west build -s apps/emul-shell-gpio -p always -b native_sim/native
```

```bash
./apps/emul-shell-gpio/build/zephyr/zephyr.exe
```

Run the tests with Twister, the same as CI:

```bash
west twister -T apps/ -p native_sim
```

Or build the test image by hand:

```bash
west build -d apps/emul-shell-gpio/build_test -s apps/emul-shell-gpio/tests/emul_button_toggle -p always -b native_sim/native
```

```bash
./apps/emul-shell-gpio/build_test/zephyr/zephyr.exe
```

## Renode

```bash
west build -s apps/renode-test -d apps/renode-test/build -p always -b nrf52840dk/nrf52840
```

```bash
cd apps/renode-test/build && renode-test ../button_led.robot
```

See [`apps/renode-test`](apps/renode-test) for running it by hand.

## ESP32-S3

```bash
west build -s apps/emul-shell-gpio -p always -b esp32s3_devkitc/esp32s3/procpu --no-sysbuild
```

```bash
west flash --runner esp32 --esp-device /dev/ttyUSB0
```

```bash
python3 -m serial.tools.miniterm --raw /dev/ttyUSB0 115200
```

## nRF5340 DK

```bash
west build -s apps/emul-shell-gpio -p always -b nrf5340dk/nrf5340/cpuapp
```

```bash
west flash --runner nrfutil
```

```bash
python3 -m serial.tools.miniterm --raw /dev/ttyACM0 115200
```

Flashing from the container needs the probe visible in it: enable the `/dev` mount in
`.devcontainer/devcontainer.json`, and on Windows attach the probe to WSL with `usbipd`.

## CI

| Workflow | What it does |
|---|---|
| [`build.yml`](.github/workflows/build.yml) | Builds `emul-shell-gpio` for every board it has an overlay for |
| [`tests.yml`](.github/workflows/tests.yml) | Runs every Twister suite under `apps/` on native_sim |
| [`renode.yml`](.github/workflows/renode.yml) | Builds `renode-test` and runs its Robot suite in Renode |

All three run on GitHub-hosted runners in `ghcr.io/royyandzakiy/zephyr-devcontainer-ci`,
the CI sibling of the devcontainer image, so CI and local builds use the same Zephyr
and SDK.
