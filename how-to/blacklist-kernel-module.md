# Blacklist Kernel Module



In this example there is a module named `rc-pinnacle-pctv-hd` that should be blacklisted.

* Connect with SSH to your LibreELEC box.
* list all used modules \(search for rc, ir or cir\)

  ```text
  lsmod
  ```

* add blacklisted module to blacklist.conf

  ```text
  echo "blacklist rc-pinnacle-pctv-hd" > /storage/.config/modprobe.d/blacklist.conf
  ```

* reboot

