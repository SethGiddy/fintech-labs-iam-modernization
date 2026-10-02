# Part 2 — Least-Privilege Access Control

## Objective

Design an access-control matrix based on the Principle of Least
Privilege (PoLP) and Separation of Duties (SoD).

## Access Matrix

                  DEV CODE     PROD DB      IAM
                  ─────────     ───────      ───
Sarah             R/W           NONE         NONE
Bob               NONE          R/W          NONE
Dave              READ          READ         READ

## Software Engineer — Sarah

Sarah requires Read/Write access to the development source-code
repository because her role involves developing backend payment APIs.

Sarah should not have access to modify the production database because
production database administration is separated from software development.

Sarah does not require access to the IAM administration console because
identity administration is outside the stated responsibilities of the software engineer role.

Software Engineer (Sarah)
        │
        ├── Res-Dev-Code → Read/Write
        ├── Res-Prod-Database → None
        └── Res-IAM-Console → None


## Database Administrator — Bob

Bob requires Read/Write access to the production database because
database administration is his primary responsibility.

Bob does not require access to modify the development source-code
repository because application development is assigned to the software
engineering function. This supports Separation of Duties.

Bob does not require access to the IAM administration console because
identity administration is outside the responsibilities defined for the database administrator role.

## DevOps Engineer — Dave

Dave requires Read access to the development source-code repository
to support deployment and operational activities without being given
unnecessary source-code modification privileges.

Dave has Read access to the production database for operational
visibility, but not Read/Write access. This prevents the DevOps role
from directly modifying production customer data.

Dave has Read access to the IAM administration environment for
visibility into identity and access configuration. Administrative
permission changes should be restricted to appropriately authorised
IAM administrators and controlled through more granular policies in a production environment.