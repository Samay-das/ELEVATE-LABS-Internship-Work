# Day 7 : Configure Load Balancing and Auto Scaling for a Web Application

## Load balancing

Google Cloud offers server-side load balancing so you can distribute incoming traffic across multiple virtual machine (VM) instances. Load balancing provides the following benefits:

1. Scale your app
2. Support heavy traffic
3. Detect and automatically remove unhealthy VM instances using health checks. Instances that become healthy again are automatically re-added.
4. Route traffic to the closest virtual machine

Google Cloud load balancing uses forwarding rule resources to match certain types of traffic and forward it to a load balancer. For example, a forwarding rule can match TCP traffic destined to port 80 on IP address 192.0.2.1, then forward it to a load balancer, which then directs it to healthy VM instances.

Google Cloud load balancing is a managed service, which means its components are redundant and highly available. If a load balancing component fails, it is restarted or replaced automatically and immediately.

## Autoscaling

Compute Engine offers autoscaling to automatically add or remove VM instances from a managed instance group (MIG) based on increases or decreases in load. Autoscaling lets your apps gracefully handle increases in traffic, and it reduces cost when the need for resources is lower. You can autoscale a MIG based on its CPU utilization, Cloud Monitoring metrics, schedules, or load balancing serving capacity.

When you set up an autoscaler to scale based on load balancing serving capacity, the autoscaler watches the serving capacity of an instance group and scales when the VM instances are over or under capacity. The serving capacity of an instance can be defined in the load balancer's backend service and can be based on either utilization or requests per second.


Application Load Balancing (HTTP/HTTPS) is implemented at the edge of Google's network in Google's points of presence (POP) around the world. User traffic directed to an Application Load Balancer (HTTP/HTTPS) enters the POP closest to the user and is then load-balanced over Google's global network to the closest backend that has sufficient available capacity.

## Task 1. Set the default region and zone for all resources

Set the default region:

       gcloud config set compute/region Region

In Cloud Shell, set the default zone:

       gcloud config set compute/zone Zone

## Task 2. Create multiple web server instances

For this load balancing scenario, you create three Compute Engine VM instances and install Apache on them, then add a firewall rule that allows HTTP traffic to reach the instances.

The code provided sets the zone to Zone. Setting the tags field lets you reference these instances all at once, such as with a firewall rule. These commands also install Apache on each instance and gives each instance a unique home page.

1. Create a virtual machine, www1, in your default zone using the following code:

       gcloud compute instances create www1 \
       --zone=Zone \
       --tags=network-lb-tag \
       --machine-type=e2-small \
       --image-family=debian-11 \
       --image-project=debian-cloud \
       --metadata=startup-script='#!/bin/bash
          apt-get update
          apt-get install apache2 -y
          service apache2 restart
          echo "
       <h3>Web Server: www1</h3>" | tee /var/www/html/index.html'

2. Create a virtual machine, www2, in your default zone using the following code:

       gcloud compute instances create www2 \
       --zone=Zone \
       --tags=network-lb-tag \
       --machine-type=e2-small \
       --image-family=debian-11 \
       --image-project=debian-cloud \
       --metadata=startup-script='#!/bin/bash
         apt-get update
         apt-get install apache2 -y
         service apache2 restart
         echo "
       <h3>Web Server: www2</h3>" | tee /var/www/html/index.html'

3. Create a virtual machine, www3, in your default zone.

       gcloud compute instances create www3 \
       --zone=Zone  \
       --tags=network-lb-tag \
       --machine-type=e2-small \
       --image-family=debian-11 \
       --image-project=debian-cloud \
       --metadata=startup-script='#!/bin/bash
         apt-get update
         apt-get install apache2 -y
         service apache2 restart
         echo "
       <h3>Web Server: www3</h3>" | tee /var/www/html/index.html'

4. Create a firewall rule to allow external traffic to the VM instances:

       gcloud compute firewall-rules create www-firewall-network-lb \
           --target-tags network-lb-tag --allow tcp:80

Now you need to get the external IP addresses of your instances and verify that they are running.

5. Run the following to list your instances. You'll see their IP addresses in the EXTERNAL_IP column:

       gcloud compute instances list

6. Verify that each instance is running with curl, replacing [IP_ADDRESS] with the external IP address for each of your VMs:

       curl http://[IP_ADDRESS]

## Task 3. Create an Application Load Balancer
Application Load Balancing is implemented on Google Front End (GFE). GFEs are distributed globally and operate together using Google's global network and control plane. You can configure URL rules to route some URLs to one set of instances and route other URLs to other instances.

Requests are always routed to the instance group that is closest to the user, if that group has enough capacity and is appropriate for the request. If the closest group does not have enough capacity, the request is sent to the closest group that does have capacity.

