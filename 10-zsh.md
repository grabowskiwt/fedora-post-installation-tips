[< Previous: Shell Color Scripts](9-colorscripts.md) | [Next: SDDM Login Manager Configuration >](11-sddm.md)

---

# 10. Zsh Shell & Plugins Setup

1. **Install Zsh, Fzf, and the Plugins:**
   ```shell
   sudo dnf install zsh fzf zsh-autosuggestions zsh-syntax-highlighting
   ```

2. **Load the Plugins from Your Zsh Configuration:**

   Installing the Fedora packages does not automatically load the
   plugins. Add their source commands to your `.zshrc`, which is
   maintained in your dotfiles repository. Load
   `zsh-autosuggestions` before `zsh-syntax-highlighting`; syntax
   highlighting should be sourced last so it can wrap the configured
   Zsh widgets. Add these lines after your other interactive Zsh
   setup, with syntax highlighting last:
   ```shell
   source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
   source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
   ```

3. **Change Default Shell to Zsh:**
   ```shell
   chsh -s "$(which zsh)"
   ```
   *(Log out and log back in for changes to take effect).*

---

[< Previous: Shell Color Scripts](9-colorscripts.md) | [Next: SDDM Login Manager Configuration >](11-sddm.md)
