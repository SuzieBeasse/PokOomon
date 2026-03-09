# PokOomon - Self-healing serverless API

## Project Overview

This project demonstrates a **cloud migration from on-premises infrastructure to AWS**, showcasing how a small startup, Oomon, can achieve **reliability, cost-effectiveness, agility, and automation** through modern cloud-native architecture.

The solution implements a **Pokédex API** that enables users to search and retrieve Pokémon information through RESTful endpoints, built entirely on AWS serverless technologies.

---

## Business Objectives

| Objective | How We Achieved It |
|-----------|-------------------|
| **Reliability** | Multi-AZ deployment, automatic scaling, CloudWatch monitoring |
| **Cost-Effectiveness** | Pay-per-use serverless model, eliminated on-prem infrastructure costs |
| **Agility** | Rapid deployment cycles, infrastructure-as-code with SAM |
| **Automation** | Automated scaling, monitoring alerts, CI/CD ready |

---

## Architecture Overview

### Technology Stack

- **Compute**: AWS Lambda (serverless functions)
- **API Management**: Amazon API Gateway
- **Database**: Amazon DynamoDB
- **Monitoring**: Amazon CloudWatch
- **Infrastructure as Code**: AWS SAM (Serverless Application Model)

### System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     Client Requests                         │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
            ┌────────────────────────┐
            │   API Gateway          │
            │  (Request Routing)     │
            └────────────┬───────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
    ┌────────┐      ┌────────┐        ┌────────┐
    │ Lambda │      │ Lambda │        │ Lambda │
    │ /name  │      │  /id   │        │ /type  │
    └────┬───┘      └────┬───┘        └────┬───┘
         │                │                │
         └────────────────┼────────────────┘
                          │
                          ▼
                  ┌──────────────────┐
                  │   DynamoDB       │
                  │  (Pokémon Data)  │
                  └──────────────────┘
                          │
                          ▼
                  ┌──────────────────┐
                  │   CloudWatch     │
                  │  (Monitoring)    │
                  └──────────────────┘
```

---

## API Endpoints

| Endpoint | Method | Description | Example |
|----------|--------|-------------|---------|
| `/pokemon/` | GET | Search all pokémon | `/pokemon` |
| `/pokemon/{id}` | GET | Search Pokémon by id | `/pokemon/52` |
| `/pokemon/name/{name}` | GET | Search Pokémon by name | `/pokemon/name/pikachu` |
| `/pokemon/type/{type}` | GET | Search Pokémon by type | `/pokemon/type/fire` |

### Example Response

```json
{
  "name": "Squirtle",
  "type": ["Water"],
  "generation": 1,
  "Pokedex_number": 7,
  "classification": "tiny turtle Pokemon",
  "hp": 44,
  "attack": 48,
  "defense": 65,
  "spAtk": 50,
  "spDef": 64,
  "speed": 43,
  "weight": 9,
  "height": 0.5,
  "capture_rate": 45,
  "is_legendary": 0,
  "abilities": [ { "S" : "Torrent" }, { "S" : "Rain Dish" } ]
  }