To set up a load balancer with a Compute Engine backend, your VMs need to be in an instance group. The managed instance group provides VMs running the backend servers of an external application load balancer. For this lab, backends serve their own hostnames.

1. First, create the load balancer template:

       gcloud compute instance-templates create lb-backend-template \
       --region=Region \
       --network=default \
       --subnet=default \
       --tags=allow-health-check \
       --machine-type=e2-medium \
       --image-family=debian-11 \
       --image-project=debian-cloud \
       --metadata=startup-script='#!/bin/bash
         apt-get update
         apt-get install apache2 -y
         a2ensite default-ssl
         a2enmod ssl
         vm_hostname="$(curl -H "Metadata-Flavor:Google" \
       
       http://169.254.169.254/computeMetadata/v1/instance/name)"
         echo "Page served from: $vm_hostname" | \
         tee /var/www/html/index.html
         systemctl restart apache2'

Managed instance groups (MIGs) let you operate apps on multiple identical VMs. You can make your workloads scalable and highly available by taking advantage of automated MIG services, including: autoscaling, autohealing, regional (multiple zone) deployment, and automatic updating.

2. Create a managed instance group based on the template:

       gcloud compute instance-groups managed create lb-backend-group \
       --template=lb-backend-template --size=2 --zone=Zone

3. Create the fw-allow-health-check firewall rule.

       gcloud compute firewall-rules create fw-allow-health-check \
        --network=default \
        --action=allow \
        --direction=ingress \
        --source-ranges=130.211.0.0/22,35.191.0.0/16 \
        --target-tags=allow-health-check \
        --rules=tcp:80

**Note**: The ingress rule allows traffic from the Google Cloud health checking systems (130.211.0.0/22 and 35.191.0.0/16). This lab uses the target tag allow-health-check to identify the VMs

4. Now that the instances are up and running, set up a global static external IP address that your customers use to reach your load balancer:

       gcloud compute addresses create lb-ipv4-1 \
        --ip-version=IPV4 \
        --global

Notice that the IPv4 address that was reserved:

       gcloud compute addresses describe lb-ipv4-1 \
        --format="get(address)" \
        --global

Note: Save this IP address, as you need to refer to it later in this lab.

5. Create a health check for the load balancer (to ensure that only healthy backends are sent traffic):

       gcloud compute health-checks create http http-basic-check \
        --port 80

6. Create a backend service:

       gcloud compute backend-services create web-backend-service \
        --protocol=HTTP \
        --port-name=http \
        --health-checks=http-basic-check \
        --global

7. Add your instance group as the backend to the backend service:

       gcloud compute backend-services add-backend web-backend-service \
        --instance-group=lb-backend-group \
        --instance-group-zone=Zone \
        --global

8. Create a URL map to route the incoming requests to the default backend service:

       gcloud compute url-maps create web-map-http \
        --default-service web-backend-service

---
**Note**: URL map is a Google Cloud configuration resource used to route requests to backend services or backend buckets. For example, with an external Application Load Balancer, you can use a single URL map to route requests to different destinations based on the rules configured in the URL map:

* Requests for https://example.com/video go to one backend service.

* Requests for https://example.com/audio go to a different backend service.

* Requests for https://example.com/images go to a Cloud Storage backend bucket.

* Requests for any other host and path combination go to a default backend service.

---

9. Create a target HTTP proxy to route requests to your URL map:

       gcloud compute target-http-proxies create http-lb-proxy \
        --url-map web-map-http

10. Create a global forwarding rule to route incoming requests to the proxy:

        gcloud compute forwarding-rules create http-content-rule \
         --address=lb-ipv4-1\
         --global \
         --target-http-proxy=http-lb-proxy \
         --ports=80

**Note**: A forwarding rule and its corresponding IP address represent the frontend configuration of a Google Cloud load balancer. Learn more about the general understanding of forwarding rules from the Forwarding rules overview guide.

## Task 4. Test traffic sent to your instances

1. On the Google Cloud console in the Search field type Load balancing, then choose Load balancing from the search results.

2. Click on the load balancer that you just created, web-map-http.

3. In the Backend section, click on the name of the backend and confirm that the VMs are Healthy. If they are not healthy, wait a few moments and try reloading the page.

4. When the VMs are healthy, test the load balancer using a web browser, going to http://IP_ADDRESS/, replacing IP_ADDRESS with the load balancer's IP address that you copied previously.

**Note**: This may take three to five minutes. If you do not connect, wait a minute, and then reload the browser.

Your browser should render a page with content showing the name of the instance that served the page, along with its zone (for example, Page served from: lb-backend-group-xxxx).

## Reference :

Google Skill Boost : 
[Set Up Application Load Balancers](https://www.skills.google/focuses/126803?catalog_rank=%7B%22rank%22%3A4%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=58208669)