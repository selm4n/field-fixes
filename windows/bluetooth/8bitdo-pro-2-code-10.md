# Fixing 8BitDo Pro 2 Bluetooth Driver Error on Windows 11 — Code 10

| Field | Details |
| --- | --- |
| Status | Resolved |
| Date resolved | 2026-11-10 |
| Category | Windows / Bluetooth |
| Relevant hardware | 8BitDo Pro 2 Bluetooth controller; PC and Bluetooth adapter models not recorded |
| Operating system and version | Windows 11; edition and build not recorded |
| Relevant applications | EA SPORTS FC 26, launched through EA App; application versions not recorded |
| Firmware and driver versions | Not recorded |
| Tested by / evidence source | Repository owner; firsthand troubleshooting account and explicit resolution confirmation |
| Last reviewed | 2026-11-10 |

## Problem summary

The controller worked over USB-C but did not work correctly over Bluetooth when being set up for EA SPORTS FC 26. Windows could discover and pair with it, yet Bluetooth settings reported a driver error. The main Bluetooth device appeared healthy while two related input-device components reported Code 10.

The owner confirmed that Bluetooth functionality was restored after device-cleanup guidance and pairing again. The precise underlying cause was not proven.

## Symptoms

- Bluetooth pairing was possible, but Settings showed `8BitDo Pro 2 — Driver error`.
- The main device's properties reported normal operation and Windows considered its best driver already installed.
- Two related entries under **Device Manager → Human Interface Devices** displayed warning icons: `Bluetooth HID Device` and `Bluetooth XINPUT compatible input device`.
- The Windows Game Controllers panel (`Win + R` → `joy.cpl`) showed a controller entry and detected button input during testing. Wired and wireless results were not independently recorded at every stage, so this does not establish that Bluetooth input worked before cleanup.
- Multiple controller entries appeared in the Game Controllers list. Their presence was observed; they were not established as the cause.

## Exact error messages

Bluetooth settings:

```text
8BitDo Pro 2 — Driver error
```

Both affected input-device components:

```text
This device cannot start. (Code 10)

{Conflicting Address Range}
The specified address range conflicts with the address space.
```

## Investigation and diagnosis

The investigation checked pairing, removed and recreated the pairing, inspected Windows device properties, examined Human Interface Devices for warnings, checked XInput mode, and compared USB-C operation with Bluetooth behavior.

The main Bluetooth device showed:

| Property | Observed value |
| --- | --- |
| Device name | 8BitDo Pro 2 |
| Manufacturer | Microsoft |
| Location | Microsoft Bluetooth Enumerator |
| Device status | This device is working properly. |

That healthy status did not describe every related component: the HID/XInput entries used for controller input failed to initialize. Checking only the main Bluetooth entry missed the relevant errors.

