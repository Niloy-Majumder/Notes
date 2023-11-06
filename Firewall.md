
Firewall match the network traffic against the rule set defined in its table. Once the rule is matched, associate action is applied to the network traffic. For example, Rules are defined as any employee from HR department cannot access the data from code server and at the same time another rule is defined like system administrator can access the data from both HR and technical department. Rules can be defined on the firewall based on the necessity and security policies of the organization.

## Different types of firewalls

|Firewall Type|Description|
|---|---|
|Proxy Firewall|An early type of firewall device that acts as a gateway for specific applications, offering functionality like content caching and security by preventing direct outside network connections. However, it can impact throughput and supported applications.|
|Stateful Inspection Firewall|A traditional firewall that allows or blocks traffic based on state, port, and protocol, monitoring activity from connection opening to closure. Filtering decisions are based on rules and context from previous connections.|
|Unified Threat Management (UTM) Firewall|Combines stateful inspection firewall, intrusion prevention, antivirus, and additional services. Emphasizes simplicity and ease of use.|
|Next-Generation Firewall (NGFW)|An advanced firewall that includes intelligence-based access control, intrusion prevention, application awareness and control, upgrade paths for future information feeds, and techniques to address evolving security threats.|
|Threat-Focused NGFW|Expands on NGFW capabilities by providing advanced threat detection and remediation, context awareness, intelligent security automation, network and endpoint event correlation, and unified policies for protection.|
|Virtual Firewall|Deployed as a virtual appliance in private or public cloud environments, monitoring and securing traffic across physical and virtual networks, often used in software-defined networks (SDN).|
|Cloud Native Firewall|Modern firewalls designed to secure applications and workload infrastructure at scale, with features like automated scaling, multi-tenancy support, and smart load balancing.|


|Firewall Type|Description|
|---|---|
|Host-based Firewalls|Installed on each network node to control incoming and outgoing packets. It's a software application or suite of applications, often part of the operating system. Protects individual hosts from attacks and unauthorized access.|
|Network-based Firewalls|Operate at the network level, filtering all incoming and outgoing traffic across the network. Protect the internal network by applying rules defined in the firewall. Typically, a dedicated system with proprietary software and multiple network interface cards (NICs).|

