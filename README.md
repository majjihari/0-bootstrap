# Zero-OS Bootstrap Webservice

This web service will provides dynamic construction of iPXE scripts for booting and bootstrapping Zero-OS kernel images.

## Endpoints

The most simple endpoint is the plain text version:
- `/ipxe/`: generate an iPXE plain text script to boot

You can generate a bootable image with a bundle boot-script via:
- `/iso/`: generate a bootable ISO file
- `/usb/`: generate a bootable USB image file
- `/uefi/`: generate an UEFI bootloader file
- `/uefimg/`: same as above, but an image to be dd'd to an usb stick for UEFI boxes
- `/krn/`: generate directly-bootable kernel

Static target:
- `/krn-generic`: build a generic ipxe kernel, with our SSL certificates authorized
- `/uefi-generic`: build a generic ipxe uefi bootable image, with our SSL certificates authorized
- `/krn-provision`: build a generic ipxe kernel, calling our provisioning endpoint with nic mac address
- `/uefi-provision`: build a generic ipxe uefi bootable, calling our provisioning endpoint with nic mac address
- `/kernel/[name]`: provide the kernel (static file)

## Arguments
All endpoints (except `/krn-generic/` and `/kernel/` which are static) accepts more optional arguments:
```
...endpoint/[branch]/[zerotier-network]/[extra-arguments]
```

Any argument are optional, but are ordered and dependants (eg: you cannot provide extra argument without providing zerotier network)

Theses are valid endpoint example:
- `/ipxe/master`
- `/ipxe/master/8056c2e21c000001`
- `/ipxe/master/8056c2e21c000001/console=ttyS0`
- `/ipxe/master/null/console=ttyS1` (special case to not set zerotier network)

### Branches

You can specify any branches available, the list is displayed on the default webpage of the webservice

### ZeroTier Network ID

The Network ID can be either public or private. If you don't provide ID, Zero-OS will listening for incoming connection on all interface.

The special ID `null` or `0` can be set to still provide extra argument without having Network ID

### Extra Argument

Everything set on the last argument will be forwarded as-it to the kernel argument. You can set spaces, etc.


## Installation

To speedup ISO and USB images creation, the script will use a iPXE-template directory which contains a pre-compiled version of the sources.

To pre-compile code, you can run the `setup/template.sh` script.
This will prepare the template and put it on `/opt/ipxe-template`.

In order to compile correctly the sources, you'll need (ubuntu): `build-essential syslinux liblzma-dev libz-dev genisoimage isolinux wget dosfstools udev`

### Database

Clients can be provisioned on the runtime using a database, you need to create the database, even if it's empty.
Just run: `cat db/schema.sql | sqlite3 db/bootstrap.sqlite3`

## Provision

The endpoint `/provision/[mac-address]` used the database to provide iPXE script generated on the fly for client from
MAC Address provided. This is used by the provision image.

## Run

This is a `Flask` web service, just run the `bootstrap.py` server file. On ubuntu, you'll need `python3-flask`.

Kernel images will be served from `kernel` directory. Images are in form: `zero-os-BRANCH-ARCH.efi`

## Configuration

You can customize the service by editing `config.py`:
- `base-host`: http web address (eg: https://bootstrap.gig.tech)
- `ipxe-template`: iPXE template path (by default, setup script install it to `/opt/ipxe-template`)
- `ipxe-template-uefi`: iPXE UEFI template path (by default, setup script install it to `/opt/ipxe-template-uefi`)
- `kernel-path`: path where to find kernels
- `http-port`: http listen port,
- `debug`: enable (True) or disable (False) debug Flask
- `popular`: list of branches to mark as popular on the homepage
- `popular-description`: list of branches with their description (on the homepage)

## Documentation

For more documentation see the [`/docs`](./docs) directory, where you'll find a [table of contents](/docs/SUMMARY.md).

# Repository Owner
- [Maxime Daniel](https://github.com/maxux), Telegram: [@maxux](http://t.me/maxux)

