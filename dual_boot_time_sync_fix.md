# Dual boot time sync fix



When running macOS & Windows at the same system time can be out of sync when changing between systems. The source of the problem that Operations systems stores the time in a hardware clock on the motherboard, but macOS stores UTC time and windows stores local time. In order to fix this issue we need to set Windows to store UTC time.

## Make Windows Store UTC Time

### Automatically

1. Download [set_windows_store_utc_time.reg](https://raw.githubusercontent.com/teimore/OpenCore_GA-H97-D3H_RX580/master/_static/dual_boot_time_sync_fix/set_windows_store_utc_time.reg) (Right click > Save Link as...)
2. Run it as Administrator
3. Restart

### Manually 

1. Open `regedit` 

2. Go to

   ```
   HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation
   ```

3. Add new `DWORD (32-bit)` value (Right click on `TimeZoneInformation` > New > `DWORD (32-bit) Value`)
4. Name the value `RealTimeIsUniversal` and set value data to `1`
5. Save it & restart.



### Disable the fix

1. Open `regedit` 

2. Go to

   ```
   HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation
   ```

3. Delete the key `RealTimeIsUniversal`

4. Save it & restart.
