# IAM Permission Test Results

## Sarah — sarah-dev

| Test | Resource | Expected Result | Actual Result | Status |
|---|---|---|---|---|
| List bucket contents | Development bucket | Allow | Allow | PASS |
| Read object | Development bucket | Allow | Allow | PASS |
| Upload object | Development bucket | Allow | Allow | PASS |
| Access production data | Production bucket | Deny | Access Denied | PASS |

## Bob — bob-dba

| Test | Resource | Expected Result | Actual Result | Status |
|---|---|---|---|---|
| List bucket contents | Production bucket | Allow | Allow | PASS |
| Read object | Production bucket | Allow | Allow | PASS |
| Upload object | Production bucket | Allow | Allow | PASS |
| Access development code | Development bucket | Deny | Access Denied | PASS |

![alt text](S3_Sarah_Test_AceesGranted.png) ![alt text](S3_Sarah_Test_NoAcess.png) ![alt text](sarah-dev_page.png) ![alt text](S3_Bob_Test_AceesGranted.png) ![alt text](S3_Bob_Test_NoAcess.png) ![alt text](S3_fintech-dev-code-aj.png) ![alt text](S3_fintech-prod-data-aj.png)