---
name: strettch-cloud
description: Provision and manage compute instances on Strettch Cloud via the Public API. Use when the user mentions Strettch, strettch.cloud, api.strettch.cloud, KGL-1, Kigali cloud infrastructure, or needs to create, start, stop, reboot, upsize, or delete cloud compute instances through the Strettch REST API.
---

# Strettch Cloud API Skill

A skill that gives AI full context on how to use the Strettch Cloud Public API to provision and manage compute instances.

## Overview

Strettch Cloud is an Africa-first cloud infrastructure platform. The Public API lets you provision compute instances, manage infrastructure, and automate cloud workflows through a single REST interface.

- **Base URL:** `https://api.strettch.cloud/api/v1`
- **Auth:** Bearer token via API key
- **Format:** JSON request/response
- **Docs:** https://docs.cloud.strettch.com

> There is no sandbox environment. All API requests interact with live resources.

## Authentication

All endpoints (except `/health`) require a Bearer token:

```http
Authorization: Bearer <your_api_key>
```

API keys are generated from the Strettch Cloud Console (https://cloud.strettch.com/p/settings/api-keys). Keys inherit the permission level (Owner, Admin, or Member) of the team member who created them.

## Endpoints

### Computes

| Method | Endpoint | Description | Status |
|--------|----------|-------------|--------|
| GET | `/computes` | List all compute instances (paginated) | 200 |
| POST | `/computes` | Create a compute instance | 202 |
| GET | `/computes/{id}` | Get a specific compute instance | 200 |
| DELETE | `/computes/{id}` | Permanently delete a compute instance | 202 |
| POST | `/computes/{id}/reboot` | Reboot a compute instance | 202 |
| POST | `/computes/{id}/start` | Start (power on) a compute instance | 202 |
| POST | `/computes/{id}/stop` | Stop (graceful shutdown) a compute instance | 202 |
| PATCH | `/computes/{id}/upsize` | Upgrade compute specifications | 202 |

### Reference Data

| Method | Endpoint | Description | Status |
|--------|----------|-------------|--------|
| GET | `/computes/specifications` | List available compute specs (vCPUs, RAM, storage) | 200 |
| GET | `/images` | List available OS images | 200 |
| GET | `/regions` | List available hosting regions | 200 |

### System

| Method | Endpoint | Description | Status |
|--------|----------|-------------|--------|
| GET | `/health` | Service health check (no auth required) | 200 |

## Request & Response Schemas

### Create Compute Request (POST /computes)

Required headers:

```http
Authorization: Bearer <api_key>
Content-Type: application/json
Idempotency-Key: <uuid_v4>
```

Body:

```json
{
  "hostName": "my-server-01",
  "image": "UBUNTU-24.04",
  "region": "KGL-1",
  "specificationId": "41720b60-c094-4ae7-b6fa-0966451993db",
  "publicKeys": ["ssh-rsa AAAAB3... user@host"],
  "withPublicIp": true,
  "tags": ["production", "web"]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `hostName` | string | Yes | Hostname for the instance |
| `image` | string | Yes | OS image slug from `GET /images` (e.g., `UBUNTU-24.04`) |
| `region` | string | Yes | Region code from `GET /regions` (e.g., `KGL-1`) |
| `specificationId` | string (UUID) | Yes | Spec ID from `GET /computes/specifications` |
| `publicKeys` | string[] | Yes (min 1) | SSH public keys for access |
| `withPublicIp` | boolean | No | Allocate a public IP address |
| `tags` | string[] | No | Labels for the instance |
| `userData` | string (base64) | No | cloud-init user data (`#cloud-config` or a `#!` script), at most 32 KiB decoded |

### Upsize Compute Request (PATCH /computes/{id}/upsize)

```json
{
  "newSpecificationId": "1076fa22-b49b-4b8f-827e-20f84262916c"
}
```

### Success Response

```json
{
  "success": true,
  "message": "Operation successful",
  "data": { ... },
  "requestId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "timestamp": "2024-01-15T10:00:00Z"
}
```

### Paginated Response (list endpoints)

```json
{
  "success": true,
  "message": "Computes retrieved successfully",
  "data": [ ... ],
  "meta": {
    "total": 42,
    "page": 1,
    "limit": 20
  },
  "requestId": "f47ac10b-...",
  "timestamp": "2024-01-15T10:00:00Z"
}
```

Pagination query params: `page` (default: 1), `limit` (default: 20, max: 100)

### Error Response

```json
{
  "success": false,
  "code": "RESOURCE_NOT_FOUND",
  "message": "The requested compute instance does not exist.",
  "requestId": "f47ac10b-...",
  "timestamp": "2024-01-15T10:00:00Z"
}
```

### Compute Response Object

```json
{
  "id": "325",
  "hostName": "my-server-01",
  "state": "RUNNING",
  "image": "UBUNTU-24.04",
  "specificationId": "1076fa22-b49b-4b8f-827e-20f84262916c",
  "region": "KGL-1",
  "ipv4": "195.15.0.73",
  "tags": ["production", "web"],
  "createdAt": "2024-01-15T10:00:00Z",
  "updatedAt": "2024-01-15T10:05:00Z"
}
```

### Specification Response Object

```json
{
  "id": "41720b60-c094-4ae7-b6fa-0966451993db",
  "vcpus": 2,
  "memoryMb": 8000,
  "storageGb": 160
}
```

### Region Response Object

```json
{
  "id": 1,
  "region": "KGL-1",
  "name": "Kigali-1",
  "countryCode": "RW"
}
```

### Image Response Object

```json
{
  "image": "UBUNTU-24.04",
  "imageName": "Ubuntu",
  "version": "24.04",
  "displayName": "Ubuntu 24.04 LTS"
}
```

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `BAD_REQUEST` | 400 | Request body or parameters are malformed |
| `VALIDATION_ERROR` | 400 | Request body failed validation |
| `MALFORMED_JSON` | 400 | Invalid JSON in request body |
| `INVALID_IDEMPOTENCY_KEY` | 400 | Idempotency key is not a valid UUID v4 |
| `UNAUTHORIZED` | 401 | API key is missing, malformed, or revoked |
| `FORBIDDEN` | 403 | Insufficient permissions for this action |
| `INSUFFICIENT_BALANCE` | 403 | Team has insufficient credits |
| `TEAM_SUSPENDED` | 403 | Team account is suspended |
| `TEAM_PAST_DUE` | 403 | Team has overdue payments |
| `TEAM_TERMINATED` | 403 | Team account is terminated |
| `RESOURCE_NOT_FOUND` | 404 | The requested resource does not exist |
| `COMPUTE_NOT_FOUND` | 404 | The specified compute instance does not exist |
| `CONFLICT` | 409 | Resource state conflict |
| `UNPROCESSABLE_ENTITY` | 422 | Request is valid but cannot be processed |
| `TOO_MANY_REQUESTS` | 429 | Rate limit exceeded |
| `INTERNAL_SERVER_ERROR` | 500 | Unexpected server error |

## Idempotency

POST endpoints that create resources require an `Idempotency-Key` header (UUID v4) to prevent duplicate actions on network retries:

```http
Idempotency-Key: 550e8400-e29b-41d4-a716-446655440000
```

A retry with the same key returns the original response with status 202. No duplicate resource is created.

## Rate Limiting

| Tier | Limit |
|------|-------|
| Reads (GET) | 30 requests/second per API key |
| Writes (POST/PATCH/DELETE) | 5 requests/second per API key |

Rate limit headers in every response:

```http
x-ratelimit-limit-second: 30
x-ratelimit-remaining-second: 29
x-ratelimit-reset-second: 1776144940
```

## Typical Workflow: Provision a Compute Instance

When a user asks to create a compute instance, follow these steps:

### Step 1: Fetch available options

Make three parallel requests to get the available configurations:

```bash
# Get available regions
curl -s https://api.strettch.cloud/api/v1/regions \
  -H "Authorization: Bearer $API_KEY"

# Get available images
curl -s https://api.strettch.cloud/api/v1/images \
  -H "Authorization: Bearer $API_KEY"

# Get available specifications
curl -s https://api.strettch.cloud/api/v1/computes/specifications \
  -H "Authorization: Bearer $API_KEY"
```

### Step 2: Create the compute instance

Use the IDs from step 1 to build the create request:

```bash
curl -s -X POST https://api.strettch.cloud/api/v1/computes \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: $(uuidgen)" \
  -d '{
    "hostName": "my-server-01",
    "image": "UBUNTU-24.04",
    "region": "KGL-1",
    "specificationId": "<id_from_specifications>",
    "publicKeys": ["ssh-rsa AAAAB3... user@host"],
    "withPublicIp": true
  }'
```

### Step 3: Verify the instance

Poll or check the instance status:

```bash
curl -s https://api.strettch.cloud/api/v1/computes/{id} \
  -H "Authorization: Bearer $API_KEY"
```

The instance starts in CREATING state and transitions to RUNNING once provisioned.

### Step 4: Connect via SSH

Once the instance is RUNNING and has an ipv4 address:

```bash
ssh root@<ipv4_address>
```

## Managing Compute Lifecycle

```bash
# Stop an instance
curl -s -X POST https://api.strettch.cloud/api/v1/computes/{id}/stop \
  -H "Authorization: Bearer $API_KEY"

# Start an instance
curl -s -X POST https://api.strettch.cloud/api/v1/computes/{id}/start \
  -H "Authorization: Bearer $API_KEY"

# Reboot an instance
curl -s -X POST https://api.strettch.cloud/api/v1/computes/{id}/reboot \
  -H "Authorization: Bearer $API_KEY"

# Upsize an instance
curl -s -X PATCH https://api.strettch.cloud/api/v1/computes/{id}/upsize \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"newSpecificationId": "<new_spec_id>"}'

# Delete an instance (permanent, stops billing)
curl -s -X DELETE https://api.strettch.cloud/api/v1/computes/{id} \
  -H "Authorization: Bearer $API_KEY"
```

## Important Notes

- **Billing:** Compute instances are billed on a pay-as-you-go basis. Stopped instances still incur charges. Only deleting an instance stops billing.
- **State transitions:** Create, start, stop, reboot, upsize, and delete are all asynchronous (return 202 Accepted). Poll GET /computes/{id} to check the current state.
- **Compute states:** CREATING, RUNNING, STOPPED, REBOOTING, DESTROYING, DESTROYED
- **SSH keys are required:** At least one SSH public key must be provided when creating an instance. There is no password-based access.
- **Regions:** Currently available: KGL-1 (Kigali, Rwanda). More regions are planned.
- **Images:** Currently available: UBUNTU-24.04 (Ubuntu 24.04 LTS). Check GET /images for the latest list.
