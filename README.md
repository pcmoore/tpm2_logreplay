TPM2 Event Log Tools
===============================================================================
Contact: pmoore2@cisco.com or paul@paul-moore.com

This repository contains some simple Python scripts for working with TPM2 event
logs.

### tpm2_logreplay

The `tpm2_logreplay` tool is designed to read a TPM2 event log from standard
input, replay the PCR extension events, and write the resulting TPM2 PCR values
to stdout.

Both the output and the TPM2 PCR selection arguments are designed to mimic the
`tpm2_pcrread` tool.  Output comparisons between the two tools is easy as you
can simply `diff` the output of the two tools.  It is important to remember
that the TPM2 event log only captures PCR extensions made *before* the kernel
boots, any PCR extensions that take place after the kernel boots will not be
part of the TPM2 event log.

Example:

```
% cat /sys/kernel/security/tpm0/binary_bios_measurements | \
  ./tpm2_logreplay sha1:0,1,2,3,4,5,6,7+sha256:0,1,2,3,4,5,6,7
sha1:
  0 : 0xF95DB1165685019F70D6702B32C7EDDD93436F46
  1 : 0x36AA249A38937697E654CCEC076E11211CE4EA6C
  2 : 0x85D813EACD7C925C20666726BE94E667ACBBC412
  3 : 0xB2A83B0EBF2F8374299A5B2BDFC31EA955AD7236
  4 : 0x9CF025571F17BF97BCA92C71715BBB7C808E6B54
  5 : 0x5D3B1EEF0D839F0282DF5F2FF2AEA4BA473B62F8
  6 : 0xB2A83B0EBF2F8374299A5B2BDFC31EA955AD7236
  7 : 0x16A1A13CE05A1CC5EE81E8E41FA18D38AC343089
sha256:
  0 : 0x1DF67835147BE403712B782205B60205CB20C352FD1E7B8AB0F2B85F1A9F0A4E
  1 : 0x56BDB750FA1B9993BFA9CDAAF56B27C46ED8C9BB0556AF8BDD9015EE0A6A9405
  2 : 0x7F4E9F6C9C0C280C2C184E60F3F9820A6B98E3FA22870FB7EDCC1D4FF9896051
  3 : 0x3D458CFE55CC03EA1F443F1562BEEC8DF51C75E14A9FCF9A7234A13F198E7969
  4 : 0x82761F3371ED408F7CB73282EFE19DE47081903E7652D1BAA17461DAEAF3521D
  5 : 0xA15F67B8E23253AB66D5C8126316993303BFB2DB95165C06449008107F7A8D20
  6 : 0x3D458CFE55CC03EA1F443F1562BEEC8DF51C75E14A9FCF9A7234A13F198E7969
  7 : 0x0C4082EF5BEDDFD13740C1DD56813612AD487CD4935AF1B134EFF019CEF3C263
```

### tpm2_logfilter

The `tpm2_logfilter` tool is similar to `tpm2_eventlog` but instead of
providing a verbose text representation of the TPM2 event log, it produces a
condensed text output that limits each event to a single line of output.  The
`tpm2_logfilter` tool also offers basic filtering using the same PCR selection
arguments as the `tpm2_logreplay` tool described above.

Example:

```
% cat /sys/kernel/security/tpm0/binary_bios_measurements | \
  ./tpm2_logfilter sha1:7
pcr:7 type:EV_EFI_VARIABLE_DRIVER_CONFIG(SecureBoot) sha1:0xD4FDD1F14D4041494DEB8FC990C45343D2277D08 
pcr:7 type:EV_EFI_VARIABLE_DRIVER_CONFIG(PK) sha1:0x397A8C41851E62BF7114970635CF22D7E57AED22 
pcr:7 type:EV_EFI_VARIABLE_DRIVER_CONFIG(KEK) sha1:0xEA1ACED0368ABE97BDD43843DAEF31B76A22D580 
pcr:7 type:EV_EFI_VARIABLE_DRIVER_CONFIG(db) sha1:0xCA4A61F2310C8EA81EB3924C4D04C1D11D122B3F 
pcr:7 type:EV_EFI_VARIABLE_DRIVER_CONFIG(dbx) sha1:0x734424C9FE8FC71716C42096F4B74C88733B175E 
pcr:7 type:EV_SEPARATOR sha1:0x9069CA78E7450A285173431B3E52C5C25299E473 
pcr:7 type:EV_EFI_VARIABLE_AUTHORITY(db) sha1:0x61D410E43D69120FB55734386F9F8D703F07EB76 
pcr:7 type:EV_EFI_VARIABLE_AUTHORITY(db) sha1:0xC0524512A5008C40743B06C6F0496CB38F86A5FF
```
