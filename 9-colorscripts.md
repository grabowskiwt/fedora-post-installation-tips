[< Previous: System Fonts Setup](8-fonts.md) | [Next: Zsh Shell & Plugins Setup >](10-zsh.md)

---

# 9. Shell Color Scripts

Install terminal color scripts (`shell-color-scripts` by dwt1) into `~/.local/bin`:

```shell
mkdir -p "$HOME/.local/bin"
git clone --depth 1 "https://gitlab.com/dwt1/shell-color-scripts.git" /tmp/shell-color-scripts
cp -r /tmp/shell-color-scripts/colorscripts/* "$HOME/.local/bin/"
chmod +x "$HOME/.local/bin/"*
rm -rf /tmp/shell-color-scripts
```

---

[< Previous: System Fonts Setup](8-fonts.md) | [Next: Zsh Shell & Plugins Setup >](10-zsh.md)
