# Part 3 — Incident Investigation & Audit Analysis

## According to the JSON file downlaod I have analysed te the inident below.

## Incident

An unauthorized data read occurred against the production database
at approximately 02:00 on 24 September 2026.

## Evidence Source

AWS CloudTrail / Audit Log

## Observed Event

- Timestamp:
- Identity Type:
- Principal:
- Source IP:
- Action:
- Status:

## AAA Analysis

### 1. Identification & Authentication

The CloudTrail event identifies the principal as the AWS IAM role `DevOps-Deployment-Role`. The event does not identify a human user as the direct principal. Therefore, the evidence establishes that an IAM role was used, but the supplied event alone does not establish whether the role was assumed by a human or by a workload.
Additional CloudTrail events relating to role assumption would be required to identify the originating principal.

### 2. Authorization
The CloudTrail event records a successful
`rds:DownloadDBClusterSnapshot` API operation performed using the `DevOps-Deployment-Role`.
The successful status indicates that the API operation was accepted and completed, but the supplied event does not identify the specific IAM policy statement that authorized the action.

The effective permissions of `DevOps-Deployment-Role` should therefore be reviewed to determine whether the role explicitly or indirectly had permission to perform `rds:DownloadDBClusterSnapshot`. This should also be compared against the organisation's least-privilege and Separation of Duties requirements.

### 3. Accounting / Forensics
The event occurred at 02:14:05 UTC on 24 September 2026, which is outside typical business hours for many development teams and is consistent with the reported time of the incident.
The source IP recorded in the event is 198.51.100.42. This address belongs to a documentation/example address range, so the supplied evidence cannot be used to establish a real geographic origin.
The main security concern is the combination of the DevOps deployment role, the successful `rds:DownloadDBClusterSnapshot` operation, and
the unusual time of access. The activity should be correlated with role-assumption events, CI/CD activity, IAM policies, network logs, and database audit records to determine whether the operation was legitimate or unauthorized.

## Security Assessment
The event should be treated as a security-relevant anomaly requiring further investigation. The recorded activity involves the DevOps-Deployment-Role performing a successful
rds:DownloadDBClusterSnapshot operation against production data at 02:14:05 UTC.
The combination of the sensitive database operation, the unusual timestamp, and the source IP warrants investigation against the organisation's expected deployment and operational activity.
The event alone does not establish that a specific human user
performed the action or that customer data was exfiltrated. Further evidence should be correlated before attributing responsibility.

## Evidence-Based Conclusion
The available CloudTrail evidence shows that the AWS
DevOps-Deployment-Role successfully performed
rds:DownloadDBClusterSnapshot at 02:14:05 UTC on 24 September 2026.

The event represents a potentially unauthorized or unexpected access to a sensitive production resource and should be investigated as a security incident.

The immediate investigation should determine who or what assumed the DevOps role, whether the role was legitimately authorized to perform the snapshot operation, whether the activity was associated with a scheduled deployment or operational task, and whether the snapshot was subsequently accessed or exported.

Relevant evidence should include IAM role policies, CloudTrail role assumption events, CI/CD pipeline records, network logs, database audit logs, and snapshot access or export records.