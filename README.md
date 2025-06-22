# R-D-document-on-the-working-of-NSG-ASG
---
## Introduction
- In today’s digital world, cloud computing has become a central part of IT infrastructure. Organizations are increasingly moving their services and workloads to cloud platforms like Microsoft Azure because of the flexibility, scalability, and cost-efficiency they offer. However, along with the benefits comes the need for strong network security. As more applications and data are hosted in the cloud, ensuring secure access and protecting resources from unauthorized users becomes crucial.

- In Azure, networking tools like Network Security Groups (NSGs) and Application Security Groups (ASGs) help control and monitor traffic to and from resources. These tools allow you to apply rules to virtual networks and virtual machines (VMs) so that only the necessary traffic is allowed. For example, you can allow access from your own IP address and block all other internet access. This level of control is important in protecting sensitive applications or internal resources.

- This research and development (R&D) report focuses on exploring the key networking components of Azure that help in securing virtual environments. The aim is to understand how NSGs and ASGs work, how we can allow only specific IPs to access VMs, how to block internet access, and how to manage public IPs. We'll also learn about static and dynamic IP allocation, service tags, and how to create and associate networking components like network interfaces (NICs) and public IPs to VMs.
  
- Each of these topics plays an important role in building a secure and efficient cloud environment. For example, service tags make it easier to manage security rules without having to remember IP ranges. Static public IPs are useful when you need to map DNS or allow access to applications via a known IP. Dynamic IPs are more suitable for temporary or development workloads.
---
---
## Network Security Group (NSG) and Application Security Group (ASG)
- In Microsoft Azure, managing access to virtual machines and other resources is a key part of ensuring a secure environment. Two important components that help with this are Network Security Groups (NSGs) and Application Security Groups (ASGs). Both of these tools are used to filter network traffic to and from Azure resources based on defined rules.
Network Security Group (NSG)
- An NSG acts like a firewall. It contains a set of rules that allow or deny traffic based on parameters such as source IP address, destination IP, port number, and protocol (TCP/UDP). NSGs can be associated with either a subnet or a network interface (NIC) attached to a virtual machine. When associated with a subnet, the rules apply to all resources within that subnet. When associated with a NIC, the rules apply only to that specific VM.
Each NSG can have multiple inbound and outbound rules, and each rule has a priority number (lower numbers have higher priority). The rules are processed in order of priority, and once a match is found, the rule is applied.
ASGs simplify the management of NSG rules when you have multiple VMs performing the same role. For example, if you have a group of web servers, instead of writing NSG rules for each server’s IP, you can place them in one ASG. Then, NSG rules can target the ASG rather than individual IP addresses.
- ASGs are particularly useful in dynamic environments where the number of VMs might increase or decrease. When a new VM is added to an ASG, it automatically inherits the NSG rules applied to that group, making security management scalable and less error-prone.

  ---
## Allowing Specific IPs and Denying Internet Access
- In cloud environments like Azure, controlling who can access your virtual machines (VMs) and services is critical for maintaining security. One of the most common requirements is to allow only specific IP addresses (such as your office or personal IP) to access a VM while blocking all other traffic, including internet access. This is a key security best practice, especially for production systems or sensitive data environments.
- How to Allow Specific IPs
- To allow access from only specific IPs, you use a Network Security Group (NSG) attached to either the VM's network interface or the subnet in which the VM resides. Within this NSG, you define an inbound security rule that permits traffic from your trusted IP address.
- Steps:
- Create or edit an NSG from the Azure portal.
- Add an inbound rule with the following settings:
- Priority: e.g., 100
- Source: IP Addresses
- Source IP Address: Your specific IP (e.g., 122.161.23.45)
- Destination Port Ranges: 22 for SSH (Linux) or 3389 for RDP (Windows)
- Protocol: TCP
- Action: Allow

---
##	Ensure that any generic rules like “Allow from any IP” have lower priority or are removed.
- This configuration ensures that only your IP address can connect to the VM over the selected port, providing a strong first layer of defense.
- How to Deny Internet Access
- Sometimes, you want your VM to operate only within the internal network and completely block access to or from the public internet. This can be done by adding an outbound rule in the NSG that explicitly denies internet-bound traffic.
- Steps:
- 1.	In the same NSG, create a new outbound rule.
- 2.	Set the following:
- o	Priority: 200
- o	Destination: Service Tag → Internet
- o	Action: Deny
- o	Port Range: *
- o	Protocol: Any


  
This will block all traffic going to the internet while still allowing traffic within the virtual network, such as between subnets or between VMs in the same VNet.
Use Case Example
Imagine you have a database server that should only be accessed by the application server and never exposed to the public internet. You can:
•	Create a rule that allows traffic only from the application server’s private IP or ASG.
•	Deny all other inbound traffic.
•	Block outbound traffic to the internet.
---
---
## Public IPs in Azure – Types and Use Cases
- Public IP addresses in Azure are used to communicate with resources over the internet. When you create a virtual machine (VM), load balancer, or other service that needs to be accessible from the internet, Azure assigns it a public IP address. This IP address acts as a gateway between the Azure resource and users outside the Azure network.
- Types of Public IP Addresses in Azure
- There are two main types based on IP allocation:
 
- 1.	Dynamic Public IP
 - 	Assigned automatically when the resource is started.
 - 	IP address may change if the resource is deallocated and started again.
 - 	Suitable for testing environments or short-term projects.
 - 	Less expensive than static IPs.
- 2.	Static Public IP
 - 	IP address remains the same across restarts, reassignments, or reboots.
 - 	Ideal for production systems where DNS mapping or firewall rules depend on a fixed IP.
 - 	Slightly more expensive, but necessary for stable public-facing service.
 
 
