# FinTech Labs IAM Modernization

## Introduction

FinTech Labs Inc. is a fictional, fast-growing financial technology company moving from a traditional network-perimeter security model to an identity-centric Zero Trust approach. After a near-miss involving compromised developer credentials, the company must ensure that every person and workload receives only the access needed for its responsibilities, and that sensitive actions are traceable.

This project applies Week 1 identity and access management concepts to that scenario. It classifies identities, designs role-based access with least privilege and separation of duties, investigates a CloudTrail event, and recommends a Zero Trust transition. A companion AWS lab uses S3 buckets to simulate the development repository and production data store, then verifies that two test users cannot cross those access boundaries.

## Goals

- Classify workforce, customer, and non-human/workload identities and identify compromise risks.
- Define resource access using Role-Based Access Control (RBAC), the Principle of Least Privilege (PoLP), and Separation of Duties (SoD).
- Analyze an audit event using Identification, Authentication, Authorization, and Accounting (AAA).
- Explain why network location alone is not a trustworthy basis for access.
- Create and test basic AWS IAM policies attached to groups.

## Project Contents

| Part | Contents |
|---|---|
| [Part 1: Identity taxonomy](part-01-identity-taxonomy/identity-inventory.md) | FinTech Labs identity inventory and compromise-risk analysis. |
| [Part 2: Access control](part-02-access-control/least-privilege-access-matrix.md) | Role/resource access matrix and least-privilege rationale. |
| [Part 2: Permission test results](part-02-access-control/permission-test-results.md) | Recorded allow/deny tests and screenshots from the S3 lab. |
| [Part 3: Incident investigation](part-03-incident-investigation/forensic-analysis.md) | Evidence-based analysis of the supplied CloudTrail event. |
| [Part 3: Event log](part-03-incident-investigation/cloudtrail-event.json) | CloudTrail-style event supplied for the investigation. |
| [Part 4: Zero Trust summary](part-04-zero-trust/zero-trust-executive-summary.md) | Executive recommendation for the CEO. |

## Access Design

The proposed access matrix separates development, production data, and IAM administration. `None` means no access; `Read` means view-only; `Read/Write` means the role can view and modify the resource.

| Role | Development code | Production database | IAM console |
|---|---:|---:|---:|
| Software Engineer (Sarah) | Read/Write | None | None |
| Database Administrator (Bob) | None | Read/Write | None |
| DevOps Engineer (Dave) | Read | Read | Read |

The AWS walkthrough below implements only the Sarah and Bob development-versus-production boundary. It does not implement the full matrix, Dave's access, IAM-console permissions, or database permissions.

## AWS S3 Lab

### What You Will Build

Create two private S3 buckets, two customer-managed IAM policies, two IAM groups, and two test IAM users. Sarah's group receives access to the development bucket; Bob's group receives access to the production-data bucket. The test verifies that each user is denied access to the other bucket.

> **Scope note:** S3 is a teaching stand-in for the source-code repository and production data store. These policies grant S3 object permissions, not RDS/database permissions. Do not use real customer data or treat this exercise as a production-ready architecture.

### Prerequisites

- An AWS account where you are authorized to create S3 buckets, IAM policies, groups, and test users.
- A unique suffix for your bucket names. Replace `<your-initials>` below with your own lowercase initials or another unique suffix, for example `aj`.
- No additional policies attached to the test users or groups that would grant broader S3 access. Other identity policies, resource policies, or organization policies can affect the effective permissions.

### Step 1: Create the S3 Buckets

1. Sign in to the AWS Management Console and open **S3**.
2. Create a bucket named `fintech-dev-code-<your-initials>`.
3. Create a second bucket named `fintech-prod-data-<your-initials>`.
4. Keep **Block all public access** enabled. Use a unique suffix if either bucket name is already taken.
5. Add a harmless test file to each bucket if you want to verify object reads and uploads later. Do not upload sensitive information.

### Step 2: Create the Software Engineer Policy

