###  **ELB Options — ALB vs. NLB vs. GWLB**

Knowing WHICH load balancer to pick is a key SAA-C03 skill. The exam describes requirements and expects you to match them.

| Feature / Metric | ALB (Application) | NLB (Network) | GWLB (Gateway) |
| :--- | :--- | :--- | :--- |
| **OSI Layer** | Layer 7 (HTTP/HTTPS/WebSocket/gRPC) | Layer 4 (TCP/UDP/TLS) | Layer 3+4 (IP Packets) |
| **Protocol Support** | HTTP, HTTPS, WebSocket, gRPC | TCP, UDP, TLS | GENEVE (port 6081) packet encapsulation |
| **Performance & Latency** | High throughput; millisecond (ms) latency | Extreme performance; millions of req/sec; microsecond (us) latency | Transparent to traffic; low-latency packet inspection |
| **Health Checks** | HTTP/HTTPS-based | TCP, HTTP, HTTPS | TCP, HTTP, HTTPS |
| **Target Types** | EC2, IP, Lambda, Containers (ECS) | EC2, IP, ALB (as target) | EC2 instances running security appliances |
| **Sticky Sessions** | Yes (app or duration-based cookies) | Yes (source IP) | Flow Stickiness (2/3/5-tuple IP flows) |
| **WebSocket** | Native support | Yes (Layer 4 pass-through) | N/A |
| **Fixed IP / EIP** | No (use NLB or Global Accelerator for fixed IP) | Yes (Elastic IP per AZ, static private IP) | No (uses endpoint IP) |
| **Security Groups (SGs)** | Yes (Required to control inbound/outbound) | Yes (Supported directly on NLB) | No (Assigned to target security appliances) |
| **Cross-Zone LB** | Always enabled by default (free; can be disabled) | Disabled by default (can be enabled; paid cross-AZ transfer) | Disabled by default (can be enabled; paid cross-AZ transfer) |
| **TLS Offloading / SNI** | Yes (terminates SSL, supports SNI) | Yes (terminates TLS, supports SNI) | No (transparent Layer 3 pass-through) |
| **Use Case** | Web apps, microservices, content routing | TCP/UDP apps, gaming, IoT, high performance, NLB → ALB pattern | Deploy 3rd-party virtual appliances (firewalls, IDS/IPS, security inspection) |
| **Exam Keywords** | "HTTP routing", "host/path-based routing", "microservices", "Lambda" | "static IP", "TCP/UDP", "extreme performance", "gaming", "whitelisting" | "intrusion detection", "firewall appliance", "security inspection", "GENEVE" |

<br>

| ⚠️ SAA-C03 EXAM TRAP: Fixed IP Whitelisting |
| :--- |
| • **NLB** provides static IP addresses (one per AZ) — use this when clients need to whitelist fixed IPs. <br>• **ALB** has no fixed IPs. <br>• **Pattern**: Place **NLB** in front of **ALB** to get fixed IPs while retaining Layer 7 routing. |

<br>

####  **ALB Advanced Routing Features**

| Routing Rule Type | Description | SAA-C03 Exam Example / TG Routing |
| :--- | :--- | :--- |
| **Host-based** | Route based on HTTP Host header | `api.example.com` → API TG; `app.example.com` → App TG |
| **Path-based** | Route based on URL path | `/images/*` → Image servers TG; `/api/*` → API TG |
| **HTTP Header** | Route based on any HTTP header value | `User-Agent: iPhone` → Mobile backend TG |
| **HTTP Method** | Route based on GET/POST/PUT etc. | `POST /orders` → Order service TG |
| **Query String** | Route based on query string parameters | `?platform=mobile` → Mobile TG |
| **Fixed Response** | Return HTTP 200/301/302 directly from ALB | Maintenance page, static response |
| **Redirect Action** | ALB redirects HTTP → HTTPS | Enforce HTTPS without backend code modification |
| **Lambda Target** | ALB invokes Lambda directly as target | Serverless web apps behind ALB |

<br>

####  **Connection Draining / Deregistration Delay**

| Metric / Scenario | Details | SAA-C03 Exam Action / Recommendation |
| :--- | :--- | :--- |
| **What it is** | When an instance is deregistered or marked unhealthy, existing connections are kept alive for up to 3600 seconds (default 300s) to complete in-flight requests. | Keep existing connections active until in-flight requests finish before removal. |
| **Scenario: Interrupted Uploads** | Long-running uploads/requests are being interrupted during deployments. | **Increase** deregistration delay (connection draining timeout). |
| **Scenario: Slow Deployments** | Deployments are slow because instances stay in draining state too long. | **Decrease** deregistration delay (connection draining timeout). |


--------------------------------------------------------------------------------

