[< Previous: System Theming & Appearance](12-theming.md) | [Next: Main Page >](README.md)

---

# 13. KDE Optimization & Speedups

1. **Reduce File Indexing (Optional):**

   In System Settings > File Search, disable content indexing or exclude
   build outputs, virtual machines, and frequently changing directories.

   Disable Baloo if you do not need indexed search in Dolphin or KRunner:
   ```shell
   balooctl6 disable
   ```

   To enable it again:
   ```shell
   balooctl6 enable
   ```
2. **Stop Akonadi If Unused (Optional):**

   Skip if you use KMail, KOrganizer, KAddressBook, or other Akonadi
   apps. Close dependent apps before stopping it:
   ```shell
   akonadictl stop
   ```

   This stops the current instance only; dependent apps may restart it.

   Start it manually:
   ```shell
   akonadictl start
   ```
3. **Desktop Effects:** In System Settings > Window Manager > Desktop Effects, disable **Blur**, **Fade**, and **Sliding Popups**.
4. **Animations:** Set Animation Speed to **Instant** in Workspace > General Behavior.
5. **User Feedback:** Set to **None** in Security & Privacy > User Feedback.

---

[< Previous: System Theming & Appearance](12-theming.md) | [Next: Main Page >](README.md)
