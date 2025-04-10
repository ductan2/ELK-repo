# ELK Stack Setup

This repository contains a Docker Compose setup for Elasticsearch, Logstash, Kibana (ELK) stack with APM monitoring capabilities.

## Prerequisites

- Docker
- Docker Compose
- Python 3.x (for backend application)

## Environment Variables

Create a `.env` file in the root directory with the following variables:

```env
STACK_VERSION=8.11.3
CLUSTER_NAME=docker-cluster
ES_PORT=9200
KIBANA_PORT=5601
FLEET_PORT=8220
APMSERVER_PORT=8200
ELASTIC_PASSWORD=your_elastic_password
KIBANA_PASSWORD=your_kibana_password
ENCRYPTION_KEY=your_encryption_key
ELASTIC_APM_SECRET_TOKEN=your_apm_secret_token
ES_MEM_LIMIT=1g
KB_MEM_LIMIT=1g
LICENSE=basic
```

## Setup

1. Clone the repository:
```bash
git clone <repository-url>
cd <repository-name>
```

2. Create the `.env` file with your configuration

3. Start the ELK stack:
```bash
docker-compose up -d
```

4. Wait for all services to start (this may take a few minutes)

## Accessing Services

- Kibana: http://localhost:5601
- Elasticsearch: http://localhost:9200
- Fleet Server: http://localhost:8220
- APM Server: http://localhost:8200

## APM Configuration

### Backend Application Setup

1. Install the Elastic APM client:
```bash
pip install elasticapm
```

2. Configure APM in your Python application:
```python
import os
from elasticapm.contrib.starlette import make_apm_client, ElasticAPM
from elasticapm import instrument

# Initialize APM
apm_config = {
    "SERVICE_NAME": "your-service-name",
    "SERVER_URL": "http://fleet-server:8200",  # If running in Docker
    # or
    # "SERVER_URL": "http://localhost:8200",  # If running outside Docker
    "ENVIRONMENT": "development"
}

# Initialize APM client
_APM = make_apm_client(apm_config)
instrument()

# Add middleware to your FastAPI/Starlette app
app.add_middleware(ElasticAPM, client=_APM)
```

## Monitoring

1. Log into Kibana (http://localhost:5601)
2. Check fleet server and change settings localhost:9200 to es01:9200
3. Navigate to Observability > APM
4. You should see your service listed in the services list

## Troubleshooting

### Common Issues

1. APM Connection Issues
   - Ensure your backend can reach the APM server
   - Check if the APM server is running: `docker-compose ps`
   - View APM server logs: `docker-compose logs fleet-server`

2. Service Not Visible in Kibana
   - Wait a few minutes for data to be indexed
   - Check if your service is sending data correctly
   - Verify the service name in your APM configuration

3. Authentication Issues
   - Ensure the APM secret token matches in both backend and ELK stack
   - Check if anonymous access is enabled in the configuration

## Maintenance

### Restarting Services

```bash
# Restart all services
docker-compose restart

# Restart specific service
docker-compose restart kibana
docker-compose restart fleet-server
```

### Viewing Logs

```bash
# View all logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f kibana
docker-compose logs -f fleet-server
```

### Stopping the Stack

```bash
docker-compose down
```

## Security Notes

- This setup uses basic security settings
- For production, consider:
  - Enabling SSL/TLS
  - Using stronger passwords
  - Implementing proper authentication
  - Restricting network access

## Contributing

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a new Pull Request
