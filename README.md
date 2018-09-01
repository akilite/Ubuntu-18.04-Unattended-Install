UBUNTU-18.04-DESKTOP UNATTENDED INSTALL
===
I have created an unattended ubuntu installation. 

REQUIRED
---
1) Preseed file/kickstart file  
2) isolinux.cfg

HOW TO INSTALL
---
1) Command line mode  
2) Graphics mode

**COMMAND LINE MODE**

Step-1  
Creat /iso directory inside /mnt and copy the downloaded 'ubuntu-18.04-Desktop-amd64.iso' to /mnt/iso directory.

		mkdir -p /mnt/iso
		mount -o loop /home/user/Downloads/ubuntu-18.04-Desktop-amd64.iso /mnt/iso

Step-2  
Create /ubuntuiso directory inside /opt and copy 'ubuntu-18.04-Desktop-amd64.iso' from /mnt/iso directory to /opt/ubuntuiso directory. Now you can edit the iso file inside /opt/ubuntuiso directory.

		mkdir -p /opt/ubuntuiso
		cp -rT /mnt/iso /opt/ubuntuiso

Step-3  
Edit the file isolinux.cfg (/opt/ubuntuiso/isolinux/isolinux.cfg path) and replace everything with below content.

		default live-install
		label live-install
			menu label ^Install Ubuntu
			kernel /casper/vmlinuz
			append  file=/cdrom/ks.preseed auto=true priority=critical debian-installer/locale=en_US keyboard-configuration/layoutcode=us ubiquity/reboot=true languagechooser/language-name=English countrychooser/shortlist=US localechooser/supported-locales=en_US.UTF-8 boot=casper automatic-ubiquity initrd=/casper/initrd.lz quiet splash noprompt noshell ---

Step-4  
-Create a file named ks.preseed (/opt/ubuntuiso/ks.preseed path) and paste the below content as it is.  
-The below preseed file suits good to above isolinux.cfg file.  
-This preseed file includes all disk partition, network, timezone, package, mirrors, account(user/root), grub. This is the one make installation automatic and this preseed file called by above isolinux.cfg file. 

		### Partitioning
		#d-i partman/early_command string debconf-set partman-auto/disk "$(list-devices disk | head -n1)"
		d-i partman-lvm/confirm boolean true
		d-i partman-auto-lvm/guided_size string max
		d-i partman-auto/method string crypto
		d-i partman-lvm/device_remove_lvm boolean true
		d-i partman-md/device_remove_md boolean true
		d-i partman-auto/choose_recipe select boot-crypto
		d-i partman-auto-lvm/new_vg_name string crypt
		d-i partman-partitioning/confirm_write_new_label boolean true
		d-i partman/choose_partition select finish
		d-i partman/confirm boolean true
		d-i partman/confirm_nooverwrite boolean true
		### Locale
		d-i debian-installer/locale string en_US
		d-i console-setup/ask_detect boolean false
		d-i console-setup/layoutcode string us
		### Network
		d-i netcfg/get_hostname string unassigned-hostname
		d-i netcfg/get_domain string unassigned-domain
		d-i netcfg/choose_interface select auto
		### Clock
		d-i clock-setup/utc-auto boolean true
		d-i clock-setup/utc boolean true
		d-i time/zone string US/Pacific
		d-i clock-setup/ntp boolean true
		# Packages, Mirrors, Image
		d-i base-installer/kernel/override-image string linux-server
		d-i base-installer/kernel/override-image string linux-image-amd64
		d-i mirror/country string US
		d-i mirror/http/proxy string
		d-i apt-setup/restricted boolean true
		d-i apt-setup/universe boolean true
		d-i pkgsel/install-language-support boolean false
		tasksel tasksel/first multiselect ubuntu-desktop
		### Users
		#mkpasswd -m sha-512 #type this in terminal and generate encrypted password
		d-i passwd/user-fullname string <User Full Name>
		d-i passwd/username string <Username>
		d-i passwd/user-password-crypted password <encrypted-user-password>
		d-i passwd/root-login boolean true
		d-i passwd/root-password-crypted password <encrypted-root-password>
		d-i user-setup/allow-password-weak boolean true
		### Grub
		d-i grub-installer/grub2_instead_of_grub_legacy boolean true
		d-i grub-installer/only_debian boolean true
		d-i finish-install/reboot_in_progress note
		### Custom Commands

Step-5  
Run the below command and create your new-ubuntu.iso (Ubuntu-18.04-desktop-autoinstall-amd64.iso) file.

		mkisofs -D -r -V UNATTENDED_UBUNTU_INSTALL -cache-inodes -J -l -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -o /opt/Ubuntu-18.04-desktop-autoinstall-amd64.iso /opt/ubuntuiso


**GRAPHICS MODE**

1)  Download and install ISO Master tool from Ubuntu Software. This tool helps to reduce above 1st two steps.  

2)  Open ISO Master tool, choose the original iso file. It will be in top panel. Double click on it, files inside iso file will be display in bottom panel.  

3)  Now start editing.  

4)  Repeat Step-3 and Step-4  

5)  Instead of Step-5, it's here  
	File --> Save as --> new-ubuntu.iso file <<Ubuntu-18.04-desktop-autoinstall-amd64.iso>>


