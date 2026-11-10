---
name: strettch-cloud-terraform
description: Provision and manage Strettch Cloud infrastructure with Terraform using the official strettch-cloud/strettchcloud provider. Use when the user mentions Strettch, strettchcloud, the Strettch Cloud Terraform provider, terraform-provider-strettchcloud, KGL-1, or needs to write Terraform/HCL to create, update, resize, reboot, import, or destroy Strettch Cloud compute instances, or to look up images, regions, or specifications.
---

# Strettch Cloud Terraform Provider Skill

A skill that gives AI full context to write correct Terraform for Strettch Cloud using the official provider.

## Overview

The Strettch Cloud Terraform provider manages Strettch Cloud infrastructure declaratively.

- **Provider:** `strettch-cloud/strettchcloud`
- **Registry:** https://registry.terraform.io/providers/strettch-cloud/strettchcloud/latest
- **Source:** https://github.com/strettch-cloud/terraform-provider-strettchcloud
- **Docs:** https://docs.cloud.strettch.com
- **Terraform:** any current version; **1.14+** is required for list resources (`terraform query`) and the reboot action.
- **Auth:** API key via the `STRETTCH_CLOUD_API_KEY` environment variable.

## Provider setup

```hcl
terraform {
  required_providers {
    strettchcloud = {
      source  = "strettch-cloud/strettchcloud"
      version = "~> 1.0"
    }
  }
}

provider "strettchcloud" {
  # api_key is read from the STRETTCH_CLOUD_API_KEY environment variable.
}
```

## Authentication

- Set `STRETTCH_CLOUD_API_KEY` in the environment (recommended — keeps the key out of config files):

  ```bash
  export STRETTCH_CLOUD_API_KEY="<your api key>"
  ```

- Or pass `api_key` from a variable or secret store (never a literal in version control). A saved plan (`terraform plan -out`) stores variable values in cleartext even when marked `sensitive`.
- An API key inherits the permission level (Owner, Admin, or Member) of the team member who created it.

## Provider configuration (arguments)

All arguments are optional; each can also be set with the matching `STRETTCH_CLOUD_*` environment variable.

| Argument | Type | Default | Env var |
|----------|------|---------|---------|
| `api_key` | string (sensitive) | — | `STRETTCH_CLOUD_API_KEY` |
| `api_url` | string | `https://api.strettch.cloud/api/v1` | `STRETTCH_CLOUD_API_URL` |
| `requests_per_second` | number | `2` (API ceiling) | `STRETTCH_CLOUD_REQUESTS_PER_SECOND` |
| `http_retry_max` | number | `4` | `STRETTCH_CLOUD_HTTP_RETRY_MAX` |
| `http_retry_wait_min` | number | `1` | `STRETTCH_CLOUD_HTTP_RETRY_WAIT_MIN` |
| `http_retry_wait_max` | number | `30` | `STRETTCH_CLOUD_HTTP_RETRY_WAIT_MAX` |

## Resource: `strettchcloud_compute`

A compute instance (virtual machine).

**Arguments:**

| Argument | Type | Required | Notes |
|----------|------|----------|-------|
| `host_name` | string | yes | Host name. Updated in place. Must be non-empty, no leading/trailing whitespace. |
| `image` | string | yes | Image slug, e.g. `UBUNTU-24.04`. **Changing it replaces the compute.** |
| `region` | string | yes | Region slug, e.g. `KGL-1`. **Changing it replaces the compute.** |
| `specification` | string | yes | Spec slug, e.g. `S-1C-1G`. Raising it **resizes in place (upward only)**; downsizing is an error; the compute is **stopped during the resize**. |
| `public_keys` | set(string) | yes | SSH public keys. **Changing them replaces the compute.** Not recoverable on import. |
| `with_public_ip` | bool | no (default `false`) | Attach a public IP. **Changing it replaces the compute.** |
| `state` | string | no (default `RUNNING`) | `RUNNING` or `STOPPED`. Updated in place via start/stop. |
| `tags` | list(string) | no | Tags. Updated in place. |
| `timeouts` | block | no | `create`, `update`, `delete` (Go durations, e.g. `"20m"`). |

**Read-only:** `id`, `ipv4`, `private_ipv4`, `created_at`, `updated_at`.

```hcl
resource "strettchcloud_compute" "web" {
  host_name      = "web-01"
  image          = "UBUNTU-24.04"
  region         = "KGL-1"
  specification  = "S-1C-1G"
  public_keys    = ["ssh-ed25519 AAAA... you@example.com"]
  with_public_ip = true
  tags           = ["production", "web"]
  # state defaults to "RUNNING"; set "STOPPED" to keep it powered off.
}
```

## Data sources

Use these to read catalog values instead of hardcoding slugs.

