# Zephyr Project Template

This template can be used to quickly work with zephyr, it starts by running the devcontainer

Works out of the box:
West, Pytest, Renode, Qemu, nRF Connect Extension (vscode)

This repo is meant to easily create and add more apps, hence it is structured with the folder apps, and everything inside stands alone

You can easily extend by adding your preferred SDK (eg: nRF Connect, different Zephyr SDK versions, your companies private Zephyr fork)

## Getting Started

Clone then Rebuild and Reopen in Container. Let the container to build itself, and the default Zephyr SDK to be downloaded

## Build

### ESP32

Build & Run

```bash
west build -s apps/emul-shell-gpio -p always -b esp32s3_devkitc/esp32s3/procpu --no-sysbuild \
&& west flash --runner esp32 --esp-device /dev/ttyUSB0 \
&& python3 -m serial.tools.miniterm --raw /dev/ttyUSB0 115200
```

### Native Sim

Build & Run

```bash
west build -s apps/emul-shell-gpio -p always -b native_sim/native \
&& ./apps/emul-shell-gpio/build/zephyr/zephyr.exe
```

Build & Run tests manually

```bash
west build -d apps/emul-shell-gpio/build_test -s apps/emul-shell-gpio/tests/emul_button_toggle -p always -b native_sim/native \
&& ./apps/emul-shell-gpio/build_test/zephyr/zephyr.exe
```

Build & Run tests via Twister

```bash
west twister -T apps/emul-shell-gpio/tests/emul_button_toggle -p native_sim/native
```
