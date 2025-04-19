# interviewQuestions

# Multi-Tenant SaaS Platform Design

## What is Multi-Tenancy?
Multi-tenancy is a software architecture where a single instance of the application serves multiple customers (tenants). Each tenant’s data is isolated, but they share the same application and infrastructure.

---

## Key Design Goals

1. **Tenant Isolation**  
   Ensure data and access segregation between tenants. Can be logical (row-level) or physical (dedicated DB per tenant).

2. **Configurability**  
   Allow tenant-specific branding, feature toggles, and workflow customizations.

3. **Scalability**  
   Support onboarding of new tenants easily. Horizontal scalability of services and databases.

4. **Security**  
   Enforce strict access controls and encryption. Ensure secure tenant authentication and authorization (RBAC, OAuth, etc.).

---

## Approaches to Data Isolation

| Approach                     | Description                        | Pros                          | Cons                        |
|-----------------------------|------------------------------------|-------------------------------|-----------------------------|
| Shared DB, Shared Schema    | All tenants in same DB and tables  | Easy to manage & cost-efficient | Hard to scale and isolate   |
| Shared DB, Separate Schema  | Same DB, separate tables per tenant| Better isolation, easier backup | Schema migrations are complex |
| Dedicated DB per Tenant     | Each tenant has its own DB         | Best isolation & flexibility   | Operationally expensive     |

---

## Key Architecture Components

1. **Tenant Context Resolver**  
   Middleware that extracts tenant ID from request (e.g., subdomain, token, header).

2. **Authentication & Authorization**  
   Use OAuth 2.0 / JWT with tenant context. Implement RBAC per tenant.

3. **API Gateway**  
   Handle request routing, tenant context injection, rate limiting, and throttling.

4. **Config Service**  
   Fetch tenant-specific UI configs, feature flags, limits, etc.

5. **Data Layer**  
   Abstract database logic using a multi-tenant-aware ORM or data access layer.

6. **Monitoring & Analytics**  
   Metrics and logs should be tagged by tenant ID. Track tenant usage patterns, errors, and audit trails.

---

## Shared vs Dedicated Resources

| Resource   | Shared                         | Dedicated                         |
|------------|--------------------------------|-----------------------------------|
| Compute    | Most often shared              | Can be isolated for premium tenants |
| Storage    | Logical isolation              | Dedicated for compliance (HIPAA, etc.) |
| Cache      | Namespacing per tenant         | Separate Redis clusters            |
| Queues     | Namespaced topics              | Dedicated topics for high-volume tenants |
| Databases  | Often varies                   | Usually dedicated for large clients |

---

## Configuration Management

Use a config service or tenant config table to store:
- Feature toggles
- Branding (colors, logos)
- Limits (users, storage)
- SLA rules (e.g., retry policies)

---

## Multi-Tenancy in the Cloud

- Use **S3 bucket prefixes** per tenant  
- Use **IAM roles** per tenant for data access  
- Auto-scale containers by tenant demand

---

## Security Practices

- Encrypt sensitive data at rest and in transit  
- Apply tenant-aware access controls (tenant ID in access tokens)  
- Prevent cross-tenant data leaks via strict validation and tenancy filters

---

## Testing Considerations

- Test for tenant leakage  
- Ensure per-tenant feature restrictions work as expected  
- Load test with multiple tenants simulating real-world scenarios

---

## Tenant Onboarding Strategy

1. Signup → Collect metadata  
2. Provision DB/schema/resources  
3. Inject tenant config defaults  
4. Send welcome email / UI tour
