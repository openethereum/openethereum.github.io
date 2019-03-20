---
title: Fether - Frequently Asked Questions
libs:
  faq: true
---

### Why does the sync starts back at 0% when I restart Fether?

The background process to synchronize the blockchain does not restart from scratch. The sync continues from the point was left the last time Fether was launched.

### How to delete an account?

We voluntarily did not allow to delete accounts from Fether user interface. In case you really what you are doing and do not want an account not to appear on Fether, you can local and move the keystore file from the keys folder. You will find it here:

- Mac OS X: `~/Library/Application\ Support/io.parity.ethereum/keys/ethereum/`
- Linux: `$HOME/.local/share/io.parity.ethereum/keys`
- Windows 7/10: `%HOMEPATH%/AppData/Roaming/Parity/Ethereum/keys`

### How to use Fether on a different network?

You can pass specific flags (such as `--chain`) for Fether to launch the underlying Parity Ethereum with:

```bash
# Launching Fether with Parity Ethereum light client on Ropsten
$ /path/to/fether --chain ropsten --light
```

### How to access to Fether terminal and console logs or errors to help to troubleshoot

To help the developers understand what is going on when Fether crashes, it is essential to provide console logs.
The console logs are accessible on:
  - Right click>View>Toggle Developer Tools
  - Undock the Developer Tools into a separate window by clicking on the 3 dots icon at the top right and selecting the separate windows symbol
  - You can now make the window large enough and take a screenshot of the `console` tab.

Terminal logs can also be useful and are available at the following location:

#### Windows 

```
C:/Windows/Users/<your user>/Application\ Data/fether/fether.log
```

#### Mac

```
~/Library/Application\ Support/fether/fether.log
```

#### Linux 

```
~/.config/fether/fether.log
```


### How to connect Fether to a full node?

Fether will automatically connect to any Parity Ethereum node (using WebSockets port 8546).
To connect Fether to a full node, simply launch Parity Ethereum with the flags you want

```bash
# Launching Parity Ethereum full node on Ropsten network
$ parity --chain ropsten
```

Launch Fether (using the graphical interface or in the console), it will automatically detect the local node and connect to it:

```bash
# Fether will connect to the running node
$ /path/to/fether
```

### What is "taskbar mode"

Per default, Fether runs as a tray application and doesn't have any window decoration (basically a frame and the usual minimize/full-size/close button)

#### Mac

Taskbar mode is `true` by default.

* Enabled `true`
  * Tray icon - Left-click or right-click the tray icon shows "tray context menu" containing just "Show/Hide Fether" and "Quit" options. "Show/Hide Fether" toggles the Fether window show/hide
  * Dock icon - no action
  * Fether window - frameless
* Disabled `false`
  * Dock icon - toggles show/hide Fether window
  * Fether window - frame (with close/minimise icons)
* Always
  * Menubar - Fether menu shown by default
  * Fether window - "window context menu" shown upon right-click in the Fether window
  * Fether window - position is saved upon move, minimising, and close so it is restored in the same position.

#### Linux

Taskbar mode is `true` by default.

* Enabled `true`
  * Tray icon - Left-click or right-click the tray icon shows "tray context menu" containing just "Show/Hide Fether" and "Quit" options. "Show/Hide Fether" toggles the Fether window show/hide
  * Dock icon - toggles show/hide Fether window
  * Fether window - frameless
* Disabled `false`
  * Dock icon - toggles show/hide Fether window
  * Fether window - frame (with close/minimise icons)
  * Menubar - Fether menu may not be shown in the tray by default depending on whether `setMenuBarVisibility` has been set. Fether menu may be configured to automatically hide by setting `setAutoHideMenuBar`. Toggle show/hide the Fether menu in the frame by clicking the Fether window and then holding down the ALT key to reveal it, which only works if auto-hide menu bar is enabled.
* Always
  * Fether window - "window context menu" shown upon right-click in the Fether window
  * Fether window - position is saved upon move, minimising, and close so it is restored in the same position.

#### Windows

Taskbar mode is `true` by default.

* Enabled `true`
  * Tray icon - Left-click toggles show/hide Fether window
  * Tray icon - Right-click the tray icon shows "tray context menu" containing just "Show/Hide Fether" and "Quit" options. "Show/Hide Fether" toggles the Fether window show/hide
  * Dock icon - toggles show/hide Fether window
  * Fether window - frameless
* Disabled `false`
  * Dock icon - toggles show/hide Fether window
  * Fether window - frame (with close/minimise icons).
  * Menubar - Fether menu may not be shown in the tray by default depending on whether `setMenuBarVisibility` has been set. Fether menu may be configured to automatically hide by setting `setAutoHideMenuBar`. Toggle show/hide the Fether menu in the frame by clicking the Fether window and then holding down the ALT key to reveal it, which only works if auto-hide menu bar is enabled.
* Always
  * Fether window - "window context menu" shown upon right-click in the Fether window
  * Fether window - position is saved upon move, minimising, and close so it is restored in the same position.

### How to run Fether without taskbar mode (no tray icon)

```bash
TASKBAR=false yarn start
```

> Troubleshooting: If it hangs on a white screen in Electron even though it has compiled and has been syncing for a long time, then simply choose 'View > Reload' (CMD + R on Mac, CTRL + R on Linux/Windows) from the Fether/Electron menu.

> Developer Tools: Open developer tools automatically by running `DEBUG=true yarn start` when not in the production environment.
