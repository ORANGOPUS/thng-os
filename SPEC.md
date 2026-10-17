# THNG — System & Design Spec

**THNG, The Human Node Generator**, is a free, open-source (MIT) spatial operating system. It grows out of [Spatial Desktop](https://github.com/ORANGOPUS/spatial-desktop) and turns every device you own into a *node* in one shared space.

Spec version: `thng-ds-2` (Once UI) · Status: draft

> **Status key**, used throughout:
> - **Available today**: ships in the current Spatial Desktop release.
> - **Planned**: designed, not built yet.
> - **Community-supported**: not tested by maintainers; PRs welcome.
> - **Pending**: waiting on maintainer input.

---

## 1. Principles

1. **Spatial first.** Everything with pixels is a panel in a scene. There's no flat desktop to fall back to.
2. **Capabilities, not promises.** A node offers only what its device can really do. When a feature isn't available, the UI says why instead of showing a broken control.
3. **Free means free.** No paid tiers, accounts or locked features. Sponsorship never unlocks anything.
4. **Every screen is a node.** That covers desktops, phones, headsets, watches, and jailbroken or rooted devices, to the extent each one allows.
5. **Generated, not shipped.** Scenes are generated in code and drawn in shaders, so there are no heavy asset packs.

## 2. Architecture

| Layer | Owns | Built on |
| --- | --- | --- |
| **Node** | Capability detection, discovery, updates | Electron / Capacitor shells from Spatial Desktop |
| **Scene** | Sky, platform, lighting, audio, layout rules | IWSDK + WebXR; shaders generated in code |
| **Panel** | Pixels + input passthrough | PipeWire capture, wayvnc, VNC (VMs) |
| **Service** | First-party apps opened as panels | Web panels |

### 2.1 Node roles

Every node takes one role at a time:

- **Host**: renders the scene and holds the panels (a desktop, a laptop or a standalone headset).
- **Viewer**: steps into the host's scene (a WebXR headset or a browser tab).
- **Companion**: controls the scene without rendering it (a watch or a phone). *Planned.*

Nodes discover each other on the local network. Discovery and pairing are **planned**; today each device runs on its own.

### 2.2 Capability matrix

| Capability | Needs |
| --- | --- |
| Render scene | WebGL 2 |
| Immersive VR | A WebXR browser and headset |
| Hyprland panel | Linux, Hyprland 0.56+, wayvnc |
| Window panels | OS-level screen capture (PipeWire portal on Linux) |
| Windows VM panel | Omarchy Windows VM (VNC) |
| Companion control | A network connection to a host node *(planned)* |

## 3. Scenes

| Scene | Status | Purpose | Default layout | Audio |
| --- | --- | --- | --- | --- |
| **Deep Space** | Available today | Everyday desktop; nebula, neural core, platform | Arc (also grid, stack) | Ambient generative, optional |
| **Arena** | Available today | Rogue Protocol wave shooter | n/a (game) | Game audio |
| **Workbench** | Planned | Long work sessions; dimmed sky, main + side panels | Main + sides | Muted |
| **Stage** | Planned | thngPlay viewing and broadcasting | Theatre / broadcaster | Spatial stream audio |
| **Focus** | Planned | One panel, nothing else | Single | Off |

Scene rules:

- Panels persist when you switch scenes, and each scene re-lays them out.
- Switching scenes is a crossfade of at most 400ms. With reduced motion it's an instant cut.
- Every scene has to hold frame rate through Spatial Desktop's adaptive render scale.

## 4. Services

| Service | Status | Summary |
| --- | --- | --- |
| **FORG** | Pending | Ships with THNG. **Description pending: maintainers to supply.** |
| **thngPlay** | Available today (thng.my) | Interactive live streaming with sub-second latency over FTL |
| **Spatial Desktop** | Available today | Hyprland, apps and the Windows VM as panels in WebXR |
| **Rogue Protocol** | Available today | Built-in wave shooter; mouse, touch or VR |

All services are free.

## 5. Devices

| Device | Status | Notes |
| --- | --- | --- |
| Linux: Omarchy / Hyprland 0.56+ | Available today | Every feature |
| Linux: other Hyprland setups | Available today | Most features |
| Windows 10/11 | Available today | Scene + window panels where capture is allowed; unsigned |
| macOS 12+ | Available today | Scene; universal build; not notarized |
| Android 7+ | Available today | Offline APK |
| Meta Quest | Available today | WebXR in Quest Browser (real VR); APK runs as a 2D window |
| Other WebXR headsets | Available today | Browser |
| Any modern browser | Available today | spatialdesktop.thng.my/app |
| THNG OS bootable distro | Planned | Live USB / install alongside current OS; ISO not published |
| iPhone / iPad | Planned | Native node app |
| Wear OS / Apple Watch | Planned | Companion: scene switch, panel focus, notifications |
| Steam Deck (SteamOS) | Community-supported | Linux build in desktop mode, no Hyprland features |
| Rooted Android / custom ROMs | Community-supported | Standard APK |
| Jailbroken iOS | Community-supported | No official build |
| Open-firmware watches (AsteroidOS, PineTime) | Community-supported | Remote-control node |

## 6. Install paths

| Path | Status | How |
| --- | --- | --- |
| AppImage (Linux) | Available today | `chmod +x` and run; self-updates |
| .deb (Linux) | Available today | `sudo apt install ./Spatial-Desktop-linux-amd64.deb` |
| From source | Available today | `git clone` → `npm install` → `npm run dev` (Node 20.19+ / 22.12+) |
| Windows installer | Available today | SmartScreen: *More info → Run anyway* |
| macOS disk image | Available today | First launch: *right-click → Open* |
| Android / Quest APK | Available today | Allow unknown sources / sideload |
| Browser / WebXR | Available today | Nothing to install |
| THNG OS live USB | Planned | Boots straight into Deep Space |
| Watch companion | Planned | From the watch's app store / sideload |

Downloads come from the latest [Spatial Desktop release](https://github.com/Cheesiq/spatial-desktop/releases/latest).

## 7. Pricing

Everything is **free**: THNG OS, Spatial Desktop, every service and every companion node. Support comes through [GitHub Sponsors](https://github.com/sponsors/ORANGOPUS) *(to configure)* and contributions. Sponsorship pays for code-signing, test hardware and maintainers' time.

## 8. Input

| Action | Desktop | VR |
| --- | --- | --- |
| Search | `/` | — |
| Cycle layout | `L` | — |
| Switch scene | `S` *(planned)* | — |
| Play Rogue Protocol | `G` | Play button |
| Leave game | `Esc` | B / Y |
| Move a panel | Drag frame | Grab frame |
| Bring a panel close | Click | Point + trigger |

## 9. Design tokens

THNG's design system is the [Once UI](https://once-ui.com) library, `@once-ui-system/core` **2.1.0** (MIT). Its global stylesheets are vendored in `vendor/once-ui/` (`tokens.css`, `styles.css`, `LICENSE`) and linked before the page's own styles. The React components are not used: they need Next.js 15.5+ and Sass, and the site is a static page.

### Theme

Set as attributes on `<html>`:

| Attribute | Value | Effect |
| --- | --- | --- |
| `data-theme` | `dark` | Dark scheme |
| `data-neutral` | `gray` | Pure gray neutrals |
| `data-brand` | `aqua` | Brand scale. Once UI's aqua is the match for THNG cyan `#5EE6D0`; its `cyan` is a sky blue |
| `data-accent` | `violet` | Secondary / *Planned* |
| `data-surface` | `filled` | Opaque card surfaces |
| `data-solid` | `color` | Solid buttons use the brand colour |
| `data-border` | `playful` | Radius scale |
| `data-transition` | `all` | Transitions on |
| `data-desktop-size` / `-tablet-size` / `-mobile-size` | `16` | 16px root size |

Switching the brand is a one-attribute change, for example `data-brand="cyan"`.

### Colour

THNG's short names are aliases onto Once UI's semantic tokens:

| THNG alias | Once UI token | Resolves to (dark) | Use |
| --- | --- | --- | --- |
| `--bg` | `--page-background` | gray-100 `#0a0a0a` | Page / sky ground |
| `--surface` | `--surface-background` | gray-200 | Cards, panels |
| `--surface-2` | `--neutral-alpha-weak` | gray-600 at 15% | Hover, raised |
| `--line` | `--neutral-alpha-medium` | gray-600 at 30% | Hairlines |
| `--text` | `--neutral-on-background-strong` | `#ffffff` | Body text |
| `--muted` | `--neutral-on-background-weak` | gray-700 `#b2b2b2` (≈9:1) | Secondary text |
| `--accent` | `--brand-on-background-weak` | aqua-700 `#01c7c3` (≈9:1) | Links, icons, focus, *Available* |
| `--accent-solid` | `--brand-solid-strong` | aqua-500 `#0b8184` | Primary buttons, selected tabs (white text ≈4.7:1) |
| `--violet` | `--accent-on-background-weak` | violet-700 `#c7a2fd` | *Planned* |
| `--warn` | `--warning-on-background-weak` | yellow-700 `#fe9c0b` | *Community*, errors, Arena |

Status always comes with a **text label and a shape**: a filled dot means available, a ring means planned, a diamond means community-supported and a dashed square means pending. Status marks render as Once UI–style tags (pill, tinted `*-alpha-weak` fill, `*-alpha-strong` border).

### Type

| Role | Family | Once UI variable |
| --- | --- | --- |
| Display / headings | Geist 600, tracking −0.025 to −0.035em | `--font-heading` |
| Body | Geist 400, 16–18px, line height 1.6 | `--font-body` |
| Labels | Geist 500 | `--font-label` |
| Mono | Geist Mono | `--font-code` |

Geist is loaded from Google Fonts because Once UI's own font loading is Next.js-only. Hero: `clamp(44px, 7vw, 80px)`.

### Space, shape, motion

- Spacing: Once UI `--static-space-*`: 4 · 8 · 16 · 24 · 32 · 48 · 64 · 104
- Radius (`playful`): `--radius-xs` 4px · `--radius-m` 12px controls · `--radius-l` 16px cards · `--radius-xl` 20px figures · `--radius-full` pills
- Shadows: Once UI `--shadow-xs` on cards, `--shadow-l` on figures and toasts.
- Background: Once UI–style grid + dots + brand glow, masked to the top of each page.
- Icons: one inline-SVG stroke family, 1.5px stroke, round caps.
- Motion: Once UI durations (`--transition-duration-macro-short` 150ms, `micro-medium` 200ms, `micro-long` 400ms), ease-out on enter and ease-in on exit. All motion respects `prefers-reduced-motion`.

## 10. Accessibility

- Text contrast is at least 4.5:1, and icons and status marks at least 3:1.
- Every control is reachable by keyboard, with a visible 2px aqua focus ring.
- Touch targets are at least 44×44px.
- Tabs follow the WAI-ARIA tab pattern (arrow keys, Home and End).
- In XR, panels can be grabbed by their frame from either hand, and nothing depends on colour alone.

## 11. Open items

- [ ] FORG: description, scope and panel behaviour.
- [ ] Set up GitHub Sponsors for ORANGOPUS.
- [ ] Node discovery / pairing protocol.
- [ ] THNG OS base distro choice and ISO pipeline.
- [ ] Watch companion apps.
