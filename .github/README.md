# FristOneRR-Wrapper

A Vulkan-on-Vulkan wrapper for Winlator and similar apps, made for the
**[FristOneRR PanVK driver](https://github.com/FristOneRR/FristOneRR-Panvk-Driver)** on Mali GPUs (kbase).
With it, **DXVK runs D3D8–D3D11** and **vkd3d-proton runs D3D12**, for 32-bit and 64-bit games.

> **Status: 3.0.** Made for FristOneRR PanVK 3.0. It is not meant for the stock Mali driver.

It is a fork of **[leegao/bionic-vulkan-wrapper](https://github.com/leegao/bionic-vulkan-wrapper)**
(tag v0.0.5r5). Most of the code is leegao's work and Mesa's. Thank you, leegao!

---

## Download and SHA256

Download only from the [Releases](../../releases) page. Check the SHA256 before installing
(on Android: a file-hash app, or Termux: `sha256sum <file>`).

| File | SHA256 |
|---|---|
| `FristOneRR-Wrapper-3.0.zip` (wrapper + `install.bat` + `restore.bat`) | `6bbfe5b252df2ea038e7bb8b360b59b11107da3465ed1d951e97fba4449e9d41` |
| `FristOneRR-Wrapper-3.0.tzst` (same wrapper, tzst package) | `3d039d0669fdbb869c8493780afd57f9168d2891126755ecbb944d99fc8c63f5` |
| `libvulkan_wrapper.so` (inside the zip / tzst) | `f9350e6b0c936f383f160b20a379ef714bce8e8e89752472d4f087cca480acfb` |

If the SHA256 is different, the file is not ours. Don't install it.

---

## What's new in 3.0

- **D3D12 through vkd3d-proton** works with FristOneRR PanVK 3.0.
- **One-click install:** `install.bat` installs the wrapper and backs up the app's own wrapper first.
  `restore.bat` puts the app's wrapper back.

Earlier (2.0): 32-bit games, better BCn texture handling, the real driver name (`panvk`, `FristOneRR …`)
in the DXVK HUD and the app.

---

## Install

You need the **[FristOneRR PanVK driver](https://github.com/FristOneRR/FristOneRR-Panvk-Driver)**
installed and selected in the container first.

### Easy install (recommended): `install.bat`

1. Download `FristOneRR-Wrapper-3.0.zip` and **extract it** into a folder the container can see
   (for example your phone's `Download` folder, which is usually drive **D:** in Winlator).
   Keep `install.bat`, `restore.bat` and `libvulkan_wrapper.so` together in the same folder.
2. Start the container, open that folder in the Wine file manager and **double-click `install.bat`**.
   - The first time, it saves the app's own wrapper to `Z:\usr\lib\FristOneRR-backup\`.
   - Then it installs FristOneRR-Wrapper 3.0. Wait for **"Done"** and press a key.
3. **Close the container and start it again.** The new wrapper is loaded on the next start.

**Go back to the app's wrapper:** double-click **`restore.bat`**, then restart the container.
Running `install.bat` again later keeps the first backup; it never overwrites it.

### Other ways

- **`FristOneRR-Wrapper-3.0.tzst`:** for apps that install wrappers from a `.tzst` file
  (contains `usr/lib/libvulkan_wrapper.so`).
- **By hand:** copy `libvulkan_wrapper.so` into the container at **`Z:\usr\lib\`**,
  replacing the existing file (keep a backup).

---

## Supported apps

| App | Status |
|---|---|
| **Winlator Ludashi** | ✅ Recommended |
| Bannerlator | ✅ |
| Winlator Mali 1.2 (adrenotools) | ✅ |
| GameNative | ✅ |

---

## Known issues

- Some heavy D3D12 games can hang while loading. Use the game's D3D11 mode for now; a fix is planned for 3.5.
- Made for FristOneRR PanVK only. With other drivers it may not work correctly.

Report problems in the driver's [Issues](https://github.com/FristOneRR/FristOneRR-Panvk-Driver/issues)
(include phone, app, DXVK / vkd3d-proton version and the Wine log).

---

## Credits

| Who | Contribution |
|---|---|
| **leegao** | [bionic-vulkan-wrapper](https://github.com/leegao/bionic-vulkan-wrapper), the base of this wrapper, and advice throughout the project |
| **Mesa developers** | The Vulkan runtime and WSI code the wrapper is built on |
| **Claude** (AI assistant by Anthropic) | Development help, debugging and code review |
| **All testers** | Reports and logs |

## support 

☕ Support my work

If you enjoy my projects and would like to support my work, you can buy me a coffee on Ko-fi! Every project I create is and will always remain free, but any support is greatly appreciated and helps me keep working on new projects and improvements.

Thank you for your support!

[Support on Ko-fi](https://ko-fi.com/fristonerr)

## License

Built from Mesa and bionic-vulkan-wrapper (MIT license).
