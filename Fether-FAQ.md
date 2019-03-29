---
title: Fether - Frequently Asked Questions
libs:
  faq: true
---

### Why does the sync starts back at 0% when I restart Fether?

The background process to synchronize the blockchain does not restart from scratch. The sync continues from the point it was left at the last time Fether was launched.

### How to add my ERC-20 Token to Fether?

We use the following repository to get the token list: [https://github.com/ethereum-lists/tokens](https://github.com/ethereum-lists/tokens).
To see your token in Fether, you need first to open a pull request to this repository. Once it is merged it will be added in Fether's next release.

### Why doesn't a token have an icon?

We use the following repository to get the token list: [https://github.com/ethereum-lists/tokens](https://github.com/ethereum-lists/tokens).
If you don't see an image for a token in Fether, it means that the `logo` field for this token was left empty or the url given is not reachable. Either way, you need to open a pull request or an [issue](https://github.com/ethereum-lists/tokens/issues/new) to this repository to fix it. Once it is merged it will be added in Fether's next release.

### How to delete an account in Fether?

We chose not to allow the deletion of accounts from the Fether user interface. In case you really know what you are doing and do not want an account to appear in Fether, you can locate and move the keystore file from the 'keys' folder. You will find it here:

- macOS: `~/Library/Application\ Support/io.parity.ethereum/keys/ethereum/`
- Linux: `$HOME/.local/share/io.parity.ethereum/keys`
- Windows 7/10: `%HOMEPATH%/AppData/Roaming/Parity/Ethereum/keys`

### Where is Fether installed?

- macOS: `/Applications/Parity Ethereum.app/Contents/MacOS/parity`
- Linux:
  - `.deb`
    - Parity Fether: `/opt/Parity\ Fether/fether`
  - AppImage
    - Parity Fether: `/wherever_you_store_it/Parity\ Fether\ X.X.X.AppImage`
  - binary
    - Parity Fether: `/wherever_you_store_it/fether`
- Windows:
  - Parity Fether: `C:\Users\fether\AppData\Local\Programs\fether\fether`

### Where are Fether logs stored?

- macOS: `~/Library/Application\ Support/fether/fether.log`
- Linux: `~/.config/fether/fether.log`
- Windows: `"C:\Windows\Users\<YOUR_USERNAME>\Application/ Data\fether\fether.log"`

### Where is Parity Ethereum installed?

- macOS: `/Applications/Parity Ethereum.app/Contents/MacOS/parity`
- Linux:
  - `.deb`
    - Parity Ethereum: `/opt/Parity Fether/resources/static/parity`
    - Parity Ethereum chain: `~/.local/share/io.parity.ethereum/`
  - AppImage
    - Parity Ethereum: `/tmp/.mount_Parity[something]/resources/static/parity`
    - Parity Ethereum chain: `~/.local/share/io.parity.ethereum/`
  - binary
    - Parity Ethereum: `/wherever_you_store_it/resources/static/parity`
    - Parity Ethereum chain: `~/.local/share/io.parity.ethereum/`
- Windows:
  - Parity Ethereum:
    - `C:\Program Files\Parity Technologies\Parity\parity.exe`
    - `C:\Users\fether\AppData\Local\Programs\fether\resources\static\parity`
  - Parity Ethereum chain: `C:\Users\fether\AppData\Roaming\Parity\Ethereum\`

Note that with older versions of Fether (i.e. <0.3) or separately installed versions of Parity Ethereum then it may be installed at:

- Linux:
  - binary
    - Parity Ethereum: `/bin/parity`, `/usr/bin/parity`, or `/usr/local/bin/parity`
- Windows:
  - Parity Ethereum: `C:\Program Files\Parity Technologies\Parity\parity.exe`

### How to launch Fether on a different network?

You can pass specific flags (such as `--chain`) for Fether to launch the underlying Parity Ethereum on a specific network:

```bash
# Launching Fether with a Parity Ethereum light client node on Ropsten
$ /path/to/fether --chain ropsten --light
```

### How to launch Fether with a separately launched Parity Ethereum node?

Fether will download and install Parity Ethereum if it is not already installed. Fether will run a Parity Ethereum light client node if Parity Ethereum has not already been launched separately.

1) Launch a Parity Ethereum node using any appropriate flags. Check what flags are available with `parity --help`).

```bash
# Launch a Parity Ethereum light client node on the Ropsten network instead of the Kovan (default) test network
$ parity --chain ropsten --light
```

2) Launch Fether (using the launcher menu or in a terminal session), it will automatically detect the local node and connect to it.

### How to connect Fether to a Parity Ethereum full node?

Fether will automatically connect to any Parity Ethereum node that is already running (using WebSockets port 8546).
To connect Fether to a full node:

1) Launch a Parity Ethereum full node using any appropriate flags. Check what flags are available with `parity --help`).

```bash
# Launch a Parity Ethereum full node on Ropsten test network
$ parity --chain ropsten
```

2) Launch Fether (from the application launcher menu or in a terminal session), it will automatically detect the local node and connect to it.

### How to access logs to troubleshoot errors?

To help users understand what is going on if Fether crashes, it is essential to provide logs.

Logs for the user interface (UI) may be viewed as follows:
- Open the Fether menu by right-clicking the Fether window
- Click the menu item "View > Toggle Developer Tools"
- Undock the Developer Tools into a separate window
  - Clicking the '3 dots' icon located at the top right
  - Select the 'separate windows' symbol
- Click the 'Console' tab in the Developer Tools window
- Expand the 'Console' tab so it is large enough to take a screenshot of any logs, warnings, or errors.
- Please take a screenshot of the Fether window and the logs and share them with us by posting an [Issue](https://github.com/paritytech/fether/issues) in the Fether GitHub repository or by clicking the 'Feedback' button on the Accounts page of the Fether window.

Logs related to the underlying Parity Ethereum node or the overall Fether application will only appear if you launch Fether using a terminal session.

Logs that are generated when running Fether are saved at locations mentioned in FAQ [Where are Fether logs stored?](#where-are-fether-logs-stored?). Please share the log file with us by uploading it when creating an [Issue](https://github.com/paritytech/fether/issues) in the Fether GitHub repository or by clicking the 'Feedback' button on the Accounts page of the Fether window.

#### Tips

If you want the Developer Tools to automatically open each time you run Fether, see the FAQ "How to open Developer Tools automatically when Fether launches".

#### Advanced

If you want to monitor the latest changes to the log file whilst running Fether in a terminal session then use the `tail` command (i.e. run `tail -f ~/.config/fether/fether.log`).

If you want to troubleshoot errors in the user interface (UI) whilst running Fether then add [line-of-code breakpoints](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints) in Developer Tools by clicking the 'Sources' tab, choosing 'Page > top > webpack-internal://', and then setting line-of-code breakpoints.

### What is "taskbar mode"?

Fether runs by default as a [tray](https://electronjs.org/docs/api/tray) application that is [frameless](https://electronjs.org/docs/api/frameless-window#frameless-window), so it doesn't have any window decoration (basically a frame and the usual minimize/full screen/close buttons)

Below we describe whether Fether's features that are 'Always' apparent, or only apparent depending on whether taskbar mode is 'Enabled' or 'Disabled', and depending on which operating system Fether is run on.

#### macOS

Taskbar mode is `true` by default.

* **Enabled** `true`
  * Tray icon - Left-click or right-click the tray icon shows "tray context menu" containing just "Show/Hide Fether" and "Quit" options. "Show/Hide Fether" toggles the Fether window show/hide. It includes the "Reload" option only when run in the development environment, since it is necessary to reload the Fether window when it is launched using `yarn start`. 
  * Dock icon - no action
  * Fether window - frameless
* **Disabled** `false`
  * Dock icon - toggles show/hide Fether window
  * Fether window - frame (with close/minimise icons)
* **Always**
  * Menubar - Fether menu shown by default
  * Fether window - "window context menu" shown upon right-click in the Fether window
  * Fether window - position is saved upon moving, minimising, or closing so it is restored in the same position.

#### Linux

Taskbar mode is `true` by default.

* **Enabled** `true`
  * Tray icon - Left-click or right-click the tray icon shows "tray context menu" containing just "Show/Hide Fether" and "Quit" options. "Show/Hide Fether" toggles the Fether window show/hide. It includes the "Reload" option only when run in the development environment, since it is necessary to reload the Fether window when it is launched using `yarn start`.
  * Dock icon - toggles show/hide Fether window
  * Fether window - frameless
* **Disabled** `false`
  * Dock icon - toggles show/hide Fether window
  * Fether window - frame (with close/minimise icons)
  * Menubar - Fether menu may not be shown in the tray by default depending on whether [`setMenuBarVisibility`](https://electronjs.org/docs/api/browser-window#winsetmenubarvisibilityvisible-windows-linux) has been set. Fether menu may be configured to automatically hide by setting [`setAutoHideMenuBar`](https://electronjs.org/docs/api/browser-window#winsetautohidemenubarhide). Toggle show/hide the Fether menu in the frame by clicking the Fether window and then holding down the ALT key to reveal it, which only works if auto-hide menu bar is enabled.
* **Always**
  * Fether window - "window context menu" shown upon right-click in the Fether window
  * Fether window - position is saved upon moving, minimising, or closing so it is restored in the same position.

#### Windows

Taskbar mode is `true` by default.

* **Enabled** `true`
  * Tray icon - Left-click toggles show/hide Fether window
  * Tray icon - Right-click the tray icon shows "tray context menu" containing just "Show/Hide Fether" and "Quit" options. "Show/Hide Fether" toggles the Fether window show/hide. It includes the "Reload" option only when run in the development environment, since it is necessary to reload the Fether window when it is launched using `yarn start`.
  * Dock icon - toggles show/hide Fether window
  * Fether window - frameless
* **Disabled** `false`
  * Dock icon - toggles show/hide Fether window
  * Fether window - frame (with close/minimise icons).
  * Menubar - Fether menu may not be shown in the tray by default depending on whether [`setMenuBarVisibility`](https://electronjs.org/docs/api/browser-window#winsetmenubarvisibilityvisible-windows-linux) has been set. Fether menu may be configured to automatically hide by setting [`setAutoHideMenuBar`](https://electronjs.org/docs/api/browser-window#winsetautohidemenubarhide). Toggle show/hide the Fether menu in the frame by clicking the Fether window and then holding down the ALT key to reveal it, which only works if auto-hide menu bar is enabled.
* **Always**
  * Fether window - "window context menu" shown upon right-click in the Fether window
  * Fether window - position is saved upon moving, minimising, or closing so it is restored in the same position.

### How to run Fether without "taskbar mode"?

#### Development Environment

Disable the `TASKBAR` environment variable prior to launching Fether as follows:

```bash
TASKBAR=false yarn start
```

### How to open Developer Tools automatically when Fether launches?

#### Development Environment

Open Developer Tools automatically by running Fether with the `DEBUG` environment variable set as follows:

```bash
DEBUG=true yarn start
```

### What to do if the Fether window appears unresponsive or does not load?

#### Development Environment

If you run Fether with `yarn start` it is a known issue that the Fether window will hang with just a white/blank screen even after it has compiled successfully. Fix it by simply right-clicking the Fether window to reveal the Fether menu, and choose the menu item 'View > Reload' (or use shortcut CMD + R on macOS, CTRL + R on Linux/Windows). Alternatively run Fether with `yarn electron` (without live reload).

If Fether is otherwise unresponsive or does not load then please follow the steps in the FAQ [How to access logs to troubleshoot errors](#how-to-access-logs-to-troubleshoot-errors).

#### Production Environment

Please follow the steps in the FAQ [How to access logs to troubleshoot errors](#how-to-access-logs-to-troubleshoot-errors).
