# VMware to OpenShift Virtualization Migration Calculator

A web-based calculator that helps organizations estimate VM migration timelines by comparing different transfer technologies (Network Copy, XCopy Array, and VolCopy FlashArray).

## Features

- **Transfer Technology Comparison**: Compare migration completion times across three transfer methods
- **Migration Timeline Analysis**: Calculate weeks and months needed to migrate your VMs
- **Throughput Metrics**: View VMs per week and per-hour throughput by technology
- **Visual Reports**: Charts comparing completion times and weekly throughput
- **PDF Export**: Generate printable reports of calculations
- **Interactive Inputs**: Customize calculations based on your migration parameters

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

- `migration-calculator.html` - Main application file
- `Dockerfile` - Container image definition
- `k8s-deployment.yaml` - Kubernetes deployment manifest
- `README.md` - This file

## Support

For issues, feature requests, or improvements, please refer to the project repository.

## License

Check the repository for license information.
