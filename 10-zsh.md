[< Previous: Shell Color Scripts](9-colorscripts.md) | [Next: SDDM Login Manager Configuration >](11-sddm.md)

---

# 10. Zsh Shell & Plugins Setup

1. **Install Zsh, Fzf, and the Plugins:**
   ```shell
   sudo dnf install zsh fzf zsh-autosuggestions zsh-syntax-highlighting
   ```

2. **Load the Plugins from Your Zsh Configuration:**

   Fedora packages do not load plugins automatically. Add these lines
   to your dotfiles-managed `.zshrc`, after other interactive setup and
   in this order:
   ```shell
   source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
   source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
   ```

3. **Change Default Shell to Zsh:**
   ```shell
   chsh -s "$(which zsh)"
   ```
   Log out and back in to apply.

---

[< Previous: Shell Color Scripts](9-colorscripts.md) | [Next: SDDM Login Manager Configuration >](11-sddm.md)
