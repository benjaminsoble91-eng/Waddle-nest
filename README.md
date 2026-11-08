# Waddle Linux 🐧

A tiny Linux distro built from scratch, starring **Pip** the mint penguin.

- Linux kernel 6.12 + BusyBox, runs from RAM, with **saving** that Waddle handles all by itself (see below)
- Pip boot splash, then a **login screen** (on first boot Pip helps you make your account), then **Waddle
  Desktop**: our own from-scratch desktop (wallpaper, taskbar + launcher, clock, draggable windows, a real
  Terminal, Firefox in its own window, About, and Pip). Add `nodesktop` to the boot options to log in at the text console instead.
- **Accounts and passwords**, with `sudo` for admin tasks. Your home folder is `/home/<you>`.
- **Sound** (Intel HD Audio and USB sound cards), with a chime from Pip when you log in
- **Updates for everything**: `sudo waddle upgrade` updates your apps *and Waddle itself*
- **Firefox**, running on a real Wayland + GTK 3 stack that Waddle builds from source
- Wired Ethernet connects automatically (Intel, Realtek, Broadcom, Atheros drivers built in)
- **Waddle's own package system:** `sudo waddle install <app>` gets apps (eggs) from the Nest, Waddle's own
  signed repository; every egg is compiled by us from the original source code (see below)
- Commands: `waddle` (apps), `storage` (saving), `sound` (volume), `sudo` (admin), `newuser` (add a person),
  `pip` (code with Pip: Claude Code), `hipip` (say hi), `say <text>`, `pipstyle text|pixel`, `waddle-app` (desktop apps)
- Two Pip styles in the console: **text** (detailed character art, default) and **pixel** (block pixel art).
  Pick per boot with the "Waddle Linux (pixel Pip)" menu entry, for you with `pipstyle pixel`, or once with `pip --pixel`.

## Install on NixOS (adds a boot menu entry, keeps NixOS)

1. Copy this folder onto your PC as `/etc/nixos/waddle/`:
   ```
   sudo mkdir -p /etc/nixos/waddle
   sudo cp vmlinuz-waddle initramfs-waddle.cpio.gz waddle-version waddle.nix /etc/nixos/waddle/
   ```
2. Open `/etc/nixos/configuration.nix` and add the file to `imports`:
   ```nix
   imports = [
     ./hardware-configuration.nix
     ./waddle/waddle.nix
   ];
   ```
3. Rebuild:
   ```
   sudo nixos-rebuild boot
   ```
4. Reboot and pick **Waddle Linux** (or **Waddle Linux (pixel Pip)**) from the boot menu.
   **Waddle Linux (previous version)** boots the version from before the last update, in case one goes wrong.

**Already had Waddle 0.1?** Copy the new files over the old ones and rebuild the same way. NixOS now only puts
Waddle on the boot partition when the copy in `/etc/nixos/waddle/` is newer; after that Waddle updates itself.

**Boot menu flashes by too fast?** Add `boot.loader.timeout = 5;` to configuration.nix and rebuild.
(With systemd-boot you can also hold Space while the PC starts.)

**Using flakes?** Run `git add waddle/` in your config repo before rebuilding, or Nix won't see the files.

## Saving files

Waddle manages its own saving. No other OS is needed to set it up.

**First time:** boot Waddle, make your account, open a Terminal and run `sudo storage setup`. Pick a drive from the list and type `yes`.
Waddle creates **one file on that drive: `/waddle/storage.img`** (up to 4 GB, but it only takes up space as you
fill it) and keeps your home folder (`/home/<you>`), accounts, apps and Waddle settings in it. Nothing else on that drive is touched.

**Every boot after that:** Waddle finds its storage by itself and opens it.

Waddle looks for, in this order:
1. a partition labelled `waddle` (ext4): Waddle's own partition, for when it lives on a drive by itself
2. `/waddle/storage.img` on any Linux drive (ext4, btrfs incl. subvolumes, xfs, f2fs)

