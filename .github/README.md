# FristOneRR-Wrapperv1

A Vulkan-on-Vulkan wrapper for Winlator, tuned for the
**[FristOneRR PanVK driver](https://github.com/FristOneRR/FristOneRR-Panvk-Driver)** on Mali GPUs (kbase).
With it, **DXVK 2.x runs D3D9 and D3D11** (feature level 11_1) on the FristOneRR driver.

It is a fork of **[leegao/bionic-vulkan-wrapper](https://github.com/leegao/bionic-vulkan-wrapper)**
at tag **v0.0.5r5**. Almost all of the code is leegao's work (and Mesa's); this fork changes
four files. Thank you, leegao!

> **Status:** first release, made to ship with FristOneRR PanVK **beta 1.2.0**.

---

## What is changed (compared to leegao v0.0.5r5)

The full history is kept, so you can see every change with
`git diff v0.0.5r5 fristonerr-wrapper-v1`.

| Commit | Files | Change |
|---|---|---|
| Termux build fix | `src/util/anon_file.c`, `src/util/rand_xor.c` | Use libc `memfd_create()` / `getrandom()` instead of raw `syscall()`. No functional change. |
| Skip missing SPIRV-Tools passes | `src/vulkan/wrapper/spirv_edit.cpp` | leegao's Mali SPIRV-Tools passes (RemoveClipCullDist, FixMaliSpecConstantComposite, MaliOptimizationBarrier) are not in the stock Termux SPIRV-Tools, so they are skipped. The last two only run on the Arm proprietary driver. RemoveClipCullDist would run on FristOneRR PanVK; without it, clip distances reach the driver unchanged. |
| Wrapper changes | `src/vulkan/wrapper/wrapper_device.c`, `wrapper_physical_device.c`, `src/vulkan/wsi/wsi_common.c` | Disable `VK_KHR_present_id`, `VK_KHR_present_wait` and `VK_KHR_dynamic_rendering` (the extension; Vulkan 1.3 dynamic rendering stays). Drop wait semaphores in `vkQueueSubmit` / `vkQueueSubmit2` (up to 8 submits). Don't signal the acquire semaphore/fence in `vkAcquireNextImage2`. Print `FristOneRR-wrapper` once to the log. |

With the leegao wrapper, DXVK 2.x crashed on the first frame on FristOneRR PanVK. These changes fix
that.

### The PanVK name on screen

With this wrapper, Winlator and the DXVK HUD show the **real driver** instead of a generic
"Wrapper" device, for the first time with FristOneRR PanVK:

```
Mali-G57 MC2
Driver:  panvk
Version: FristOneRR 1.2.0 (Mesa 26.3.0-devel)
```

So you can see at a glance that the game is running on PanVK, and which FristOneRR version.

---

## Limitations

- **Made for FristOneRR PanVK.** The driver runs one queue strictly in order, so the dropped waits
  are safe there. On other drivers (for example the stock Mali driver) you may see flicker or
  corruption.
- **64-bit games only** for now. 32-bit games don't start with this wrapper yet; use the leegao
  wrapper with DXVK 1.10.3 for them.
- Apps that wait on the swapchain **acquire fence** can hang (DXVK uses semaphores, so it is fine).
- GPU memory size comes from the driver (FristOneRR 1.2.0 reports 25 % of RAM, 1–3 GB).

---

## Install

The wrapper is **not** installed through the Winlator driver menu. You replace the wrapper
library inside the container.

1. Download the wrapper zip from [Releases](../../releases) and **extract it**.
   Inside is `libvulkan_wrapper.so`; you need this file, not the zip.
2. Start the container and open a file manager in it (for example Winlator's file manager or
   Explorer).
3. Go to drive **Z:** → `usr` → `lib`.
4. Rename the existing `libvulkan_wrapper.so` to `libvulkan_wrapper.so.bak` (so you can go back).
5. Copy the new `libvulkan_wrapper.so` into `Z:\usr\lib\`.
6. In the container settings, choose **Panvk-Mali-G57-beta_1.2.0** as the Vulkan driver, use
   **DXVK 2.x** (tested: 2.3.1, 2.4.1-gplasync) and set **BCn texture emulation to "none"**.
7. Restart the container. The Wine log should contain one line: `FristOneRR-wrapper`.

To undo: delete the new file and rename `libvulkan_wrapper.so.bak` back.

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
and all of them are there (DXVK 2.x starts at D3D11 feature level 11_1).

---

## Building (Termux)

Same as leegao's wrapper, but linked against the SPIRV-Tools static libraries from Termux
(`libSPIRV-Tools.a` and `libSPIRV-Tools-opt.a` in `$PREFIX/lib`):

```sh
meson setup build -Dvulkan-drivers=wrapper -Dgallium-drivers= -Dplatforms=android,x11 -Dbuildtype=release
ninja -C build
# output: build/src/vulkan/wrapper/libvulkan_wrapper.so
```

---

## Credits

| Who | Contribution |
|---|---|
| **leegao** / [bionic-vulkan-wrapper](https://github.com/leegao/bionic-vulkan-wrapper) | The wrapper this fork is built on (nearly all of the code) |
| **Mesa developers** | Vulkan runtime and WSI code |
| **FristOneRR** | The changes listed above, testing with FristOneRR PanVK |
| **Claude** (AI assistant by Anthropic) | Debugging help and review |

## License

MIT, same as Mesa and leegao's wrapper. See the license headers in each file.
