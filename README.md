# VMware Migration Planning Toolkit

A suite of web-based calculators that help organizations plan VMware-to-cloud migrations — from estimating transfer timelines to building a full financial ROI/TCO business case.

## Features

### Transfer Time Calculator (`migration-calculator.html`)
- **Transfer Technology Comparison**: Compare migration completion times across three transfer methods
- **Migration Timeline Analysis**: Calculate weeks and months needed to migrate your VMs
- **Throughput Metrics**: View VMs per week and per-hour throughput by technology
- **Visual Reports**: Charts comparing completion times and weekly throughput

### ROI & TCO Calculator (`roi-calculator.html`)
- **VMware TCO Analysis**: Full cost breakdown — licensing, support, labor, power, facilities, hardware
- **Cloud Cost Modeling**: Compute, storage, egress, managed services with provider-specific defaults (AWS/Azure/GCP)
- **Multi-Year Projections**: Year-by-year costs with escalation rates (VMware licensing up 15%/yr, cloud pricing down 5%/yr)
- **Financial Metrics**: NPV, IRR, ROI %, and payback period calculations
- **6 Interactive Charts**: TCO comparison, cumulative savings, cost breakdowns (doughnut), year-over-year, sensitivity tornado
- **Sensitivity Analysis**: Interactive sliders for VMware licensing, cloud pricing, and rightsizing assumptions
- **Recommendations Engine**: Context-aware advice on pricing models, Broadcom impact, and migration strategy

### Shared Features
- **PDF Export**: Generate printable reports from either calculator
- **Linked Navigation**: Shared nav bar connects both calculators
- **Interactive Inputs**: All calculations update dynamically

## Technology Stack

- **Frontend**: HTML5, CSS3, JavaScript (ES6+)
- **Charts**: Chart.js for data visualization
- **Deployment**: Containerized with Docker, orchestrated with Kubernetes

## Usage

### Local Development

Open either calculator directly in a web browser:

```bash
open migration-calculator.html   # Transfer time analysis
open roi-calculator.html          # ROI & TCO analysis
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

- **Project Configuration**
  - Total VMs to Migrate
  - Available Concurrent Streams
  - Weeks per Year
  - Migration Windows per Week

- **Window Configuration**
  - Window Duration (hours)
  - Overhead per Window (hours)
  - Efficiency Factor (0-1)

- **Per-VM Data Model**
  - Average Data per VM (GB)
  - Per-VM Overhead (minutes)

## Outputs

- **Completion time by technology** (months)
- **Weekly throughput** (VMs per week per technology)
- **Detailed comparison table** showing:
  - Transfer speed (MB/s)
  - Time per VM (minutes)
  - VMs/hour/stream
  - VMs/week
  - Total weeks needed
  - Total months needed
- **Time savings summary** showing which technology saves the most time

## Transfer Technologies

The calculator compares three transfer methods:

1. **Network Copy** - Standard network transfer (100 MB/s)
2. **XCopy (Array)** - Storage array-based XCopy offload (500 MB/s)
3. **VolCopy (FlashArray)** - Direct FlashArray volume copy (2000 MB/s)

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

- `migration-calculator.html` - Transfer time calculator
- `roi-calculator.html` - ROI & TCO financial calculator
- `Dockerfile` - Container image definition
- `k8s-deployment.yaml` - Kubernetes deployment manifest
- `CLAUDE.md` - Claude Code project guidance
- `README.md` - This file

## Support

For issues, feature requests, or improvements, please refer to the project repository.

## License

Check the repository for license information.
