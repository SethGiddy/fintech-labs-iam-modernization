# Part 1 — Identity Inventory & Taxonomy

## Objective

Identify and classify the identities used within FinTech Labs and
determine the primary security risk associated with the compromise
of each identity.

## IAM Identity Inventory

| Entity | IAM Identity Type | Primary Security Risk |
|---|---|---|
| Sarah | | |
| Payment-Gateway-API-Key | | |
| Alex | | |
| Lambda-Log-Processor | | |

## Identity Classification Analysis

### Sarah 
Sarah is a human employee of FinTech Labs and therefore represents a Workforce Identity. Her identity would normally be managed through the organisation's workforce identity provider and access-control system.

### Payment-Gateway-API-Key
The Payment-Gateway-API-Key is not associated with a human employee.It is an application credential used by a server to authenticate with an external payment service. It therefore represents a Non-Human / Workload Identity.

### Alex
Alex is a human employee working for FinTech Labs as a customer
service representative. Although Alex interacts with customers,
Alex is not a customer identity. Alex therefore belongs to the
Workforce Identity category.

### Lambda-Log-Processor
The Lambda-Log-Processor is an AWS serverless workload rather than
a human user. It therefore represents a Non-Human / Workload Identity.
Its AWS permissions should be granted through an IAM role and limited
to the resources required to process audit logs.