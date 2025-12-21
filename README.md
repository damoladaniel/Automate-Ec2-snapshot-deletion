The aim was to explore with AWS Serverless Compute Service.

The task was to automate EC2 snapshot deletion.
As a cloud engineer you will agree with me that cleaning up after your work saves you some money and old snapshots can sit in your account racking up expenses.
Snapshots are orphaned parts of your EC instances that are attached to your volumes and suprisingly, even if you terminate your EC2 instances, these snapshots do not accompany them to delete.

This Lambda function automates the deletion of snapshots attached to your EC2 instances after deletion.

How?

1. We tell AWS to run a code whenever an EC2 instance is terminated or deleted.

2. The code searches for any snapshots that were created from the specific instance

3. The code deletes the associated snapshot.


**Step 1: Create the Lambda Function**
Open the AWS Lambda Console.

Click **Create function**.

Choose **Author from scratch**.

Function name: DeleteSnapshotsAfterTermination.

Runtime: Select Python 3.12 (or the latest version).

Click **Create function.**

**Step 2: The Code**
Replace the default code in the editor with this script:


```
import boto3
def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    
    # 1. Get the Instance ID from the Event (the instance that just died)
    instance_id = event['detail']['instance-id']
    print(f"Cleaning up snapshots for terminated instance: {instance_id}")

    # 2. Find all snapshots that belong to this Instance ID
    # Note: We filter by the 'Description' because AWS includes the ID there
    snapshots = ec2.describe_snapshots(
        Filters=[
            {'Name': 'description', 'Values': [f"*{instance_id}*"]}
        ]
    )['Snapshots']

    # 3. Loop through and delete them
    for snap in snapshots:
        snap_id = snap['SnapshotId']
        try:
            print(f"Deleting snapshot: {snap_id}")
            ec2.delete_snapshot(SnapshotId=snap_id)
        except Exception as e:
            print(f"Could not delete {snap_id}: {e}")

    return {"status": "success"}
```
<img width="2038" height="761" alt="Code source" src="https://github.com/user-attachments/assets/a09112a1-db70-43ca-beb7-a7b17456230e" />
