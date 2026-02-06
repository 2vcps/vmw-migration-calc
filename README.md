# VMware to OpenShift Virtualization Migration Calculator

A web-based calculator that helps organizations estimate the costs and benefits of migrating from VMware virtualization to OpenShift Virtualization (KubeVirt).

## Features

- **Cost Analysis**: Compare lifecycle costs between VMware and OpenShift Virtualization
- **Performance Metrics**: Evaluate performance characteristics and resource requirements
- **ROI Calculation**: Calculate return on investment and cost savings
- **Visual Reports**: Charts and graphs showing cost breakdowns and comparisons
- **PDF Export**: Generate printable reports of calculations
- **Interactive Inputs**: Customize calculations based on your infrastructure

## Technology Stack

- **Frontend**: HTML5, CSS3, JavaScript (ES6+)
- **Charts**: Chart.js for data visualization
- **Deployment**: Containerized with Docker, orchestrated with Kubernetes

## Usage

### Local Development

Simply open `migration-calculator.html` in a web browser:

```bash
open migration-calculator.html
# or
firefox migration-calculator.html
```

### Docker

Build and run the calculator in a Docker container:

```bash
docker build -t vmw-migration-calc .
docker run -p 8080:80 vmw-migration-calc
```

Then access the calculator at `http://localhost:8080`

### Kubernetes

Deploy to a Kubernetes cluster:

```bash
kubectl apply -f k8s-deployment.yaml
```

By default, the service is exposed on port 8080. To access it:

```bash
# Port forward
kubectl port-forward svc/migration-calculator 8080:80

# Or create an ingress route (configure as needed for your cluster)
```

## Inputs

The calculator accepts the following parameters:

- **VMware Inputs**
  - Number of VMs
  - Compute costs (CPU, memory)
  - Storage requirements
  - Licensing costs
  - Support and maintenance costs

- **OpenShift Virtualization Inputs**
  - Cluster infrastructure costs
  - Storage costs
  - Support and licensing
  - Migration timeline and labor

## Outputs

- Total cost of ownership (TCO) comparison
- 5-year financial analysis
- Cost breakdowns by category
- ROI and payback period
- Recommendation based on financial analysis

## Architecture

```
┌─────────────────────────────────┐
│   migration-calculator.html     │
│   (Interactive Web Calculator)  │
├─────────────────────────────────┤
│      Docker Container           │
│   (Nginx Web Server)            │
├─────────────────────────────────┤
│   Kubernetes Deployment         │
│   (Pod, Service, Replica Set)   │
└─────────────────────────────────┘
```

## Requirements

- Docker (for containerized deployment)
- Kubernetes 1.19+ (for K8s deployment)
- Modern web browser with JavaScript enabled
- Chart.js library (loaded via CDN)

## Files

- `migration-calculator.html` - Main application file
- `Dockerfile` - Container image definition
- `k8s-deployment.yaml` - Kubernetes deployment manifest
- `README.md` - This file

## Support

For issues, feature requests, or improvements, please refer to the project repository.

## License

Check the repository for license information.
