# Fedora 44 cloud images

Cloud-init-ready, signed Fedora 44 qcow2 images for OpenStack and Proxmox,
part of the [openimages.cloud](https://openimages.cloud) catalog.

- Catalog page: <https://openimages.cloud/images/fedora-44/>
- Registry: <https://images.openimages.cloud/fedora-44/latest/>

## About

Fedora 44 is the previous Fedora release, published by the Fedora Project
([https://fedoraproject.org/](https://fedoraproject.org/)). This repo takes the official upstream
Cloud Base Generic image ([listing](https://dl.fedoraproject.org/pub/fedora/linux/releases/44/Cloud/x86_64/images/)), verifies it against the
upstream SHA-256 checksum, applies the openimages.cloud cloud-init policy,
boot-tests it, signs it with cosign (keyless) and publishes it.

The build pipeline is shared with the rest of
[`open-img-cloud`](https://github.com/open-img-cloud): this repo only ships
`VERSION`, `build/customize.sh`, `build/detect-upstream.sh` and two thin
caller workflows that delegate to the reusable workflows in
[`open-img-cloud/.github`](https://github.com/open-img-cloud/.github).

## Lifecycle

Fedora releases are supported for about 13 months: Fedora 44 until four weeks after the release of Fedora 46. Prefer Fedora 45 for new deployments.

## Image

- File: `fedora-<version>-x86_64.qcow2`, where `<version>` is
  the upstream release and compose (e.g. `44-1.7`).
- Default user: `fedora` (SSH key from cloud-init, passwordless sudo).
- The root filesystem grows to the flavor disk on first boot.

## Use it

```sh
V=44-1.7
curl -fLO https://images.openimages.cloud/fedora-44/$V/fedora-$V-x86_64.qcow2

openstack image create \
    --disk-format qcow2 --container-format bare \
    --property hw_qemu_guest_agent=yes \
    --file fedora-$V-x86_64.qcow2 \
    'Fedora 44 '$V
```

Verification (cosign), Proxmox commands and provenance details are on the
[catalog page](https://openimages.cloud/images/fedora-44/) and in the
[verification guide](https://openimages.cloud/verify/).

## Release flow

1. `watch.yml` runs daily (07:08 UTC) and opens an
   `auto/upstream-bump` pull request when upstream publishes a new image.
2. Merge it, then push the tag `v<VERSION>`: `release.yml` builds, boot-tests,
   signs and publishes the image.

## License

The build tooling in this repository is [GPL-2.0](LICENSE). The image keeps
the licensing terms of Fedora. Fedora is a trademark of Red Hat, Inc. This project is not affiliated with it.
