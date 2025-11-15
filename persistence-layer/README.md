# Database Persistence Layer

This directory contains the database persistence layer implementation for the microservices-demo project.

## Components

### 1. PostgreSQL Database (`postgres.yaml`)
- PostgreSQL 13 deployment
- Database: `orders`
- User: `orderuser` 
- Password: `orderpass`
- Service exposed on port 5432

### 2. Database Schema (`db-init.yaml`)
- Kubernetes Job to initialize database schema
- Creates `orders` table with the following structure:
  - `id` - Primary key (SERIAL)
  - `user_id` - User identifier (VARCHAR)
  - `user_currency` - Currency code (VARCHAR)
  - `items` - Order items (JSONB)
  - `total_amount` - Order total (DECIMAL)
  - `shipping_address` - Address details (JSONB)
  - `created_at` - Timestamp (TIMESTAMP)

## Deployment

1. Deploy PostgreSQL:
```bash
kubectl apply -f persistence-layer/postgres.yaml -n hipster
```

2. Initialize database schema:
```bash
kubectl apply -f persistence-layer/db-init.yaml -n hipster
```

3. Verify deployment:
```bash
kubectl get pods -n hipster | grep postgres
kubectl exec -n hipster <postgres-pod> -- psql -U orderuser -d orders -c "\dt"
```

## Integration

The checkoutservice has been configured with database environment variables:
- `DB_HOST=postgres`
- `DB_PORT=5432`
- `DB_NAME=orders`
- `DB_USER=orderuser`
- `DB_PASSWORD=orderpass`

## Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Frontend      │───▶│  Checkoutservice │───▶│   PostgreSQL    │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                              │                         │
                              ▼                         ▼
                       ┌──────────────┐         ┌─────────────┐
                       │ Order Processing │         │ Orders Table │
                       └──────────────┘         └─────────────┘
```

This implementation provides the infrastructure foundation for order persistence in the microservices architecture.