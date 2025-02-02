# Update Cartographer Klipper repo

If you already have cartographer-klipper cloned locally, and especially if you had switched to the beta to flash 5.1.0 firmware, you need to pull down the latest master and switch to it, which you can do like this:

```bash
cd $HOME/cartographer-klipper
git fetch
git switch master
git reset --hard origin/master
```
