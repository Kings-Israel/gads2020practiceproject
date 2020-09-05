# LAB: VPC NETWORKING

## Objectives

 - Explore the default VPC network
 - Create an auto mode network with firewall rules
 - Convert an auto mode network to a custom mode network
 - Create custom mode VPC networks with firewall rules
 - Create VM instances using Compute Engine
 - Explore the connectivity for VM instances across VPC networks

## STEPS:

1. List VPC networks

    `gcloud compute networks list`

2. List subnetworks (default network)

    `gcloud compute networks subnets list`

3. List routes

    `gcloud compute routes list`

3. List firewall rules

    `gcloud compute firewall-rules list`

4. Delete firewall rules(in the default network)

    `gcloud compute firewall-rules delete default-allow-internal`
    `gcloud compute firewall-rules delete default-allow-rdp`
    `gcloud compute firewall-rules delete default-allow-ssh`
    `gcloud compute firewall-rules delete default-allow-icmp`

5. Delete VPC network (default network)

    `gcloud compute networks delete default`

6. Create VPC network (Auto mode)

    `gcloud compute networks create mynetwork --project=qwiklabs-gcp-04-c5596b64c253 --subnet-mode=auto --bgp-routing-mode=regional`

7. Create firewall rules

    `gcloud compute firewall-rules create mynetwork-allow-icmp --project=qwiklabs-gcp-04-c5596b64c253 --network=projects/qwiklabs-gcp-04-c5596b64c253/global/networks/mynetwork --description=Allows\ ICMP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=icmp`

    `gcloud compute firewall-rules create mynetwork-allow-internal --project=qwiklabs-gcp-04-c5596b64c253 --network=projects/qwiklabs-gcp-04-c5596b64c253/global/networks/mynetwork --description=Allows\ connections\ from\ any\ source\ in\ the\ network\ IP\ range\ to\ any\ instance\ on\ the\ network\ using\ all\ protocols. --direction=INGRESS --priority=65534 --source-ranges=10.128.0.0/9 --action=ALLOW --rules=all`

    `gcloud compute firewall-rules create mynetwork-allow-rdp --project=qwiklabs-gcp-04-c5596b64c253 --network=projects/qwiklabs-gcp-04-c5596b64c253/global/networks/mynetwork --description=Allows\ RDP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 3389. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389`

    `gcloud compute firewall-rules create mynetwork-allow-ssh --project=qwiklabs-gcp-04-c5596b64c253 --network=projects/qwiklabs-gcp-04-c5596b64c253/global/networks/mynetwork --description=Allows\ TCP\ connections\ from\ any\ source\ to\ any\ instance\ on\ the\ network\ using\ port\ 22. --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22`

8. Create VM instance in mynetwork us-central1 region

    `gcloud beta compute --project=qwiklabs-gcp-04-c5596b64c253 instances create mynet-us-vm --zone=europe-west1-c --machine-type=n1-standard-1 --subnet=mynetwork --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=273403533376-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200805 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mynet-us-vm --reservation-affinity=any`

9. Create VM instance in mynetwork europe-west1 region

    `gcloud beta compute --project=qwiklabs-gcp-04-c5596b64c253 instances create mynet-eu-vm --zone=europe-west1-c --machine-type=n1-standard-1 --subnet=mynetwork --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=273403533376-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200805 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=mynet-eu-vm --reservation-affinity=any`

10. Test connectivity between VMs

    1. SSH into mynet-us-vm

        `gcloud compute ssh mynet-us-vm`

    2. Ping mynet-eu-vm

        `ping -c 3 mynet-eu-vm`


11. Switch mynetwork from Auto mode to custom mode

    `gcloud compute networks update mynetwork --switch-to-custom-subnet-mode`

12. Create a custom mode network (managementnet with managementsubnet subnet in us-central1)

    `gcloud compute networks create managementnet --project=qwiklabs-gcp-04-c5596b64c253 --subnet-mode=custom --bgp-routing-mode=regional`

    `gcloud compute networks subnets create managementsubnet-us --project=qwiklabs-gcp-04-c5596b64c253 --range=10.130.0.0/20 --network=managementnet --region=us-central1`

13. Create privatenet custom mode network with privatesubnet subnet in us-central1

    `gcloud compute networks create privatenet --subnet-mode=custom`

14. Create subnet for privatenet

    `gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24`

15. Create subnet in privatenet in europe-west1 region

    `gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20`

16. List networks

    `gcloud compute networks list`

17. List subnetowrks sorted by networks

    `gcloud compute networks subnets list --sort-by=NETWORK`

18. Create firewall rules in managementnet network

    `gcloud compute --project=qwiklabs-gcp-04-c5596b64c253 firewall-rules create management-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0`

19. Create firewall rule in privatenet network

    `gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0`

20. List firewall rules sorted by network

    `gcloud compute firewall-rules list --sort-by=NETWORK`

21. Create VM instance in managementnet network

    `gcloud beta compute --project=qwiklabs-gcp-04-c5596b64c253 instances create management-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=managementsubnet-us --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=273403533376-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-9-stretch-v20200805 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=management-us-vm --reservation-affinity=any`

22. Create VM instance in privatenet network

    `gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us`

23. List all VM instances sorted by zone

    `gcloud compute instances list --sort-by=ZONE`

24. SSH into mynet-us-vm

    `ssh mynet-us-vm`

25. Test for connectivity across VMs

    1. Ping mynet-eu-vm using external IP

        `ping -c 3 <mynet-eu-vm external IP>`

    2. Ping managementnet-us-vm using internal IP

        `ping -c 3 <managementnet-us-vm internal IP>`

    3. Ping privatent-us-vm internal using internal IP

        `ping -c 3 <privatenet-us-vm internal IP>`