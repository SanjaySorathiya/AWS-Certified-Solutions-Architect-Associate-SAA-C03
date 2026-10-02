###  **CloudFront - Content Delivery Network**
CloudFront is AWS's global CDN. It caches content at 400+ Edge Locations worldwide and integrates deeply with AWS services.

####  **CloudFront Key Concepts**
| Concept | Detail | Exam Implication |
| :--- | :--- | :--- |
| **Origin** | Source of truth for CDN: S3 bucket, ALB, EC2, API GW, or any custom HTTP server. | **S3**: Use OAC (not legacy OAI).<br>**ALB/EC2**: Must be public OR use VPC Origins (private). Restrict origin traffic via SGs with CF managed prefix list or custom headers. |
| **Distribution** | Deployment of CF with >=1 origins & behaviors. | Map path patterns to specific origins (e.g., `/api/*` -> ALB, `/*` -> S3). |
| **Edge Location & REC** | **Edge**: Global PoPs caching content closer to users.<br>**REC**: Larger mid-tier caches between Edge & Origin. | Edge caches popular content. REC caches less-popular content to protect Origin from traffic spikes. |
| **Cache Behaviors** | Rules routing requests based on path patterns. | Order matters (specific first). Configures allowed HTTP methods, custom cache policies, and SSL redirects. |
| **TTL & Invalidation** | Cache duration controlled by origin headers (`Cache-Control`, `Expires`) or CF settings (Min/Default/Max). | **Invalidation**: Forces immediate cache clear for path (e.g., `/*`). Useful for rapid updates, but costs apply. |
| **OAC** | Secures private S3 bucket, forcing all access through CF. | Replaces legacy OAI. Supports S3 SSE-KMS, POST requests, and all AWS regions. |
| **S-URLs vs S-Cookies** | Restricts content access to authorized users using trusted key pairs/Key Groups. | **S-URL**: Single file access (e.g., specific download).<br>**S-Cookie**: Multiple files / full directory access (e.g., media streaming). |
| **WAF Integration** | Deploys AWS WAF on CF at Edge. | Stops attacks (DDoS, SQLi, XSS) before hitting Origin. Supports rate-limiting & geo-blocking. |
| **CF-Fn vs L@E** | Edge computing options to manipulate viewer or origin requests. | **CF-Fn**: Ultra-light (JS, <1ms, <2MB, Edge). Viewer-Request/Response only. Best for URL rewrites, header manipulation, basic auth.<br>**L@E**: Heavy (Node/Python, 30s timeout, 10GB RAM, REC). Supports Viewer/Origin Request/Response. Best for DB lookups, API calls, dynamic content. |
| **Field-Level Encryption** | Encrypts specific POST fields (e.g., credit card) at Edge. | Decrypted only at origin server using private key. Ensures PCI-DSS compliance. |
| **Origin Shield** | Centralized caching layer between RECs and Origin. | Maximizes cache hit ratio, protects Origin from concurrent requests across regions, and lowers egress costs. |
| **Custom SSL/TLS & SNI** | Allows custom domain CNAMEs with HTTPS (e.g., `cdn.domain.com`). | Cert must be in ACM **us-east-1** (N. Virginia) to associate with CF.<br>**SNI**: Serves multiple domains' SSL certs on single CF IP. |
| **Geo-Restrictions** | Restricts access by country. | **Built-in restriction**: Basic allowlist/denylist. For advanced matching, use WAF geo-blocking. |
| **Cache Policy vs Origin Request Policy** | Controls how CF handles request parameters. | **Cache Policy**: Determines Cache Key (headers/cookies/query strings that make content unique).<br>**Origin Request Policy**: Forwards parameters to Origin *without* including them in Cache Key (avoids lowering cache hit ratio). |

#### **Feature Comparison: CloudFront vs S3 Transfer Acceleration (S3TA)**
| Feature | CloudFront (CDN) | S3 Transfer Acceleration (S3TA) |
| :--- | :--- | :--- |
| **Primary Use** | Global content delivery (reads) & edge caching. | Fast upload/download of large files to S3. |
| **Caching** | Caches content at 400+ global Edge Locations. | No caching; directly routes to S3 via AWS backbone. |
| **Protocols** | Standard web protocols (HTTP/HTTPS). | S3 API operations (PUT, GET, etc.). |
| **Key Scenarios** | Static/dynamic web assets, video streaming, API caching. | Distant clients uploading GB-TB datasets to central S3 bucket. |

--------------------------------------------------------------------------------
