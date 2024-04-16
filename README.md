# HashiCrop `packer` Template of Debian 12 (bookworm)
This repo contains a `packer` template for building a Vagrant Base Box of [Debian 12.5](https://www.debian.org/releases/bookworm/) (aka bookworm).

You can also find these boxes on Vagrant cloud:
 - [vagrantup.com/aphorise/boxes/debian12](https://app.vagrantup.com/aphorise/boxes/debian12)

For ARM64 / AARCH64 builds used on AppleSilicon (M1-M3) see: [github.com/aphorise/hashicorp.packer-arm64-debian12-bookworm](https://github.com/aphorise/hashicorp.packer-arm64-debian12-bookworm).

### Prerequisites
Ensure that you already have the following applications installed & working:
 - [**Virtualbox**](https://www.virtualbox.org/)
 - [**Virtualbox Guest Additions (VBox GA)**](https://download.virtualbox.org/virtualbox/)
 - > **MacOS** (aka OSX) - VirtualBox 6.x+ is expected to be shipped with the related .iso present under (eg):
 `/Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso`
You may however need to download the .iso specific to your version (mount it) and execute the VBoxDarwinAdditions.pkg
 - [**Vagrant**](https://www.vagrantup.com/)
 - [**Packer**](https://www.packer.io/)


## Usage
1. Set `"iso_checksum": "sha512:..."` getting the required SUM via the version specific URL & SHASUM path eg:
   - [https://cdimage.debian.org/cdimage/release/ :warning: _***12.5.0***_ :warning: /amd64/iso-cd/SHA512SUMS](https://cdimage.debian.org/cdimage/release/12.5.0/amd64/iso-cd/SHA512SUMS)

2. Make all changes as required (eg: `d-i mirror/country string Netherlands` in `ui-input.http/pressed_debian12-bookworm.cfg`) and thereafter commence with build using `packer` CLI:
    ```bash
    packer validate debian12-bookworm.json && \
    # if output: Template validated successfully. \
    packer build debian12-bookworm.json
    ```

3. Add Box & Test using Vagrant when box is produced:
    ```bash
    vagrant box add --name debian_x86-64 vbox-debian-x86_64-12.5.0.box ;
    vagrant init debian_x86-64 ;
    vagrant up ;
    # // when done:
    vagrant destroy -f && vagrant box remove debian_x86-64 ;
    ```

The resulting Vagrant Base **\*.box** file will be produced in the root of the repository (where no issues / errors).

## Last Run

```
  # Build 'virtualbox-iso.vbox-debian-x86_64-12.5.0' finished after 12 minutes 15 seconds.
```

```bash
date '+%Y-%m-%d %H:%M:%S' && uname -a && \
 vboxmanage --version && packer version && vagrant version ;
  # 2024-04-16 11:08:10
  # Linux ... 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux
  # 7.0.14r161095
  # Packer v1.10.2
  # Installed Version: 2.4.0
  # Latest Version: 2.4.1
```


## Notes
Make adjustments to use appropriate mirrors (which are insecure ftp or http presently) where intended to extend to make proper use beyond a mere practise / training.

If your development & delivery resource specifications differ (eg - you'd like to use more resource locally) then then update (`debian12-bookworm.json`) by adding the below noted **`vboxmanage_post`** block (after: *`vboxmanage`*) within the *`builders`* section as well its related **`variables`**:
```
"variables":
{
    ...
 	"cpu_cores_delivery": "1",
	"memory_delivery": "1024",
    ...
},
"builders":
[{
    ...
	"vboxmanage_post":
	[
		[ "modifyvm", "{{.Name}}", "--memory", "{{user `memory_delivery`}}" ],
		[ "modifyvm", "{{.Name}}", "--cpus", "{{user `cpu_cores_delivery`}}" ]
	],
    ...
}],
```


## Reference & Credits:
Accreditation due for material that are from other repo's which also worth referencing:
 * [deimosfr/packer-debian](https://github.com/deimosfr/packer-debian)
 * [rgl/debian-vagrant](https://github.com/rgl/debian-vagrant)
------
