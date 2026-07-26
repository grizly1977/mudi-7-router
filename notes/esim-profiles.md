# eSIM on Mudi 7 (GL-E5800) — How Many Profiles Can It Store?

Researched 2026-07-25. Sources: official user manual v4.8.3 + GL.iNet's live docs site (see Sources below).

## Short answer

**GL.iNet does not publish a fixed maximum profile count.** The onboard eUICC (eSIM chip)
shows live "storage remaining/total" and "profile number" values in the router's own UI,
but no fixed spec (e.g., "holds 5 profiles") is documented anywhere. Capacity in practice
depends on how large each carrier's profile is — profile sizes vary by carrier, so the
number you can fit varies too.

Where to check on your own unit: **INTERNET > Cellular > eSIM Management**, or (on models
that expose the newer generic app) **APPLICATIONS > eSIM Management > Current eSIM Status**
→ `eSIM Storage (remain/total)` and `eSIM Profile Number`.

Note: Mudi 7 uses its own dedicated eSIM management flow under **INTERNET > Cellular**,
which is separate from the generic **APPLICATIONS > eSIM Management** app documented for
other GL.iNet models (X2000, X3000, E750/E750V2, etc.) — that generic app's docs actually
list GL-E5800 as unsupported ("✗"), because Mudi 7's built-in eSIM has its own interface
instead.

## Hardware facts (confirmed)

- 1x onboard eSIM chipset + 2x Nano-SIM slots (SIM 1 / SIM 2).
- **Only two identities can be active/switchable at once**: SIM 1 + SIM 2, **or** SIM 1 + eSIM.
  The built-in eSIM and SIM 2 are mutually exclusive — enabling eSIM disables SIM 2 even if
  a card is physically inserted.
- A physical "eSIM card" (e.g. SIMPoYo) inserted into a SIM slot is **not** recognized as an
  eSIM on Mudi 7 — it just works as a regular SIM, since the device already has its own
  built-in eSIM chip.
- Most eSIM activation QR codes / codes are **single-use** — once installed, you generally
  can't re-download the same code again if you remove that profile.

## What the generic GL.iNet eSIM app documents (NOT confirmed for Mudi 7's own flow)

On models that use the generic "APPLICATIONS > eSIM Management" app (Mudi V2/E750V2, Spitz
Plus/X2000, Spitz AX/X3000, Puli AX/XE3000...), GL.iNet preloads a **Seed Profile**: 1GB free
data for US/Europe + 100MB global data, valid 1 year, intended just for downloading your real
profile on arrival with no other internet access. **It's unclear whether Mudi 7's separate,
dedicated eSIM flow includes an equivalent seed profile** — the GL-E5800-specific user guide
doesn't mention one. Worth checking directly on the device or asking GL.iNet support
(support@gl-inet.com) if this matters to you.

## Practical takeaway

Don't buy multiple travel eSIMs assuming a specific stackable count — install one at a time,
check the live storage/profile counters in eSIM Management, and only buy the next one once
you're actually about to need it (most codes are single-use anyway, and re-downloading
isn't guaranteed to work if you run out of room).

## Sources

- [eSIM Management — GL.iNet Router Docs 4](https://docs.gl-inet.com/router/en/4/interface_guide/esim_management/)
- [GL-E5800 (Mudi 7) User Guide — GL.iNet Router Docs 4](https://docs.gl-inet.com/router/en/4/user_guide/gl-e5800/)
- [Mudi 7 - No eSIM Setup — GL.iNet Official Forum](https://forum.gl-inet.com/t/mudi-7-no-esim-setup/68496)
- `gl-e5800_user_manual_v4.8.3.pdf` (this repo), pages 19–22
