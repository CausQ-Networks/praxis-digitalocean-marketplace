# Praxis DigitalOcean Marketplace 1-Click App

<div align="center">
  <a href="https://praxis.causq.com/">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/CausQ-Networks/praxis/main/public/logo/praxis-logo-white.png">
      <img alt="Praxis Logo" src="https://raw.githubusercontent.com/CausQ-Networks/praxis/main/public/logo/praxis-logo.png" width="280">
    </picture>
  </a>
</div>

This repository contains Packer templates and scripts to build a DigitalOcean Marketplace 1-Click App for [Praxis](https://github.com/CausQ-Networks/praxis), a self-hosted tunneled mesh reverse proxy with access control.

## TL;DR - Quick Build

Packer will create a DigitalOcean droplet, install Praxis and its dependencies, then save the system as a reusable image (snapshot) for the Marketplace.

```bash
# Install Packer plugin
packer init plugins.pkr.hcl

# Set API token
export DIGITALOCEAN_API_TOKEN=your_digitalocean_token

# Validate and build
packer validate praxis-24-04/template.json
packer build praxis-24-04/template.json
```

## Prerequisites

- [Packer](https://www.packer.io/downloads) (v1.7.0 or higher)
- [DigitalOcean Personal Access Token](https://docs.digitalocean.com/reference/api/create-personal-access-token/)
- The [DigitalOcean Packer plugin](https://developer.hashicorp.com/packer/plugins/builders/digitalocean) (for Packer 1.7.0+)

## Directory Structure

```
praxis-marketplace/
├── common/
│   ├── files/
│   │   └── var/
│   │       └── lib/
│   │           └── digitalocean/
│   └── scripts/
│       ├── 010-docker.sh
│       ├── 018-force-ssh-logout.sh
│       ├── 020-application-tag.sh
│       └── 900-cleanup.sh
└── praxis-24-04/
    ├── scripts/
    │   ├── 010-praxis.sh
    │   └── 020-firewall.sh
    └── template.json
```

## Building the Image

1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/praxis-marketplace.git
   cd praxis-marketplace
   ```

2. For Packer 1.7.0+, initialize the DigitalOcean plugin:
   ```bash
   # Create plugins.pkr.hcl file
   cat > plugins.pkr.hcl << EOF
   packer {
     required_plugins {
       digitalocean = {
         version = ">= 1.4.1"
         source  = "github.com/digitalocean/digitalocean"
       }
     }
   }
   EOF

   # Initialize plugins
   packer init plugins.pkr.hcl
   ```

3. Set your DigitalOcean API token as an environment variable:
   ```bash
   export DIGITALOCEAN_API_TOKEN=your_digitalocean_token
   ```

4. Create the necessary directory structure if it doesn't already exist:
   ```bash
   mkdir -p common/files/var/lib/digitalocean
   ```

5. Validate the template:
   ```bash
   packer validate praxis-24-04/template.json
   ```

6. Build the image:
   ```bash
   packer build praxis-24-04/template.json
   ```

7. The build will output a snapshot ID. Note this ID for submission to the Marketplace.

## Submitting to the DigitalOcean Marketplace

1. Log in to the [DigitalOcean Vendor Portal](https://cloud.digitalocean.com/vendorportal).

2. Submit your app with the following details:
   - **Name**: Praxis
   - **Version**: 1.2.0
   - **Software Included**:
     - Praxis 1.2.0
     - Docker CE (latest)
   - **Image ID**: [The snapshot ID from the Packer build]

3. Complete the rest of the submission form with relevant details about Praxis.

## For End Users

Once the image is approved and available in the Marketplace, users can deploy Praxis by:

1. Selecting "Praxis" from the DigitalOcean Marketplace.
2. Creating a Droplet based on the image.
3. Pointing a domain to the Droplet's IP address.
4. SSH'ing into the Droplet, where they'll be guided through initial setup.

## Troubleshooting

If you encounter issues during the build:

1. Add the `-debug` flag to prompt for confirmation at each build step:
   ```bash
   packer build -debug praxis-24-04/template.json
   ```

2. Use the `-on-error=ask` flag to debug failed builds:
   ```bash
   packer build -on-error=ask praxis-24-04/template.json
   ```

3. Enable verbose logging:
   ```bash
   PACKER_LOG=1 packer build praxis-24-04/template.json
   ```

## License

Praxis is dual licensed under the AGPL-3 and the Fossorial Commercial license.
