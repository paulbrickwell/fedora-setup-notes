# Fedora Setup Notes

Just some personal notes made public in case they help anyone else out.

## NVIDIA Driver with Secure Boot and LUKS Encrypted Drive

This is my process for Fedora KDE Plasma Desktop 43 with an RTX 5080, but should work with slight variations. GPUs older than the 600/700 series require different steps. I followed several different, significantly more complex guides and after a lot of testing, this is by far the cleanest and fastest way to get drivers running (for my setup at least).

> This assumes secure boot is already enabled in the BIOS and drive is encrypted with LUKS.

1. Enable the RPM Fusion Repo from within Discover by clicking **Settings** and enabling **RPM Fusion for Fedora 43 - Nonfree - NVIDIA Driver**.

2. Update system from within Discover by clicking **Updates** and then **Update All**.

3. Reboot the computer

4. Install dependencies for key enrollment and generate a key:

`sudo dnf install kmodtool akmods mokutil openssl`

`sudo kmodgenca -a`

> NOTE: The following command will ask for a password. You are _creating_ a password here to enter when enrolling the MOK. Make it simple and memorable.

`sudo mokutil --import /etc/pki/akmods/certs/public_key.der`

_Enter new password_

5. Reboot the computer

6. On boot you'll see the MOK Management screen. Press any key to start, then: **Enroll MOK** → **Continue** → **Yes** → _enter password created in step 4_ → **Reboot**

7. Install NVIDIA drivers. These will auto-sign now with the previous steps completed:

`sudo dnf install akmod-nvidia`

`sudo dnf install xorg-x11-drv-nvidia-cuda`

Verify drivers are installed, should return version: `modinfo -F version nvidia`

> NOTE: DO NOT reboot yet. Step 8 is critical for LUKS Encrypted drives and rebooting first will give you a black screen.

8. Enable drivers for boot to show LUKS password prompt:

Open config file: `sudo nano /etc/dracut.conf.d/nvidia.conf`

Add this line to the file: `add_drivers+=" nvidia nvidia_modeset nvidia_uvm nvidia_drm "`

Save changes with CTRL+X, Y, ENTER

Rebuild: `sudo dracut --force`

9. Reboot and enjoy!

## Installing Steam

1. Might seem obvious, but enable the RPM Fusion Repo from within Discover by clicking **Settings** and enabling **RPM Fusion for Fedora 43 - Nonfree - Steam**.

2. Then, search for and install Steam. Install the one from Fedora Linux, not Flathub.

## Installing Element

1. Install Element from Discover

2. Grant Element access to the system keyring: `sudo flatpak override --talk-name=org.kde.kwalletd6 im.riot.Riot`

