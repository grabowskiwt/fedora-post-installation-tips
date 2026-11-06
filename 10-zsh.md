[< Previous: Shell Color Scripts](9-colorscripts.md) | [Next: SDDM Login Manager Configuration >](11-sddm.md)

---

# 10. Zsh Shell & Plugins Setup

1. **Install Zsh, Git, and Fzf:**
   ```shell
   sudo dnf install -y zsh git fzf
   ```

2. **Setup Zsh Custom Directory & Plugins:**
   ```shell
   ZSH_CUSTOM="${ZSH_CUSTOM:-$HOME/.zsh}"
   mkdir -p "$ZSH_CUSTOM"

   # Install zsh-autosuggestions
   if [ ! -d "$ZSH_CUSTOM/zsh-autosuggestions" ]; then
     git clone --depth 1 "https://github.com/zsh-users/zsh-autosuggestions" "$ZSH_CUSTOM/zsh-autosuggestions"
   fi

   # Install zsh-syntax-highlighting
   if [ ! -d "$ZSH_CUSTOM/zsh-syntax-highlighting" ]; then
     git clone --depth 1 "https://github.com/zsh-users/zsh-syntax-highlighting" "$ZSH_CUSTOM/zsh-syntax-highlighting"
   fi
   ```

3. **Change Default Shell to Zsh:**
   ```shell
   chsh -s "$(which zsh)"
   ```
   *(Log out and log back in for changes to take effect).*

---

[< Previous: Shell Color Scripts](9-colorscripts.md) | [Next: SDDM Login Manager Configuration >](11-sddm.md)