| Data source | Purpose | Key arguments | Read-only |
|-------------|---------|---------------|-----------|
| `strettchcloud_compute` | Look up one compute by id | `id` (required) | all compute fields |
| `strettchcloud_image` | Find one image by filter | one of `image`, `image_name`, `version` | `display_name` |
| `strettchcloud_images` | List all images | — | `images[]` (`image`, `image_name`, `version`, `display_name`) |
| `strettchcloud_region` | Find one region by slug | `region` (required) | `country_code`, `id`, `name` |
| `strettchcloud_regions` | List all regions | — | `regions[]` (`region`, `country_code`, `id`, `name`) |
| `strettchcloud_specification` | Find one spec by filter | one of `specification`, `vcpus`, `memory_mb` | `id`, `storage_gb` |
| `strettchcloud_specifications` | List all specs | — | `specifications[]` (`specification`, `vcpus`, `memory_mb`, `storage_gb`, `id`) |

```hcl
# Pick a specification by size without hardcoding its slug.
data "strettchcloud_specification" "medium" {
  vcpus     = 2
  memory_mb = 4096
}

# List every region slug.
data "strettchcloud_regions" "all" {}
output "regions" {
  value = data.strettchcloud_regions.all.regions[*].region
}
```

## Action: `strettchcloud_compute_reboot`

Reboots a running compute (Terraform **1.14+**). The compute must be `RUNNING`.

| Argument | Type | Required | Notes |
|----------|------|----------|-------|
| `compute_id` | string | yes | Id of the compute to reboot. |
| `timeout` | string | no (default `15m`) | How long to wait, as a Go duration. |

```hcl
action "strettchcloud_compute_reboot" "web" {
  config {
    compute_id = strettchcloud_compute.web.id
  }
}

# Or trigger it from a lifecycle event:
resource "strettchcloud_compute" "web" {
  host_name     = "web-01"
  image         = "UBUNTU-24.04"
  region        = "KGL-1"
  specification = "S-1C-1G"
  public_keys   = ["ssh-ed25519 AAAA..."]

  lifecycle {
    action_trigger {
      events  = [after_update]
      actions = [action.strettchcloud_compute_reboot.web]
    }
  }
}
```

## Full working example (`main.tf`)

```hcl
terraform {
  required_providers {
    strettchcloud = {
      source  = "strettch-cloud/strettchcloud"
      version = "~> 1.0"
    }
  }
}

provider "strettchcloud" {
  # api_key is read from STRETTCH_CLOUD_API_KEY
}

data "strettchcloud_specification" "small" {
  specification = "S-1C-1G"
}

resource "strettchcloud_compute" "web" {
  host_name      = "web-01"
  image          = "UBUNTU-24.04"
  region         = "KGL-1"
  specification  = data.strettchcloud_specification.small.specification
  public_keys    = ["ssh-ed25519 AAAA... you@example.com"]
  with_public_ip = true
  tags           = ["production", "web"]
}

output "public_ip" {
  value = strettchcloud_compute.web.ipv4
}
```

Apply it:

```bash
export STRETTCH_CLOUD_API_KEY="<your api key>"
terraform init
terraform plan
terraform apply
```

Connect over SSH (Strettch Cloud exposes SSH on port **222**):

```bash
ssh root@$(terraform output -raw public_ip) -p 222
```

## Import

```bash
terraform import strettchcloud_compute.web <compute-id>
```

After import, the first apply plans an **in-place update** on `public_keys` (never a replace). Set `public_keys` in config to match the key the compute was created with.

## Common errors and gotchas

- **Downsizing is not supported.** `specification` can only be raised; lowering it is an error. Replace the compute if you need a smaller size.
- **A resize stops the compute.** Raising `specification` stops the compute during the resize, then starts it again if its configured state is `RUNNING`. Expect a short outage.
- **Replacement-forcing fields:** changing `image`, `region`, `public_keys`, or `with_public_ip` replaces (destroys and recreates) the compute.
- **`public_keys` is not recoverable on import** — set it in config to match the original key.
- **Terraform 1.14+** is required for list resources (`terraform query`) and the reboot action.
- **`403 FORBIDDEN`** means the API key's role lacks permission for the action; **`401 UNAUTHORIZED`** means the key is missing, malformed, or revoked.

## Catalog values (current)

Always prefer the data sources above over hardcoding, but as of now:

- **Regions:** `KGL-1` (Kigali, Rwanda). More are planned.
- **Images:** `UBUNTU-24.04`. Check `strettchcloud_images` for the latest list.
- **Specifications:** slugs follow `S-{vCPUs}C-{memoryGB}G` (e.g. `S-1C-1G`, `S-1C-2G`, `S-2C-4G`). Check `strettchcloud_specifications` for the full list.