```

---

## Database Schema

### DynamoDB Table: `pokemon`

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | String | Pokémon name |
| `pokedex_number` | Number | National Pokédex number |
| `type` | List | Pokémon types |
| `generation` | Number | Pokémon generation |
| `classification` | String | Pokémon classification |
| `is_legendary` | Boolean | Pokémon legendary status |
| `height_m`|Number|Pokémon height|
| `weight_kg`|Number|Pokémon weight|
| `abilities`|List|Pokémon abilities|
| `attack`|number|Pokémon attack|
| `defence`|number|Pokémon defence|
| `sp_attack`|number|Pokémon special attack|
| `sp_defence`|number|Pokémon special defence|
| `speed`|number|Pokémon speed|
`capture_rate`|number|Pokémon capture rate|

---

## Cost Analysis: Cloud vs On-Premises

### Key Findings

| Metric | On-Premises | AWS Cloud | Savings |
|--------|-------------|-----------|---------|
| **Monthly Cost** | €1,525 | €144 | 90.55% |
| **Annual Cost (Year 1)** | €18,300 | €1,729 | 90.55% |
| **Scalability** | Limited | Unlimited |  🗸 |
| **Maintenance Overhead** | High | Low |  🗸 |

### Cost Breakdown (AWS Monthly)

- **Lambda Invocations**: €0.37
- **API Gateway Requests**: €26.2
- **DynamoDB Storage & Throughput**: €90.43
- **CloudWatch Logs & Monitoring**: €24.30

---

## Getting Started

### Prerequisites

- AWS Account with appropriate permissions
- AWS SAM CLI installed
- Python 3.9+ (or your chosen runtime)
- Git

### Installation & Deployment

1. ** Fork and clone the repository**

2. **Build the SAM application**
   ```bash
   sam build
   ```

3. **Deploy to AWS**
   ```bash
   sam deploy --guided
   ```

4. **Test the API**
   ```bash
   curl https://<api-gateway-url>//pokemon/name/pikachu
   ```

---

## Monitoring & Observability

### CloudWatch Metrics

- **Lambda Invocations**: Track function execution count
- **Lambda Duration**: Monitor function performance
- **Lambda Errors**: Alert on failures
- **API Gateway Latency**: Measure response times
- **DynamoDB Throttling**: Monitor database performance

### Setting Up Alarms

```bash
# Example: Alert on Lambda errors
aws cloudwatch put-metric-alarm \
  --alarm-name pokemon-api-errors \
  --metric-name Errors \
  --namespace AWS/Lambda \
  --statistic Sum \
  --period 300 \
  --threshold 5 \
  --comparison-operator GreaterThanThreshold
```

---

## Future Security Considerations

- API Gateway authentication (API Keys / OAuth)
- DynamoDB encryption at rest
- VPC endpoints for private connectivity
- IAM roles with least privilege
- CloudWatch logging for audit trails

---

## Performance & Scalability

| Aspect | Capability |
|--------|-----------|
| **Concurrent Requests** | Auto-scales to thousands |
| **Database Throughput** | On-demand or provisioned capacity |
| **Cold Start Latency** | ~100-300ms (acceptable for this use case) |
| **Response Time (Warm)** | <50ms average |

---

## Project Structure

```
pokedex-api/
pokedex-api/
├── template.yaml
# SAM template
├── src/
│   ├── pokemon_name/
│   │   └── app.py
# Lambda function for /pokemon/name
│   ├── pokemon_stats/
│   │   └── app.py
# Lambda function for /pokemon/stats
│   └── pokemon_number/
│       └── app.py
# Lambda function for /pokemon/number
├── tests/
│   └── unit/
│       └── test_handlers.py
# Unit tests
├── README.md
# Project documentation
└── requirements.txt
# Python dependencies
```

---

## Testing

### Unit Tests

```bash
python -m pytest tests/unit/ -v
```

### Integration Tests

```bash
sam local start-api
curl http://localhost:3000/pokemon/name/pikachu
```

---

## Key Learnings & Best Practices

1. **Serverless Architecture**: Reduced operational overhead and infrastructure management
2. **Infrastructure as Code**: Reproducible deployments using SAM templates
3. **Cost Optimization**: Pay-per-use model eliminates idle capacity costs
4. **Monitoring First**: CloudWatch integration enables proactive issue detection
5. **Scalability by Design**: Auto-scaling handles traffic spikes automatically

---

## Future Enhancements

- [ ] Add caching layer (ElastiCache / CloudFront)
- [ ] Implement GraphQL endpoint
- [ ] Add authentication (Cognito)
- [ ] Implement rate limiting
- [ ] Add data validation & error handling
- [ ] Expand Pokémon dataset

---

## Support & Contact

For questions or issues, please refer to the project documentation or contact the development team.

---

## License

This project is created for educational and demonstration purposes.

---

## Acknowledgments

- AWS Serverless Application Model (SAM) documentation
- Pokémon API data source: The Complete Pokemon Dataset, Kaggle
- Cloud migration best practices from industry standards

---

**Last Updated**: March 9, 2026
**Project Status**: Archived
