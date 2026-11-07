[< Previous: System Fonts Setup](8-fonts.md) | [Next: Zsh Shell & Plugins Setup >](10-zsh.md)

---

# 9. Shell Color Scripts

Install terminal color scripts (`shell-color-scripts` by dwt1) into `~/.local/bin`:

```shell
mkdir -p "$HOME/.local/bin"
colors_tmp="$(mktemp -d /tmp/shell-colorscripts.XXXXXX)"
git clone --depth 1 "https://gitlab.com/dwt1/shell-color-scripts.git" "$colors_tmp/repo"
for script in "$colors_tmp"/repo/colorscripts/*; do
  [ -f "$script" ] || continue
  cp "$script" "$HOME/.local/bin/"
  chmod +x "$HOME/.local/bin/${script##*/}"
done
rm -rf -- "$colors_tmp"
```

---

[< Previous: System Fonts Setup](8-fonts.md) | [Next: Zsh Shell & Plugins Setup >](10-zsh.md)
