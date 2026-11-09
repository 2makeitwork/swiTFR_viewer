# swiTFR COMTRADE viewer: files and maintenance

## Open a recording

Choose **Open file** and select a COMTRADE `.CFG` or `.DAT` file, or a ZIP containing one matching pair. A standalone CFG and DAT must be in the same folder and have the same base name, for example `event.CFG` and `event.DAT`. The viewer reads them without changing either file. You can keep recordings in any accessible folder and move the pair together; choose the new location with **Open file**. The viewer remembers the folder of the last successful open.

The viewer opens COMTRADE 2013 recordings with BINARY, BINARY32 or FLOAT32 data.

## Set signal names and calibration

The PLC sends 359 REAL values. Channel 1 is the trigger input; channels 2–359 are signal values. The recording receiver generates a CFG file for every recording. You can provide an optional `default.cfg` beside the receiver executable to set the name, unit, gain and offset for each signal. The file is read at receiver startup; restart the receiver after changing it.

Use a COMTRADE 2013 CFG containing 359 analog channels numbered 1–359. A convenient starting point is a CFG from a recording: copy it beside the receiver, rename the copy `default.cfg`, and edit the channel details. For example:

```text
2,Voltage A,,,V,2.5,-7,0,-3.4028235e38,3.4028235e38,1,1,P
```

The second field is the signal name; the fifth, sixth and seventh fields are the unit, gain and offset. The displayed value is `raw × gain + offset`. Keep the channel numbers and their order, and do not put commas in signal names. An invalid or mismatched `default.cfg` stops the receiver with an error; the receiver does not change the file.

If `default.cfg` is absent, the receiver uses its built-in defaults: signal numbers as names, unit `unknown`, gain 1 and offset 0. It takes the sample period and trigger time from the PLC and calculates sample times from the sample counters, independently of the template. The PLC task period is either 1 ms or 10 ms; it is nominal timing, not measured scan jitter.

The Windows viewer reads the CFG inside the recording ZIP or next to the DAT. Its tree root is `swi-energy.com`. A saved view may contain an older tree snapshot; reopen the recording and save the view again if necessary. Within each view, all signals share the time axis and marker positions. Each signal has its own Y scale, including overlays. Up to three color-matched tick axes are visible when space permits; the legend shows the range for every overlaid signal. Older saved views open with individual Y scales and can be saved again.

## Where files are stored

| Item | Location and behavior | How to change the location |
| --- | --- | --- |
| Viewer executable | `swiTFR-COMTRADE-v<version>.exe` in this folder. | Place the executable in another folder if needed. The standard release is delivered in this folder. |
| CFG, DAT and ZIP recordings | The viewer reads recordings in place and does not import them into a database. When installed using `install-comtrade-service.sh`, the receiver writes to a `comtrade` folder beside the script. If run directly, it defaults to a `recordings` folder relative to its working directory. | To change the receiver output on the PLC, configure an absolute path with `--output` in its service arguments. Move a CFG/DAT pair together, or move the ZIP, then use **Open file** in the viewer. |
| Saved view | **Save view** writes `swiTFR-view.json` beside the executable, replacing the previous file of that name. **Open view** can select a view file elsewhere. | Move the executable to change the default save folder. Copy or rename a saved JSON file if you want to keep multiple versions; use **Open view** to select one. |
| Last recording folder | `%LOCALAPPDATA%\swiPDA\comtrade-viewer.json`. | No location setting is provided in the viewer. Opening a recording in another folder updates the remembered folder. |
| Viewer and bundled browser cache | `%LOCALAPPDATA%\swiTFR\b` and `%LOCALAPPDATA%\swiTFR\r`. The first launch unpacks the packaged components here. | No location setting is provided in the viewer. Moving the executable does not move this cache. |
| Integrated browser preferences | `%LOCALAPPDATA%\swiTFR\WebView2`; an alternate profile may be under `%LOCALAPPDATA%\swiTFR\WebView2-alternates`. These hold browser-local settings and selections. | No location setting is provided in the viewer. Use **Save view** to keep a portable layout. |

Paste a `%LOCALAPPDATA%` path into Windows File Explorer to open it for the current Windows user. Saved views contain layout and tree metadata, not the recording samples; keep the recording files separately.

## Update the executable

1. Close running viewer windows and keep any recordings, `default.cfg` and `swiTFR-view.json` you want to retain.
2. Place the new versioned executable in this folder. Each release includes its version in the filename, such as `swiTFR-COMTRADE-v0.1.3.exe`. Do not replace the saved view or recording files.
3. Start the new version. Check **About** and **Properties → Details** for its version. Open a recording and, if needed, use **Open view**.
4. For the recording receiver, copy `install-comtrade-service.sh`, the receiver binary matching the PLC architecture, and your optional `default.cfg` into one folder on the PLC. Run `sudo sh ./install-comtrade-service.sh` from that folder. It selects the newest matching version, creates the recording folder and starts the service. For compression level 1, add `-1`. The receiver’s `--version` displays its version. The viewer and receiver are separate programs.

