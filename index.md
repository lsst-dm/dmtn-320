# Retention Strategy for eups packages.

```{abstract}
Define Eups package backup strategy in GCP. As we move away from AWS, we need to define our retention rules, implement cleanup automation and create snapshots
```

## Backup 

The data storage infrastructure consists of two primary buckets: eups-prod and eups-backup. The eups-prod bucket serves as our active storage with live data and features hierarchical namespace enablement for optimized input/output performance. To safeguard against data loss, this bucket implements a 30-day soft delete policy for data recovery.
As an additional security measure, we've implemented a daily backup process. Every day at 8:00 AM EST, a data transfer job automatically copies all content from eups-prod to eups-backup. The backup bucket offers enhanced protection with a 60-day soft delete policy and versioning capabilities, maintaining three distinct copies of data for 90 days.
To optimize costs, eups-backup utilizes archive storage instead of standard storage. While standard storage offers faster access at $0.020 per GB monthly (approximately $73.20 per month), archive storage provides adequate restoration speeds at a significantly lower cost of $0.0012 per GB monthly (approximately $4.40 per month).

## Migration

For migrating we will run rclone to sync the AWS s3 bucket with GCP bucket. This will make sure that the buckets are identical. After they are sync, we will run Jenkins to upload files to both AWS and GCP. After checking files integretrity and we can tell the file upload works correctly, we can stop pushing to AWS. Once we migrate to GCP full time, we can run GCP data transfer to back up production.

## Automation 

Automation will be handled by the daily Jenkins jobs. We will be using the same process that we are using to update AWS, but we will be pushing to GCP. This will allow us to have a consistent process that will not cause any interruptions while we transition.

## Cleanup 

Our new approach delegates cleanup operations to GCP, replacing the previous Jenkins-based cleanup system. This shift streamlines maintenance operations while reducing developer overhead. The [Backup](#Backup) section details our comprehensive data retention and cleanup procedures.

See the [Documenteer documentation](https://documenteer.lsst.io/technotes/index.html) for tips on how to write and configure your new technote.
