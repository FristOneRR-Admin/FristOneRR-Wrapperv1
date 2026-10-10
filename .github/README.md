# FristOneRR-Wrapper 2.0

A Vulkan-on-Vulkan wrapper for Winlator, made for the
**[FristOneRR PanVK driver](https://github.com/FristOneRR/FristOneRR-Panvk-Driver)** on Mali GPUs (kbase).
With it, **DXVK runs D3D8, D3D9, D3D10 and D3D11 for both 32-bit and 64-bit games** on the FristOneRR driver.

It is a fork of **[leegao/bionic-vulkan-wrapper](https://github.com/leegao/bionic-vulkan-wrapper)**
at tag **v0.0.5r5**. Almost all of the code is leegao's work (and Mesa's). Thank you, leegao!

---

## Official download and SHA256

Download only from the [Releases](../../releases) page of this repository.
To check that your file is the original, compare its SHA256 with the table below
(on Android: a file-hash app, or Termux: `sha256sum <file>`).

| File | SHA256 |
|---|---|
| `FristOneRR-Wrapper-2.0.zip` | `8e3254fff8d0604d4e30b5586683827beb387aa4cd229d50e3ce4ae3c3ee0686` |
| `FristOneRR-Wrapper-2.0.tzst` | `0f7bde510d5f902f3295c847ebabc7d95183b9b82f98bf114b9cd3e4f0dd2980` |
| `libvulkan_wrapper.so` (inside the zip / tzst) | `ab4f32ad587d635dd56a8886519697dbe957e6ead2229086dd00310f9ab20a89` |

If the SHA256 is different, the file is not ours. Don't install it.

---

## What's new in 2.0

- **32-bit games** now work: D3D8, D3D9, D3D10 and D3D11 run for 32-bit and 64-bit games.
- **D3D12** (vkd3d-proton) starts but isn't complete yet. It is being worked on for the next update.
- **Better BCn texture emulation:** texture arrays, cubemaps and 3D textures are decoded correctly
  (before, only the first layer was right and some games crashed with a GPU page fault).
- Fixes for wrong BC7 blocks on Mali, and lower memory use while uploading compressed textures.

---

## Install

The wrapper is **not** installed through the Winlator driver menu. There are two packages with the
same `libvulkan_wrapper.so` inside:

| File | For |
|---|---|
| `FristOneRR-Wrapper-2.0.zip` | Apps where you copy the `.so` into the container yourself (steps below) |
| `FristOneRR-Wrapper-2.0.tzst` | Apps that install wrappers from a `.tzst` file (contains `usr/lib/libvulkan_wrapper.so`) |

**With the zip:**

1. Download `FristOneRR-Wrapper-2.0.zip` from [Releases](../../releases) and **extract it**.
   Inside is `libvulkan_wrapper.so`; you need this file, not the zip.
2. Start the container and open a file manager in it (for example Winlator's file manager or
   Explorer).
3. Go to drive **Z:** → `usr` → `lib`.
4. Rename the existing `libvulkan_wrapper.so` to `libvulkan_wrapper.so.bak` (so you can go back).
5. Copy the new `libvulkan_wrapper.so` into `Z:\usr\lib\`.
6. In the container settings, choose **Panvk-Mali-G57-2.0-rc1** as the Vulkan driver and pick a DXVK
   version.
7. Restart the container. The Wine log should contain one line: `FristOneRR-wrapper 2.0`.

To undo: delete the new file and rename `libvulkan_wrapper.so.bak` back.

If a game crashes on start or the phone freezes, set **BCn texture emulation to "none"**.

---

## The PanVK name on screen

With this wrapper, Winlator and the DXVK HUD show the **real driver** instead of a generic
"Wrapper" device:

```
Mali-G57 MC2
Driver:  panvk
Version: FristOneRR 2.0-rc1 (Mesa 26.3.0-devel)
```

---

## Limitations

- **Made for FristOneRR PanVK.** It skips some swapchain/semaphore waits that this driver doesn't
  need. On other drivers (for example the stock Mali driver) you may see flicker or corruption.
- D3D12 is not complete yet.

---

## Why the extension count is lower

AIO Graphics Test → GPU Info shows about **133 extensions** with this wrapper, a bit fewer than
with some other wrappers. This is expected:

- This wrapper **turns off `VK_KHR_present_id`, `VK_KHR_present_wait` and
  `VK_KHR_dynamic_rendering`** on purpose. The first two caused DXVK 2.x to crash when presenting
  on FristOneRR PanVK; dynamic rendering is still available through Vulkan 1.3.
- The wrapper only passes through extensions it knows how to wrap, so some driver extensions are
  not shown to games (the same happens with leegao's wrapper).

A lower number does **not** mean fewer games work. DXVK only needs a specific set of extensions,
and all of them are there.

---

## Credits

| Who | Contribution |
|---|---|
| **leegao** / [bionic-vulkan-wrapper](https://github.com/leegao/bionic-vulkan-wrapper) | The wrapper this fork is built on (nearly all of the code) |
| **Mesa developers** | Vulkan runtime and WSI code |
| **FristOneRR** | The FristOneRR changes, testing with FristOneRR PanVK |
| **Claude** (AI assistant by Anthropic) | Development help, debugging and review |

## License

MIT, same as Mesa and leegao's wrapper. See the license headers in each file.
