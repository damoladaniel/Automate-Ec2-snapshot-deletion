The aim was to explore with AWS Serverless Compute Service.

The task was to automate EC2 snapshot deletion.
As a cloud engineer you will agree with me that cleaning up after your work saves you some money and old snapshots can sit in your account racking up expenses.
Snapshots are orphaned parts of your EC instances that are attached to your volumes and suprisingly, even if you terminate your EC2 instances, these snapshots do not accompany them to delete.

This Lambda function automates the deletion of snapshots attached to your EC2 instances after deletion.

How?

1. We tell AWS to run a code whenever an EC2 instance is terminated or deleted.

2. The code searches for any snapshots that were created from the specific instance

3. The code deletes the associated snapshot.