While looking, Waddle only *reads* drives (it doesn't even replay journals), so it's safe even if the other OS
wasn't shut down cleanly. It only writes inside its own file or partition.

- `storage` (or **About Waddle**) shows whether saving is on and how much space is used.
- Always shut down with **Power off**/**Restart** (menu or login screen) or `sudo poweroff`, not the power button, so storage
  is closed cleanly.
- Don't hibernate NixOS and then boot Waddle; shut NixOS down normally first.
- Windows (NTFS) and ZFS drives can't be used for Waddle's storage.

## Settings

Click **Settings** on the desktop (or in Pip's menu in the corner). Changes apply right away and are kept:
- **Time zone** (the clock, `date` in terminals, and file times), **12- or 24-hour clock**
- **Wallpaper**: Pip's iceberg, Mint, Ocean, Sunset, Night or Lavender
- **Pip on the desktop**: Pip sits on every wallpaper (or stands, the classic look, or Off)
- **Graphics acceleration**: on Intel graphics the graphics card draws (Mesa's iris driver); Off makes the CPU draw
- **Style**: Light or Dark (windows, taskbar, menus, and apps like Firefox follow it)
- **Mouse speed** and **natural scrolling** (and tap-to-click on touchpads, always on)
- **Volume** and the **login sound**, and whether **Pip says hi** when you log in
- **Lock the screen** after 5 to 60 minutes of not using it (or only when you choose: Super+L or Lock in the menu)

They're stored in `/etc/waddle/desktop.conf` (and the time zone in `/etc/waddle/timezone`).

## Windows

Drag a window by its title bar; drop it on the left or right edge of the screen to fill that half, or on the top
edge to maximize it (drag it away to get its old size back). Double-click a title bar to maximize. Terminals and
apps resize from their bottom-right corner.

## Pictures

Click a picture in Files (or **Pictures** in Apps) to open it in Waddle's picture editor: **Draw** (6 colors,
3 pen sizes), **Crop** (drag a box), **Text** (click, type, Enter), **Rotate**, **Undo** (Ctrl+Z), and
**Save copy** (Ctrl+S) saves "name (edited).png" next to the original. PNG pictures for now (screenshots are PNG).

## The terminal

- **Any size**: maximize it, drag its corner, or snap it to half the screen; programs inside (htop, nano...) follow the new size
- **Scroll back**: mouse wheel, or Shift+PageUp / Shift+PageDown (the last 3000 lines are kept). Typing jumps back down.
- **Copy**: select text with the mouse (it's copied as soon as you let go), or Ctrl+Shift+C
- **Paste**: Ctrl+Shift+V or Shift+Insert. Copy and paste work between terminals and apps like Firefox, both ways.

## The taskbar

Pip (bottom left) opens the menu; right-click the desktop for a quick menu. Each open window has a button (click it to switch to or minimize that window; middle- or right-click it to close the window).
Next to the clock: the **network** icon and the **volume** icon (click to mute, scroll on it to change the volume).

## Wi-Fi

Click the **network** icon in the taskbar: it shows your Ethernet connection, a Wi-Fi on/off switch and the
networks nearby (signal and a lock for ones with a password). Click a network, type its password, press Enter.
Waddle remembers it and joins it by itself from then on (hover a saved network for **Forget**). When both are
plugged in, Ethernet is used first and Wi-Fi takes over if the cable comes out. The taskbar icon turns into Wi-Fi
waves when that's your connection.

From a terminal: `wifi` (status and networks nearby), `wifi connect "Name"`, `wifi forget "Name"`, `wifi saved`,
`wifi off` / `wifi on`.

Works with most Intel, Realtek, MediaTek, Qualcomm/Atheros and Broadcom Wi-Fi cards and USB Wi-Fi adapters, and with
WPA2 and WPA3 home networks. The cards' firmware is a separate egg (`wifi-firmware`, ~70 MB): the first time Waddle
sees a Wi-Fi card it downloads it by itself over Ethernet (or `sudo waddle install wifi-firmware`).
School and work networks that ask for a username too (like eduroam) work the same way: the network list shows a
username box for them.

## Under the hood

- **Sleep**: Pip's menu > Sleep (or the sleep key). The screen locks first; your password wakes it.
- **Clock**: set from the internet (NTP) once the network is up, and saved to the computer's clock (UTC).
- **Compressed memory**: half your RAM's size of zram swap, so memory filling up slows things down instead of
  crashing apps. (Turn off: create `/etc/waddle/no-zram`.)
- **Firewall**: nothing can connect in from the network unless you allow it. `firewall` shows it,
  `sudo firewall allow 8080` opens a port, `sudo firewall deny 8080` closes it, `sudo firewall off|on`.
- **Undo an app update**: `sudo waddle rollback <egg>` goes back to the version before its last update and keeps it
  there; `sudo waddle unhold <egg>` lets it update again.

## Keyboard shortcuts

| Keys | What it does |
|---|---|
| Super (the Windows key), tap it | open Pip's menu (Esc closes it) |
| Super+T, Super+Enter or Ctrl+Alt+T | new terminal |
| Alt+Tab | switch to the next window |
| Alt+F4, Super+W or Super+Q | close the window |
| Super+Up / Super+Down | maximize / restore or minimize |
| Super+Left / Super+Right | fill the left / right half of the screen |
| Super+D | show the desktop (again to bring the windows back) |
| Super+A | Apps (everything you can open, and More apps) |
| Super+F | Files (your folders; click a picture to view it) |
| Super+I | Settings |
| Super+L | lock the screen (your password unlocks it; your windows stay open) |
| Super+E | About Waddle |
| PrintScreen or Super+Shift+S | screenshot, saved as a PNG in your Pictures folder |
| Volume keys | volume up, down, mute |
| Ctrl+Alt+Backspace | leave the desktop (text console) |

## Your account: login, passwords and sudo

- **First boot:** Pip asks for your name, a username and a password, and you're in. That account can use `sudo`.
- **After that:** the login screen asks for your password. Menu > **Log out** goes back to it.
- Your terminals run as you, not as the all-powerful root account. For things that change the whole system,
  put `sudo` in front: `sudo waddle install htop`, `sudo storage setup`, `sudo poweroff`. It asks for *your*
  password and remembers it for 5 minutes. `sudo -i` opens a root shell.
- `passwd` changes your password. `sudo newuser` adds another person (each gets their own home folder).
- Root can't log in directly (it has no password); use `sudo`. To fix a broken system from another computer,
  the boot option `waddle.serialroot` gives a root shell on the serial port.
- Coming from Waddle 0.1 (everything ran as root): your old files are still in `/root`; get them with
  `sudo cp -r /root/<thing> ~/ && sudo chown -R $USER ~/<thing>`. Run `claude-code-setup` again as yourself.

## Sound

`sudo waddle install alsa-utils` once, then:
```
sound              # sound cards and volume
sound test         # Pip's chime, then "front left / front right"
sound volume 70    # or: sound up, sound down, sound mute, sound unmute
sound mixer        # every volume control (alsamixer)
```
Waddle remembers your volume between boots. Intel HD Audio (most PCs, incl. HDMI audio) and USB sound cards work.

## Installing Waddle on its own drive or USB stick

Waddle can live on its own drive and start without NixOS. Plug in a USB stick or drive (it will be ERASED),
then in a terminal:

    sudo waddle-install              # lists your drives
    sudo waddle-install /dev/sdb     # installs on that one (you type its name to confirm)

It makes a small boot partition (with the systemd-boot boot loader) and a Linux partition for your files,
and can copy your account, files, apps and settings over. To start it, pick the drive in your PC's boot menu
(F9 on HP computers); Secure Boot must be off. Updates go to that drive by themselves.

## Updating Waddle

`sudo waddle upgrade` updates your eggs, then checks the Nest for a newer **Waddle itself** (kernel and system).
If there is one, it's checked (signature + checksum + sanity checks), written next to the current one on the
boot partition, and the current one is kept as **Waddle Linux (previous version)** in the boot menu. Restart to
use it. `waddle system` shows which version you're running and where Waddle boots from.

**You don't have to remember to check:** 30 seconds after you log in (and every 6 hours), Waddle Desktop
quietly checks the Nest. When there's something new, Pip shows a card in the top-right corner:
"Waddle 0.3 is out! Click here to update" (or "3 app updates are ready"). Click it to update, or click the
x to hide it until the next version. Once a new Waddle is installed, the card says "click here to restart".

To publish a new Waddle (for the Nest owner): `nest/tools/make-system <version> vmlinuz-waddle initramfs-waddle.cpio.gz`,
then upload the new egg + INDEX + INDEX.sig like any other egg.

After an update, the first time you log in Pip shows a **What's new** window listing everything that changed
since the version you had before.

## Firefox and other desktop apps

```
sudo waddle install firefox     # everything Firefox needs (about 40 MB of libraries, all built from source)
firefox-setup                   # gets the newest official Firefox from Mozilla (as you, no sudo)
```
Then click **Firefox** on the desktop (or type `firefox`). Firefox opens in a **normal window**: minimize,
maximize and close, drag it around, resize it from the edges, and switch with its taskbar button, right next
to your terminals. Firefox updates itself after that.

How it works: since Waddle 0.6, Waddle Desktop is a real **Wayland window system** (a compositor built on
wlroots), installed as the `waddle-desktop` egg (Firefox brings it along). Apps connect to it and get windows
with Waddle title bars; apps that draw their own title bar (GTK apps with header bars, Firefox's tabs) keep
theirs. The Nest builds the whole stack from the original source code (Wayland, wlroots, GTK 3, glib, cairo,
pango, HarfBuzz, FreeType, fontconfig, libinput, the X11 client libraries Firefox links to, and more). It draws
with the CPU, so it works on any graphics card. The simple built-in desktop stays in Waddle as a fallback: it
runs if the windowed one can't start (or add `waddle.fbdesktop` to the boot options).
Any GTK 3 / Wayland app works the same way: `waddle-app <command>` (try `waddle-app gtk3-widget-factory`).

Sound in Firefox works through **apulse** (it gives Firefox the PulseAudio it asks for, on top of Waddle's ALSA
sound). On Intel graphics the desktop and Firefox are drawn by the graphics card (Mesa, built from source); on other
cards the CPU draws. If the desktop ever stops unexpectedly, it restarts with the CPU drawing.

## Apps you can add

`sudo waddle install l3afpad` (Text editor), `sudo waddle install galculator` (Calculator),
`sudo waddle install mpv` (Videos and Music: open a video or song in Files). They show up in Apps.
Apps can also show **notifications** in the top-right corner (try `notify-send "Hi" "from Pip"`).

## Getting apps: eggs from the Nest

Waddle has its own package system:
- **Recipes** (`nest/recipes/`) say where a program's original source code lives and how to build it.
- **`hatch`** (`nest/tools/hatch`) builds a recipe into an **egg**, a Waddle package, and updates the Nest's
  package list (`INDEX`), signed with the Nest key.
- **The Nest** is the folder `nest/repo/` (INDEX, INDEX.sig, eggs/) put anywhere on the web.
- **`waddle`** on Waddle downloads eggs, checks the signature and checksums, and installs them into `/apps`,
  which lives in Waddle's saved storage, so apps stay installed after reboots.

On Waddle:
```
sudo waddle nest <url>   # once: where your Nest lives
waddle search            # see what's available
sudo waddle install htop # install (and anything it needs)
waddle list | info <egg>
sudo waddle remove <egg> | upgrade | reinstall <egg>
```
Eggs include **htop**, **lua**, **tree**, **cmatrix**, **sl**, **pipfetch** (Pip shows your system info),
**alsa-utils** (sound), **claude-code**, and **firefox** with its whole GTK/Wayland stack.

### Publishing your Nest on GitHub
1. Make a public GitHub repo, e.g. `waddle-nest`.
2. Upload the *contents* of `nest/repo/` to it (`INDEX`, `INDEX.sig`, and the `eggs` folder).
3. On Waddle: `waddle nest https://raw.githubusercontent.com/<your-name>/waddle-nest/main`

### Adding a new app
Write `nest/recipes/<app>.recipe` (copy an existing one), then on any Linux PC with build tools run
`nest/tools/hatch <app>` and upload the new egg + INDEX + INDEX.sig. On NixOS you can get the build tools with:
`nix-shell -p gcc gnumake autoconf automake libtool pkg-config git ncurses file`

The GTK/Wayland stack's recipes are generated by `nest/stack/gen-recipes.py`. They build shared libraries
(recipe `kind=lib`) under `/apps` on the build machine, and fetch each original source tarball through the
Ubuntu archive's mirror (`source="apt ..."`, so they're easiest to hatch on Ubuntu with `deb-src` enabled), plus
`meson`, `ninja`, `bison`, `flex` and `gperf`.

**Keep `nest-private/nest.secret` private.** It's the key that proves eggs are really yours; never upload it.
`hatch` uses it automatically if it's at `nest/keys/nest.secret` (or set `NEST_KEY=/path/to/nest.secret`).

## Claude Code on Waddle (Pip edition)

Waddle can run Anthropic's Claude Code in its terminal, and Pip takes over its look and voice.

Just type **`pip`** in a terminal (or click **Pip Code** in Apps), like you'd type `claude`. The first time,
Pip sets himself up: he asks for your password once to install what he needs from the Nest (all hatched from
source: **glibc**, **bash**, **curl** with real certificate checking, Mozilla's trusted certificate list), adds
his look, then runs Anthropic's official installer for Claude Code. Then it shows a link to sign in: open it on
your phone, then type the code it shows. After that, `pip` starts Pip right away. (Set up saving first with
`sudo storage setup`, so Claude Code is kept between boots.)

What Pip changes (all through Claude Code's own customization features; Claude Code itself is untouched):
- **Pixel Pip above the prompt** with a speech bubble when a session starts (a Claude Code *mod*, in
  `/apps/share/claude-code/pip-mod`). He naps once you send your first message; `/pip` calls him back.
  On a small window he's a one-line `>v<` greeting instead. He shows up a few seconds after Claude Code opens.
- **Pip's header**: Waddle Desktop's terminal shows Claude Code's welcome header Pip-style: Pip in the mascot's
  spot, the title as `Pip v<version>` (the real version number), and the model under a penguin name with its
  real number: Opus = **Emperor Pip**, Sonnet = **King Pip**, Haiku = **Little Pip**, Fable = **Royal Pip**.
  The status line uses the same names. Only the picture on your screen changes; Claude Code itself isn't
  modified, and menus like `/model` still use the real names. `pipcover off` shows the original header again
  (`pipcover on` to undo). On the plain text console (no desktop) you'll see the mascot in mint instead.
- **Mint theme** (`~/.claude/themes/waddle.json`): accents, prompt border and the mascot's color turn Pip-mint.
- **Pip's voice**, an output style (`~/.claude/output-styles/pip.md`). `/output-style default` turns it off.
- **Status line** `>v< Emperor Pip 5.5 · folder`, penguin spinner words ("Waddling...", "Belly-sliding...") and Pip tips.

Notes:
- Claude Code needs a Pro, Max, Team, Enterprise or Console account (the free plan doesn't include it).
- If you already had your own `~/.claude/settings.json`, setup leaves it alone; copy what you want from
  `/apps/share/claude-code/settings.json`. `/theme` switches back to the stock colors.
- Claude Code is installed in your home folder (`~/.local/bin/claude`) and updates itself.

## Uninstall

Remove `./waddle/waddle.nix` from `imports` and run `sudo nixos-rebuild boot`.
If you set up saving, delete Waddle's files too with `sudo rm -r /waddle` (on the drive you picked).

## Notes

- Only your home folder and Waddle settings are saved; everything else resets each boot.
- To leave, type `poweroff` or `reboot`.
