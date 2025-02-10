
# 1 The error message **"No space left on device"** typically occurs when the disk of your EC2 instance runs out of available space.
  - When the disk space is completely used up, the system cannot perform operations like writing data to the disk, which results in this error.
  - In Amazon EC2 instances, the disk is typically provided by Elastic Block Store (EBS) volumes, and if the storage space on your EBS volume is exhausted, you'll face this issue.
  -  Increasing the size of your volume and extending the file system can resolve this issue.
### Steps to Resolve the "No Space Left on Device" Issue

#### Step 1: Check Disk Usage
To find out how much space is being used on your instance's file system, you can use the **`df`** command. The **`df`** command shows the amount of disk space used and available on each mounted file system.

```bash
df -h
```

- **`-h`**: This option makes the sizes human-readable (e.g., in GB, MB).
- The output will show the used space, available space, and the file system where the space is being consumed. Look for the partition with **100% usage**; this is where the disk space is full.

#### Step 2: Resize the EBS Volume on AWS
To increase the storage capacity of your EC2 instance, you need to modify the EBS volume attached to it.

1. **Go to the AWS Management Console**:
   - Navigate to the EC2 dashboard.
   - In the left-hand menu, find and click on **Volumes** under **Elastic Block Store (EBS)**.

2. **Modify the EBS Volume**:
   - Select the EBS volume that is attached to your EC2 instance.
   - Click the **Modify Volume** button.
   - Increase the size of the volume (e.g., add 10GB, 20GB, or more as per your requirement).
   - Click **Modify** and then confirm your action.

#### Step 3: Verify Volume Size Changes

After modifying the EBS volume size in the AWS Console, you need to make sure that the instance's file system is aware of the increased size. 

You can use the **`lsblk`** command to check the block devices attached to the instance, including your volume.

```bash
lsblk
```

This will display a list of block devices and partitions on the EC2 instance.

#### Step 4: Install Cloud Guest Utilities (if needed)

For resizing the file system, **cloud guest utils** are required. This utility helps in resizing the file system after expanding the disk size. You can install it using the following command:

```bash
sudo apt install cloud-guest-utils
```

#### Step 5: Resize the Partition
Once the cloud guest utilities are installed, use the **`growpart`** tool to resize the partition and make use of the newly allocated space. Replace `/dev/xvda1` with the name of your partition.

```bash
sudo growpart /dev/xvda1
```

- **`growpart`**: This command resizes a partition on the specified disk (`/dev/xvda1` in this case) to use the newly allocated space from the EBS volume.

#### Step 6: Resize the File System

Now, you need to resize the file system so it can recognize the newly available space. If you are using ext4 file system, use the **`resize2fs`** command:

```bash
sudo resize2fs /dev/xvda1
```

- **`resize2fs`**: This command resizes the file system on a specified partition (`/dev/xvda1` in this case) to match the size of the partition.

#### Step 7: Verify the Disk Usage

Finally, run **`df -h`** again to confirm that the file system has been successfully resized and that the extra space is available.

```bash
df -h
```
---


  

