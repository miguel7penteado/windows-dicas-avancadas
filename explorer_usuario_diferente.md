1. Run **regedit.exe** elevated.
2. Take ownership of **HKEY_CLASSES_ROOT\AppID\{CDCBCFCA-3CDC-436f-A4E2-0E02075250C2}**.
3. Rename the **RunAs** value to `_RunAs`.
4. Create a new shortcut on your Desktop for **C:\Windows\System32\runas.exe**.
5. Name it something like `Admin-Explorer`.
6. Right-click the new shortcut and click **Properties**.
7. Change the target to: `C:\Windows\System32\runas.exe /noprofile /user:<domain>\<username> "c:\windows\explorer.exe /separate"`
8. (Optional) Change the icon.
9. (Optional) Pin to taskbar.
