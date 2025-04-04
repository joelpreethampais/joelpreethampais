# Hybrid NAT Gateway Terraform Module

## Overview
This Terraform module provisions a **Hybrid NAT Gateway** on **Google Cloud Platform (GCP)**. The module automates the creation of NAT Gateways, Cloud Routers, and associated subnet configurations to manage outbound internet traffic for private resources.

## Features
- Automatically assigns NAT Gateways to Cloud Routers.
- Supports multiple applications (`appid`) with their respective CIDR blocks.
- Dynamically configures subnetworks with primary and secondary IP ranges.
- Ensures scalability by managing Cloud Router limits (max 50 NAT Gateways per router).

## Prerequisites
- Terraform v1.6.5 or later
- Google Cloud credentials with the required IAM permissions:
  - `roles/compute.networkAdmin`
  - `roles/compute.admin`
  - `roles/iam.serviceAccountUser`

## Inputs

| Name                  | Type   | Description |
|-----------------------|--------|-------------|
| `config`              | `map`  | NAT gateway configuration including router, app ID, CIDR blocks, and subnet settings. |
| `project_id`          | `string` | GCP Project ID where resources will be deployed. |
| `region`              | `string` | GCP region for deployment. |
| `nat_timeout_settings` | `map`  | Optional timeout settings for NAT Gateway. |

## Example Usage

```hcl
module "hybrid_nat_gateway" {
  source     = "./modules/hybrid-nat-gateway"
  project_id = "my-gcp-project"
  region     = "us-central1"
  config = {
    "router-1-app1" = {
      router = "router-1"
      appid  = "app1"
      cidr   = ["10.24.3.5/29"]
      sbnts  = [
        {
          name                      = "projects/223577/regions/us-central1/subnetworks/my-subnet"
          source_ip_ranges_to_nat   = ["ALL_IP_RANGES"]
          secondary_ip_ranges_names = []
        }
      ]
      min   = 256
      max   = 2048
    }
  }
}
```

## Outputs
| Name            | Description |
|----------------|-------------|
| `nat_gateways` | List of created NAT Gateways with associated Cloud Routers. |
| `cloud_routers` | Cloud Routers assigned to NAT Gateways. |

## Notes
- Ensure the Cloud Router exists before assigning a NAT Gateway.
- If more than 50 NAT Gateways are needed, the module will provision additional Cloud Routers automatically.

## License
This module is released under the MIT License.