In **IAM → Policies → Create policy**, choose the JSON editor and paste the policy below. Replace both occurrences of `<your-initials>` with the exact suffix used in your bucket names. Review the policy, then name it `FinTech-SoftwareEngineer-Policy` and create it.

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "AllowConsoleListing",
			"Effect": "Allow",
			"Action": [
				"s3:ListAllMyBuckets",
				"s3:GetBucketLocation"
			],
			"Resource": "*"
		},
		{
			"Sid": "AllowDevCodeAccessOnly",
			"Effect": "Allow",
			"Action": [
				"s3:ListBucket",
				"s3:GetObject",
				"s3:PutObject"
			],
			"Resource": [
				"arn:aws:s3:::fintech-dev-code-<your-initials>",
				"arn:aws:s3:::fintech-dev-code-<your-initials>/*"
			]
		}
	]
}
```

### Step 3: Create the Database Administrator Policy

Create another policy using the JSON below. Replace `<your-initials>` with the same suffix. Name the policy `FinTech-DBA-Policy` and create it.

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "AllowConsoleListing",
			"Effect": "Allow",
			"Action": [
				"s3:ListAllMyBuckets",
				"s3:GetBucketLocation"
			],
			"Resource": "*"
		},
		{
			"Sid": "AllowProdDataAccessOnly",
			"Effect": "Allow",
			"Action": [
				"s3:ListBucket",
				"s3:GetObject",
				"s3:PutObject"
			],
			"Resource": [
				"arn:aws:s3:::fintech-prod-data-<your-initials>",
				"arn:aws:s3:::fintech-prod-data-<your-initials>/*"
			]
		}
	]
}
```

These are allow policies scoped to the named bucket and its objects. AWS implicitly denies actions not allowed by an applicable policy, provided no other policy grants the test identity additional access. `s3:ListAllMyBuckets` allows the console to show bucket names; it does not grant permission to read or write objects in every bucket.

### Step 4: Create Groups and Test Users

1. In **IAM → User groups**, create `SoftwareEngineers` and attach `FinTech-SoftwareEngineer-Policy`.
2. Create `DatabaseAdmins` and attach `FinTech-DBA-Policy`.
3. In **IAM → Users**, create `sarah-dev` and add her to `SoftwareEngineers`.
4. Create `bob-dba` and add him to `DatabaseAdmins`.
5. For this isolated lab, enable console access using secure, unique credentials. Require a password change at first sign-in and enable MFA where available. Never commit credentials or access keys to this repository.

### Step 5: Verify the Access Boundaries

Sign in as each test user separately and open S3. Confirm the following results:

| Test identity | Allowed | Denied |
|---|---|---|
| `sarah-dev` | List buckets; list, read, and upload objects in `fintech-dev-code-<your-initials>` | List or access objects in `fintech-prod-data-<your-initials>` |
| `bob-dba` | List buckets; list, read, and upload objects in `fintech-prod-data-<your-initials>` | List or access objects in `fintech-dev-code-<your-initials>` |

Record the expected result, actual result, and status in [permission-test-results.md](part-02-access-control/permission-test-results.md). An **Access Denied** result for the out-of-scope bucket is expected. If a user can access both buckets, check for other attached policies, group membership, bucket policies, and permissions granted through other AWS mechanisms.

Screenshots from the recorded tests:

- Sarah: [allowed access](S3_Sarah_Test_AceesGranted.png), [denied access](S3_Sarah_Test_NoAcess.png), [user page](sarah-dev_page.png)
- Bob: [allowed access](S3_Bob_Test_AceesGranted.png), [denied access](S3_Bob_Test_NoAcess.png)
- Buckets: [development code](S3_fintech-dev-code-aj.png), [production data](S3_fintech-prod-data-aj.png)

## Submission Checklist

- [x] Part 1: Identity taxonomy and risk analysis documented.
- [x] Part 2: Least-privilege access matrix and separation-of-duties rationale documented.
- [x] Part 2: S3 allow/deny test results and evidence documented.
- [x] Part 3: CloudTrail event analyzed using AAA principles.
- [x] Part 4: Zero Trust executive summary prepared.
- [ ] Optional: Repeat the AWS walkthrough in your own account and record your test evidence.

## Security Notes

- Use test identities only for this exercise. For real workforce access, prefer federated access through AWS IAM Identity Center or an organizational identity provider, temporary credentials, MFA, and managed role assignment over long-lived IAM users.
- Keep production data and development access separate. Review policies regularly and remove permissions that are no longer required.
- The supplied event's successful status confirms the API operation succeeded; it does not, by itself, prove which human or workload initiated it or which policy statement authorized it. Correlate CloudTrail role-assumption events and other records before drawing attribution conclusions.
- The sample source IP `198.51.100.42` is in a documentation-only address range. It is not a usable indicator of a real actor or geographic origin.

## Key Concepts

Authentication, authorization, accounting, workforce identity, workload identity, RBAC, least privilege, separation of duties, CloudTrail auditing, incident investigation, and Zero Trust.