Microsoft describes Code 10 as a device-start failure. It does not, by itself, identify the failed driver or establish the root cause. See [Microsoft's Code 10 documentation](https://learn.microsoft.com/en-us/windows-hardware/drivers/install/cm-prob-failed-start).

## What did not work

- Simply checking for updated drivers did not resolve the issue; Windows reported that the best driver for the main Bluetooth device was already installed.
- Removing and recreating the pairing was part of the investigation, but pairing alone was not recorded as a successful resolution.
- USB-C worked as a comparison and temporary connection method; it did not resolve the Bluetooth failure.

## Working solution

> [!IMPORTANT]
> The overall cleanup-and-re-pair approach was successful according to the owner. The original account does not independently confirm exactly which uninstall operations were performed. The procedure below reconstructs the guidance in a safe order; it is not a claim that each individual operation was verified.

### 1. Remove the pairing

1. Disconnect the controller's USB cable so subsequent input checks can isolate Bluetooth.
2. Power off the controller.
3. Open **Settings → Bluetooth & devices**.
4. Remove the existing **8BitDo Pro 2** Bluetooth pairing.

### 2. Clean up only the affected controller instances

5. Open **Device Manager** from the Start menu.
6. Expand **Human Interface Devices**. If a previously identified instance is no longer visible, **View → Show hidden devices** can help inspect disconnected entries. This is supplemental inspection guidance, not a separately recorded action from this case. If removing the pairing already removed an entry, skip that entry.
7. Identify the affected **Bluetooth XINPUT compatible input device**. Check its properties and the recorded Code 10 error, and confirm its association with the Pro 2 before proceeding.
8. Right-click that faulty instance and select **Uninstall device**. Leave any option to remove or delete the driver package **unchecked**.
9. Identify the affected **Bluetooth HID Device**.
10. Uninstall that instance **only after confirming it belongs to this controller**, again leaving driver-package removal unchecked.
11. Preserve shared driver packages and all unrelated devices. Do not uninstall the **Microsoft Bluetooth Enumerator**.

> [!WARNING]
> Generic HID names can also belong to keyboards, mice, and other peripherals. A name or warning icon alone is not enough to identify the Pro 2. Inspect **Properties → Details** for parent/container relationships and, where helpful, **View → Devices by connection** to trace the association to the controller. These are identification aids, not proof from this case. If the relationship is unclear, skip the uninstall and investigate further. Never remove all HID devices indiscriminately.

### 3. Restart and pair in XInput mode

12. Restart Windows.
13. Set the controller's rear mode selector to **X** (XInput), the mode recommended here for Windows gaming and specified in the [official Windows pairing guide](https://manual.8bitdo.com/pro2/pro2_bluetooth_windows.html).
14. Press **Start** to turn on the controller, then hold its pairing button for approximately **3 seconds** to enter pairing mode, following the official guide.
15. In **Settings → Bluetooth & devices → Add device → Bluetooth**, select **8BitDo Pro 2** and complete pairing. Keep USB disconnected.
16. Press **Win + R**, run `joy.cpl`, select the relevant controller, and open **Properties** to check button and axis responses over Bluetooth.
17. Launch **EA SPORTS FC 26** through **EA App** and test controller input in the game.

## Verification

**Recorded outcome:** the owner explicitly confirmed the issue was resolved and Bluetooth functionality was restored after the cleanup guidance. Separate post-cleanup logs, screenshots, individual uninstall confirmations, and per-application test results were not recorded.

**Checks for readers reproducing the fix:**

- Keep USB unplugged to ensure the input is coming through Bluetooth.
- Check that Bluetooth settings no longer show the driver error.
- Inspect the related HID/XInput entries for remaining warning icons or error codes.
- Verify buttons and axes in `joy.cpl`, then test input in EA SPORTS FC 26.

These checks define what to verify now; they do not imply that each was independently documented in the original case.

## Possible explanation

**Hypothesis:** removing affected device instances and pairing again may have allowed Windows to rebuild the controller's device registrations and initialize its input components successfully.

The available evidence does not establish whether stale registrations, a driver issue, or another condition caused the failure. The literal `{Conflicting Address Range}` diagnostic is not proof of a physical memory-address conflict. Neither multiple controller entries nor the main device's Microsoft manufacturer field establishes a cause.

## Warnings and limitations

- This is one resolved Windows 11 case, not a guaranteed fix for every Windows build, Bluetooth adapter, or controller revision.
- Exact build, adapter, firmware, and driver versions were not recorded, limiting direct comparison with other systems.
- Remove only positively identified affected controller instances; preserve shared drivers and the Microsoft Bluetooth Enumerator. No registry modification is part of this procedure.
- If the issue returns, inspect the **current** device status and error codes before repeating changes. A different error may require a different investigation.
- Controller firmware and the PC/Bluetooth-adapter manufacturer's drivers are secondary troubleshooting avenues, not verified parts of this resolution. Consult the relevant official support resources and model-specific instructions before changing them.

## References

Official sources checked while preparing this entry:

- [Microsoft Learn: Code 10 — CM_PROB_FAILED_START](https://learn.microsoft.com/en-us/windows-hardware/drivers/install/cm-prob-failed-start) — explains the device-start failure code; does not diagnose this particular case.
- [8BitDo: Pro 2 Windows Bluetooth pairing guide](https://manual.8bitdo.com/pro2/pro2_bluetooth_windows.html) — documents X mode, power-on, and Bluetooth pairing.

Neither source endorses or independently verifies this specific cleanup procedure. The resolution evidence comes from the owner's firsthand report.