SKUs (Stock Keeping Units) – Basic vs. Standard
Azure also provides two SKUs for public IPs:
•	Basic Public IP:
o	Works with any region and is available to all Azure customers.
o	Does not support zone redundancy or advanced DDoS protection.
o	Can be assigned to resources like VMs and load balancers.
•	Standard Public IP:
o	Offers better performance, enhanced security, and availability zone support.
o	Can be used with Standard Load Balancer.
o	Requires additional configuration and is more secure by default (inbound traffic is blocked unless allowed explicitly).
Common Use Cases
1.	Hosting a Website or Application:
Assign a public IP to a VM or load balancer so that users can access the site globally.
2.	Remote Management:
Use public IPs to connect to VMs via SSH (Linux) or RDP (Windows) from your own system.
3.	DNS Mapping:
When using a domain name (like mycompany.com), you often map it to a static public IP address for consistency.
4.	API Gateways and Services:
APIs exposed to external clients often need static public IPs for predictable access.





5. Static vs. Dynamic IPs
Static IP Address
A static IP address is one that does not change over time. When you allocate a static IP to a resource in Azure, that address is reserved for your subscription and stays the same even if the resource is restarted, shut down, or reassigned.
Features:
•	Remains constant across reboots or reassignments.
•	Useful when you need a predictable IP address for DNS mapping or firewall rules.
•	Slightly higher cost compared to dynamic IPs.
•	Can be used with services like application gateways, VPN gateways, and front-end web applications.
Use Case: You are hosting a business website or an API server that clients connect to regularly. You can use a static IP and map your domain name (e.g., www.myapp.com) to it in DNS records. This way, your service is always accessible at the same IP.
________________________________________
Dynamic IP Address
A dynamic IP address is assigned to a resource from a pool of available IPs. It is not reserved and may change each time the resource is stopped and started again.
Features:
•	IP is assigned at runtime.
•	Might change if the VM is deallocated or restarted.
•	Ideal for short-term or non-critical resources.
•	Cost-effective for development or test environments.
Use Case: You are creating a virtual machine for learning, testing, or short-term use. Since the IP address does not need to remain the same, a dynamic IP is a cost-saving option.
6. Service Tags in NSGs
When configuring security in Azure using Network Security Groups (NSGs), managing IP addresses for well-known Azure services can be complex and error-prone. That’s where Service Tags come in. Service Tags are predefined, named labels provided by Azure that represent a group of IP address ranges associated with a particular Azure service. They simplify NSG rule creation and improve maintainability and security.
What is a Service Tag?
A Service Tag is a logical name for a group of IP addresses. Instead of typing long or changing IP lists manually in your NSG rules, you can simply refer to a service tag like Internet, Virtual Network, AzureLoadBalancer, or Storage. Azure manages the underlying IPs for each tag and keeps them updated.

________________________________________
Examples of Common Service Tags
1.	Internet: Represents all traffic that originates from outside the Azure virtual network.
2.	VirtualNetwork : Refers to all the address space within your virtual network, including any connected VNets via peering.
3.	AzureLoadBalancer : Includes the IP ranges used by Azure's default load balancer to probe VMs and distribute traffic.
4.	Storage: Refers to Azure Storage services like Blob, Queue, and Table services. Used when you want your VMs to access only Azure Storage.
5.	AppGatewayBackendPool: Represents the backend pool IPs for Application Gateway, useful when setting inbound access rules.
________________________________________
Why Use Service Tags?
•	Simplifies Management:
You no longer need to manage changing IP ranges manually. Azure updates service tag mappings automatically.
•	Reduces Human Error:
Prevents configuration mistakes that might happen while typing or copying IPs.
•	Keeps Rules Readable and Organized:
A rule saying “Allow from Storage” is easier to understand than one with a list of storage IP ranges.
•	Improves Automation and Scale:
Useful in large deployments where rules are applied using scripts or templates.
________________________________________










7. Assigning Static Public IPs to VMs
In Microsoft Azure, public IP addresses are used to allow access to virtual machines (VMs) and services from the internet. While dynamic IPs are fine for temporary use, many real-world applications require a static public IP address—an IP that doesn’t change over time. Assigning a static public IP to your VM ensures stable DNS mappings, reliable connectivity, and easier firewall or security configurations.
________________________________________
Why Use a Static Public IP?
A static IP remains the same even if the virtual machine is stopped, deallocated, or rebooted. This is important when:
•	You want to map a custom domain to your VM.
•	You want to create firewall rules that allow traffic only from or to a known IP.
•	You need to whitelist your application with external services or clients.
•	You want to avoid connectivity issues due to changing IPs.
________________________________________
Steps to Allocate and Assign a Static Public IP to a VM
There are two main approaches:
A. During VM Creation
1.	In the Azure Portal, go to Create a Virtual Machine.
2.	In the Networking tab:
o	Select Create New under Public IP.
o	Set Assignment to Static instead of Dynamic.
o	Choose a name for the public IP.
3.	Complete VM creation. The static IP will now be linked with your VM.
B. For an Existing VM
1.	Go to the VM’s Networking section in the Azure Portal.
2.	Click on the Network Interface linked to the VM.
3.	Under IP Configurations, click on the existing IP configuration.
4.	Under Public IP address, click Associate → Choose Static.
5.	Create a new static IP or use an existing one.
________________________________________
Best Practices
•	Use static public IPs only when necessary. If the VM doesn’t need internet access, avoid assigning a public IP at all.
•	Combine static IPs with Network Security Groups (NSGs) to control who can access your VM.
•	Consider using Azure DNS if you want to map a domain to the static IP.
