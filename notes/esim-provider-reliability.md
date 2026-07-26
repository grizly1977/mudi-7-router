# Mudi 7 (GL-E5800) — Is There a "100% Reliable" eSIM Provider?

Researched 2026-07-26 across Reddit, GL.iNet's official forum, and general web (device
launched ~Jan–May 2026, so the sample of real-world reports is still small).

## Bottom line

**No.** Every independent failure report found spans a *different* provider (and even
physical SIMs from local carriers), all with the identical symptom: SIM/eSIM reads fine,
network is visible on a manual scan, but data registration never completes ("SIM not
registered" / "cannot connect"). That pattern points at a modem/firmware issue on the
device itself, not any one provider being bad — so a ranked "these providers are 100%"
list doesn't meaningfully exist yet for this device.

## Confirmed failure reports (real users, GL.iNet forum)

- **4-for-4 failure**: one user tried a non-IMEI-locked physical SIM, the pre-installed
  IOTCLUB eSIM, an Airalo eSIM, and a US Mobile roaming eSIM — all failed with "SIM not
  registered" despite correct APN/ICCID and strong visible signal. Factory reset + full
  firmware reflash (4.8.5) did not fix it.
  ([thread](https://forum.gl-inet.com/t/gl-e5800-mudi-7-seems-doa-any-sim-i-try-not-registered/69615))
- **RedTeaGo** — listed on GL.iNet's own official "supported" compatibility table — failed
  for a real user, confirmed by GL.iNet support after remote testing. Same user's local
  national-carrier physical SIM (works fine in every other device) also failed on Mudi 7
  across both SIM slots, all APNs tried, 4G/5G, IPv4/IPv6 — despite successfully receiving
  SMS (proof basic signaling works; only the data bearer fails).
  ([thread](https://forum.gl-inet.com/t/mudi-7-redteago-esim-issue/68959))
- **Our own case** (this repo): a Telcoequity-branded eSIM (host network: Plus, Poland)
  failed to register until the APN was manually corrected to `plus` — see
  [esim-troubleshooting.md](./esim-troubleshooting.md) for the full log analysis. Fits the
  same "wrong auto-detected APN masquerading as a provider failure" pattern others
  independently hit.

## GL.iNet support's actual recommended fix (for registration failures)

Given by GL.iNet staff (Cathy) in response to the 4-provider failure report:

1. Set **TTL to 64** in SIM Card Settings
2. Disable IMS via the Modem AT Command page:
   ```
   AT+QCFG="IMS",2
   AT+QEFSSYNC=1
   AT+CFUN=1,1
   ```
3. Check that **Tower Lock / Operator Lock** isn't enabled

No confirmation yet in these threads that this fix actually resolved the reported cases —
worth trying, but not a guaranteed fix.

## Practical strategy given the uncertainty

1. Buy the smallest/cheapest data package from a candidate provider to test compatibility
   before committing to a full plan — independently suggested by multiple affected users.
2. Look up the exact APN the provider's own phone-app instructions specify, and set it
   manually rather than trusting auto-detection.
3. If registration fails, try the TTL=64 + IMS-disable sequence above before concluding the
   provider itself is incompatible.

## Sources

- [GL-E5800/Mudi 7 seems DOA - any SIM I try "not registered"](https://forum.gl-inet.com/t/gl-e5800-mudi-7-seems-doa-any-sim-i-try-not-registered/69615)
- [Mudi 7 RedTeaGo eSIM issue](https://forum.gl-inet.com/t/mudi-7-redteago-esim-issue/68959)
- [Carriers Supported by SIMPoYo and EIOTCLUB Physical eSIM Cards](https://forum.gl-inet.com/t/carriers-supported-by-simpoyo-and-eiotculb-physical-esim-cards/54164)
- [How to install eSIM on Mudi 7](https://forum.gl-inet.com/t/how-to-install-esim-on-mudi-7/69599)
