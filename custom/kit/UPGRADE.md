# Trunk Recorder Pro 0.5.2-custom-v40: upgrade steps

v40 = official **0.5.2** plus our changes: the EDACS data-burst fix, SoapySDR Find keeping the
RSPduo's tuner mode, SoapySDR live AGC / gain stages, the EDACS tail trim, Setup's Live / Next
Start marks and Find list, the page-tone edge fix, the plugin page's settings-first layout, and
the EDACS multi-site rule (copies on the air together are one transmission), rewritten for 0.5.1's
new duplicate rules. The AUXTIN temperature fix is now upstream's own.

## Before you upgrade

- Coming from v38 / v39 (0.5.0): nothing to change first. The store, config and plugins carry over.
- Plugins (Page alerts, Health, Rdio Scanner, …) keep working as they are: the plugin SDK changes
  are additive. Update any that offer an update afterwards.
- Airspys now use TRP's own built-in driver only (libairspy is no longer used). Nothing to set;
  the `TRUNK_PRO_AIRSPY*` environment variables, if any were set, now do nothing.

## Steps (on each box: rdio, rdioa, multi)

1. Back up the config folder:

       cp -a ~/.config/trunk-pro ~/trunk-pro-config-backup-$(date +%F)

2. Unpack:

       cd ~/Downloads
       rm -rf trunk-pro-0.5.2-custom-v40-build-kit
       tar xzf trunk-pro-0.5.2-custom-v40-build-kit.tar.gz
       cd trunk-pro-0.5.2-custom-v40-build-kit

3. Build (10-20 minutes; recording keeps running). The last line must say
   `trunk-pro 0.5.2-custom-v40`:

       bash build.sh

4. Stop, back up the program, install (from this same folder):

       B=$(command -v trunk-pro)
       systemctl --user stop trunk-pro
       sudo cp "$B" "$B.prev"
       sudo install -m 755 trunk-pro-0.5.2-custom "$B"
       trunk-pro --version

5. Real-time priority for the radios (repeat after every update):

       sudo setcap cap_sys_nice+ep "$B"

6. Start it (on rdioa allow a minute: the 30 s boot delay):

       systemctl --user start trunk-pro
       sleep 60
       systemctl --user is-active trunk-pro

   It must print `active`. Hard-refresh the page (Ctrl+Shift+R): the top-left says
   **v0.5.2-custom-v40**.

7. Plugins page: press **Update to v…** on any plugin that offers it.

## Going back

    B=$(command -v trunk-pro)
    systemctl --user stop trunk-pro
    sudo install -m 755 "$B.prev" "$B"
    sudo setcap cap_sys_nice+ep "$B"
    rm -rf ~/.config/trunk-pro
    cp -a ~/trunk-pro-config-backup-<date> ~/.config/trunk-pro
    systemctl --user start trunk-pro
