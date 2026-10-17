# Batman PCVR

Play **your own** Meta Quest copy of *Batman: Arkham Shadow* on a Windows PC in VR, through Meta Quest Link or SteamVR.

> Unofficial fan project. Not affiliated with, endorsed or sponsored by Camouflaj, Meta, Warner Bros. Games or DC,
> or by the Refract authors. You need the game bought from the Meta Store and installed on your Quest. This
> repository contains no game files, and the game itself is never modified.

Source code and scripts will be added here later.

---

## What it is

*Batman: Arkham Shadow* was released only for Meta Quest, as a standalone Android VR app. This project runs that
same Quest game on a PC, so it can use a desktop graphics card: higher resolution, sharper textures and around
**116-119 fps** at 140% resolution (measured on an RTX 4070 SUPER), shown in the headset through Quest Link or
Air Link.

It is a **compatibility layer**, not a port, a decompile or a crack:

- It runs the original, unmodified game files (APK and game data) from your own Quest.
- It checks ownership before the game starts.
- It ships no game content.

## How it works

The Quest game is an Android app built for ARM64 processors. On the PC it runs inside a small Android environment,
built on **[Refract](https://github.com/MIXIDtheSilly/Refract)**:

1. **CPU translation:** the game's ARM64 code is translated to x86-64 while it runs, by Dynarmic or Digitalis (the
   faster one, used by default).
2. **Android system:** the Android system libraries and services the game expects come from Google's own Android 14
   system image, plus a compatibility layer for the services it calls.
3. **Graphics:** the game draws with Vulkan. Its calls go straight to the PC's graphics card. Textures are stored in
   ASTC format, which PC GPUs can't read, so they are converted while loading.
4. **VR output:** a host bridge receives the game's two eye images through shared GPU textures and sends them to the
   headset with OpenXR (Meta Quest Link directly, or SteamVR). Head and controller tracking go the other way.

## What this project adds on top of Refract

- **Meta Quest Link stereo fix:** the Meta OpenXR runtime ignores the field of view the app asks for, which made the
  two eyes fail to line up. The bridge now crops each eye image to the runtime's own per-eye field of view.
- **In-headset graphics panel:** hold A + X for a second to change resolution, texture detail, filtering, sharpening,
  CPU translator, VR runtime and frame pacing. Settings are shared with the launcher; some apply live.
- **Sharper picture:**
  - FSR1 RCAS sharpening on both eyes, about 0.14 ms per frame.
  - A small Vulkan layer that forces 16x anisotropic filtering, for floors and walls seen at an angle.
- **Performance and stability:**
  - A texture mip limit that stops the game running out of video memory.
  - A watchdog that recovers the GPU image path if it drops.
  - "CPU boost", which raises the game's busiest threads to high priority on the fastest cores.
- **Desktop stats overlay:** the performance panel (frame rate, GPU and CPU) shows on the desktop mirror window as
  well as in the headset.
- **Experimental BC3 textures:** an option to store textures as BC3, a quarter of the video memory, to allow
  full-detail textures. It is still being tested.
- **Batman PCVR app:** a one-window Windows app with nothing to install. You choose your APK and game-data folder and
  press Play. It then:
  - checks that both are the genuine Meta Store files (hash and signature; modified or repacked APKs are refused);
  - checks **on your own Quest, over USB**, that the game was installed from the Meta Store, valid for 30 days of
    play over Air Link;
  - downloads Google's Android files after you accept their licence, prepares the game and starts it.
- **Launcher window:** Play button, live checks (game files, Quest Link, ownership), graphics settings window, and
  dark/light mode.

## Rules this project keeps

- No game files, APKs, OBBs, saves or decompiled code are shared, ever.
- No ownership bypass: ownership is always checked, and modified APKs are refused.
- Only Google- or SideQuest-signed `adb.exe` is used to talk to the Quest.
- Nothing is downloaded without the user accepting it, and only from the original sources.

## Credits

This project is built on **[Refract](https://github.com/MIXIDtheSilly/Refract)** by **MIXIDtheSilly** and its
contributors. Refract does the hard part: running Android VR games natively on Windows, with ARM64 translation, the
Android runtime, Vulkan graphics and the OpenXR host bridge. Batman PCVR is a set of changes and tools on top of
Refract, made for one game. All credit for Refract itself goes to its authors. Please support and star the original
project.

Refract builds on other open-source work, credited in Refract's own `CREDITS.md`. That includes
Dynarmic and Digitalis (ARM64 translation), the
[ARM astc-encoder](https://github.com/ARM-software/astc-encoder), the
[Khronos OpenXR SDK](https://github.com/KhronosGroup/OpenXR-SDK) and
[Vulkan-Headers](https://github.com/KhronosGroup/Vulkan-Headers), and AMD's FidelityFX (RCAS sharpening).
Android system files come from Google's Android SDK under its own licence.

*Batman: Arkham Shadow* is © Warner Bros. Entertainment Inc. and DC, developed by Camouflaj and published by Oculus
Studios (Meta). Batman and all related characters and elements are trademarks of DC.

## Licence

Like Refract, this project's code is licensed under the GNU General Public License v3.0 or later. The full licence
text and the third-party notices will be added together with the source files.
