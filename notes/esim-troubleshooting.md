# Mudi 7 (GL-E5800) — eSIM Connection Troubleshooting Log

Real diagnoses from this device's own logs (`eSIM Log.txt`, modem debug JSON export),
2026-07-23 to 2026-07-26. Two distinct failure modes were hit and diagnosed — worth
telling apart, since the fix is different for each.

## Failure mode 1: profile installs but won't *enable*

Symptom: eSIM profile downloads and installs fine (`IPA_DL_10 end.err: 0`), but the
subsequent activation step fails every time, regardless of which profile:

```
ES10C_ENABLE_PROFILE_0 iccid: ...
Error: XAT_CSIM_4 err:-1
Error: IPA_ENABLE_PROFILE_5 enable.fail:2
Error: enable.fail: -2
```

Seen 3 times across 3 different ICCIDs over multiple days — ruling out "bad profile" as
the cause. This is a low-level AT-command failure in the modem's SIM-access path, at the
"enable this profile on the eUICC" step, before any network registration is attempted.

No confirmed fix found in GL.iNet's forum for this exact signature. Recommended next
steps: firmware update check (System > Upgrade), full power cycle (not just the
automatic 5s modem-only reboot the eSIM daemon already tries), and exporting the log
to support@gl-inet.com — GL.iNet staff have repeatedly asked for this exact log export
in similar threads.

## Failure mode 2: profile enables fine, but network registration is denied

Symptom: profile is live on the modem (`AT+CPIN?` → READY), but:

```
AT+CEREG?      -> 0,3   (registration DENIED)
AT+C5GREG?     -> 0,0   (not registered, not searching)
AT+CSQ         -> 99,99 (no usable signal reading)
AT+QENG="servingcell" -> "SEARCH" (never camped on a cell)
AT+CGATT?      -> 0     (not attached)
AT+QNETRC?     -> esm_cause:38 ("Network failure"), 5gmm_cause:15 ("no network slices available")
```

**Root cause in our case: wrong APN.** The eSIM's IMSI (MCC 260 / MNC 01) identifies its
home network as Plus (Polkomtel), Poland. The router had auto-configured APN
`global.telcoequity` (the eSIM reseller's own default), but the network only accepted
registration once the APN was manually changed to `plus`.

**Fix that worked**: Cellular > SIM Card Settings > manually set APN to the value the
carrier actually expects (check the ICCID's `apn_list` in a modem debug export, or what
the provider's own phone-app instructions specify) — do not trust auto-detection.

This matches a broader pattern seen independently by other Mudi 7 users (see
[esim-provider-reliability.md](./esim-provider-reliability.md)): what looks like a
"registration denied" / "cannot connect" network-level failure is sometimes actually a
wrong-APN problem, not a genuine carrier/device incompatibility.

## How to tell which failure mode you're hitting

- Check **INTERNET > Cellular > eSIM Management > Export Log for Support** for the
  `XAT_CSIM` / `enable.fail` signature → failure mode 1 (device/firmware-level, enable step).
- Check a modem debug export (or `AT+CEREG?` / `AT+QNETRC?` if you have AT access) for
  `esm_cause` / registration-denied after the profile is already enabled → failure mode 2
  (likely APN, possibly also region/roaming coverage — see
  [esim-provider-reliability.md](./esim-provider-reliability.md) for the wider pattern).
