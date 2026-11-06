[< Previous: System Theming & Appearance](12-theming.md) | [Next: Main Page >](README.md)

---

# 13. KDE Optimization & Speedups

1. **Reduce File Indexing (Optional):**

   In System Settings, open File Search. Disable content indexing or
   exclude directories containing build outputs, virtual machines,
   and other frequently changing files.

   If you do not use indexed file search in Dolphin or KRunner,
   disable Baloo completely:
   ```shell
   balooctl6 disable
   ```

   To enable it again:
   ```shell
   balooctl6 enable
   ```
2. **Stop Akonadi If Unused (Optional):**

   Skip this step if you use KMail, KOrganizer, KAddressBook, or other
   applications that depend on Akonadi. Otherwise, close those
   applications before stopping it:
   ```shell
   akonadictl stop
   ```

   This stops the current instance; it does not permanently disable
   Akonadi. Applications that require it may start it again.

   To start it manually:
   ```shell
   akonadictl start
   ```
3. **Desktop Effects:** In System Settings > Window Manager > Desktop Effects, disable **Blur**, **Fade**, and **Sliding Popups**.
4. **Animations:** Set Animation Speed to **Instant** in Workspace > General Behavior.
5. **User Feedback:** Set to **None** in Security & Privacy > User Feedback.

---

[< Previous: System Theming & Appearance](12-theming.md) | [Next: Main Page >](README.md)
