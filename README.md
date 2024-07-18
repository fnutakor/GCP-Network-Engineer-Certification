# GCP-Network-Engineer-Certification
This repository contains Terraform configurations and documentation for the GCP Professional Cloud Network Engineer Certification lab. It includes setups for private and custom networks, firewall rules, and VM instances on Google Cloud Platform. Follow the steps to deploy the infrastructure for your certification.

# GCP Professional Cloud Network Engineer Certification

This repository contains the files and steps for the GCP Professional Cloud Network Engineer Certification lab. The lab involves setting up networks and instances using Terraform.

## Lab Files

- `main.tf`: Main configuration file.
- `provider.tf`: Provider configuration for Google Cloud.
- `managementnet.tf`: Configuration for the management network.
- `privatenet.tf`: Configuration for the private network.
- `mynetwork.tf`: Configuration for the custom network.

## Steps to Complete the Lab

### 1. Set Up the Provider

Configure the Google Cloud provider in `provider.tf`:

```hcl
provider "google" {}

2. Create Networks and Subnetworks
Private Network (privatenet.tf):

resource "google_compute_network" "privatenet" {
  name                    = "privatenet"
  auto_create_subnetworks = false
}

resource "google_compute_subnetwork" "privatesubnet-us" {
  name          = "privatesubnet-us"
  region        = "us-east1"
  network       = google_compute_network.privatenet.self_link
  ip_cidr_range = "172.16.0.0/24"
}

resource "google_compute_subnetwork" "privatesubnet-second-subnet" {
  name          = "privatesubnet-second-subnet"
  region        = "us-west1"
  network       = google_compute_network.privatenet.self_link
  ip_cidr_range = "172.20.0.0/24"
}

Custom Network (mynetwork.tf):

resource "google_compute_firewall" "mynetwork-allow-http-ssh-rdp-icmp" {
  name = "mynetwork-allow-http-ssh-rdp-icmp"
  source_ranges = [
    "0.0.0.0/0"
  ]
  network = google_compute_network.mynetwork.self_link

  allow {
    protocol = "tcp"
    ports    = ["22", "80", "3389"]
  }

  allow {
    protocol = "icmp"
  }
}

module "mynet-us-vm" {
  source              = "./instance"
  instance_name       = "mynet-us-vm"
  instance_zone       = "us-east1-c"
  instance_subnetwork = google_compute_network.mynetwork.self_link
}

module "mynet-second-vm" {
  source              = "./instance"
  instance_name       = "mynet-second-vm"
  instance_zone       = "us-west1-a"
  instance_subnetwork = google_compute_network.mynetwork.self_link
}

3. Deploy the Configuration
Run the following commands to deploy the Terraform configuration:

terraform init
terraform apply
