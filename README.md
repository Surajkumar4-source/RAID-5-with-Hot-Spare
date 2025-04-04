# Implementing RAID 5 with Hot Spare Disks Using mdadm

<br>

## Introduction
  *RAID (Redundant Array of Independent Disks) is a data storage virtualization technology that combines multiple physical drives into a single logical unit for improved performance, redundancy, or both. In this guide, we implement RAID 5 with hot spare disks using mdadm. This configuration provides redundancy with striped data and parity, ensuring data integrity even in the event of a single disk failure. Additionally, hot spare disks automatically replace failed drives, reducing downtime and manual intervention.*


## Benefits of RAID 5 with Hot Spare

- Redundancy: Protects against single-disk failure using parity.
- Cost-Effective Storage: Offers a balance of performance, storage capacity, and fault tolerance.
- Automatic Recovery: Hot spare disks replace failed drives automatically.
- Read Performance: Data striping across drives improves read speeds.
- Easy Maintenance: RAID recovery processes are automated for minimal disruption.

## Other RAID Levels Overview
- RAID 0 (Striping): High performance with no redundancy. Data is striped across drives, increasing speed but losing all data if any drive fails.
- RAID 1 (Mirroring): High redundancy by duplicating data on multiple drives. Read performance improves, but storage capacity is halved.
- RAID 10 (1+0): Combines mirroring and striping for high redundancy and performance but requires at least four drives.
- RAID 6: Similar to RAID 5 but uses two parity blocks, allowing up to two disk failures.
- RAID 5 (our implementation): Combines striping with parity, offering redundancy and good performance. A single drive can fail without data loss.


<br>



# Comparison with Other RAID Levels

| RAID Level | Description             | Pros                              | Cons                            |
|------------|-------------------------|-----------------------------------|---------------------------------|
| RAID 0     | Striping only           | High speed                       | No redundancy                  |
| RAID 1     | Mirroring               | High redundancy                  | Inefficient use of space       |
| RAID 5     | Striping with parity    | Balanced performance and redundancy | Can tolerate only one disk failure |
| RAID 6     | RAID 5 + extra parity   | Handles two failures             | More parity overhead           |
| RAID 10    | RAID 1 + RAID 0         | High performance and redundancy  | Expensive                      |







<br>
<br>

## Steps to Implement RAID 5 with Hot Spare Disks

### 1. Prerequisites
  - Active Disks: /dev/sdb, /dev/sdc, /dev/sdd
  - Hot Spare Disks: /dev/sde, /dev/sdf
  - RAID Device: /dev/md127
  - VM (Ubuntu/CentOS/)
  - Ensure mdadm is installed:

```yml
sudo apt-get update
sudo apt-get install mdadm
```

### 2. Create RAID 5 Array

```yml
sudo mdadm --create --verbose /dev/md127 --level=5 --raid-devices=3 /dev/sdb /dev/sdc /dev/sdd --spare-devices=2 /dev/sde /dev/sdf
```

- --create: Initializes a new RAID array.

- --level=5: Configures RAID 5 (striping with parity).

- --raid-devices=3: Three active disks.

- --spare-devices=2: Two hot spare disks.

### 3. Verify RAID Creation
```yml
sudo mdadm --detail /dev/md127

lsblk
```
  - Check RAID status and disk structure. Look for three active disks and two spare disks.

### 4. Format and Mount the RAID

```yml
sudo mkfs.ext4 /dev/md127

sudo mkdir /mnt/raid5

sudo mount /dev/md127 /mnt/raid5

df -h | grep /mnt/raid5

```
  - Format with ext4 and mount at /mnt/raid5.
  - Verify using df -h.

### 5. Simulate Disk Failure
```yml

sudo mdadm /dev/md127 --remove /dev/sdb

```

 - Simulates failure of /dev/sdb, marking it as failed.

 - Use sudo mdadm --detail /dev/md127 to verify RAID's degraded state.


### 6. Monitor Hot Spare Activation

```yml
watch cat /proc/mdstat

sudo mdadm --detail /dev/md127

```
  - Hot spare disks automatically replace failed drives.
  - Use watch to monitor the rebuild process in real time.

### 7. Re-Add Removed Disk (Optional)
```yml

sudo mdadm /dev/md127 --add /dev/sdb
```
  - Re-add the failed disk after rebuild completion to restore it as a hot spare.


### 8. Persist RAID Configuration
  - Save the RAID configuration to make it persistent across reboots:

```yml
sudo mdadm --detail --scan >> /etc/mdadm/mdadm.conf

sudo update-initramfs -u
```

### 9. Final Verification

```yml
sudo mdadm --detail /dev/md127

cat /mnt/raid5/testfile.txt

```
  - Confirm RAID is in a "clean" state.
  - Ensure the test file remains intact and accessible.



<br>
<br>




## Summary

<br>

1. Create RAID 5 Array:

    - Combine 3 active disks and 2 hot spare disks into a RAID 5 array.

2. Verify RAID Creation:

    - Check the RAID configuration, including active and spare disks.
    - Ensure the status is "clean."

3. Format the RAID Array:

    - Use a filesystem (e.g., ext4) for the RAID array.

4. Mount the RAID Array:

    -Create a mount point and mount the RAID array for usage.

5. Test the RAID Array:

    - Create a test file on the RAID to confirm data is accessible.

6. Simulate Disk Failure:

    - Remove one active disk to simulate a failure.
    - Verify the RAID enters a "degraded" state but remains operational.

7. Check Hot Spare Disk Activation:

    - Observe the hot spare disk automatically replacing the failed disk.

8. Monitor RAID Rebuild:

    - Track the rebuild process until the RAID returns to "clean" status.

9. Optional Re-addition:

    - Re-add the removed disk to the RAID array if required.

10. Final Verification:

    - Confirm all disks are active and the RAID is healthy.
    - Verify the test file to ensure data integrity.


<br>

*This implementation provides the steps for setting up and managing a RAID 5 array with hot spare disks, simulating failures, and handling recovery.*







<br>
<br>
<br>



## ------------------Screnshots--------------------
1.
<br>
<br>


![Alt text for image](screenshots/1.png)

2.
<br>
<br>


![Alt text for image](screenshots/2.png)


3.
<br>
<br>


![Alt text for image](screenshots/3.png)

<br>
<br>


4.
<br>
<br>


![Alt text for image](screenshots/4.png)

<br>
<br>

5.
<br>
<br>


![Alt text for image](screenshots/5.png)

<br>
<br>





<br>
<br>

6.
<br>
<br>



![Alt text for image](screenshots/6.png)

<br>
<br>




<br>
<br>

7.
<br>
<br>


![Alt text for image](screenshots/7.png)

<br>
<br>





<br>
<br>

8.
<br>
<br>


![Alt text for image](screenshots/8.png)

<br>
<br>





<br>
<br>

9.
<br>
<br>



![Alt text for image](screenshots/9.png)


<br>
<br>




10.
<br>
<br>


![Alt text for image](screenshots/10.png)

<br>
<br>



11.
<br>
<br>

![Alt text for image](screenshots/11.png)

<br>
<br>









<br>
<br>
<br>
<br>



**👨‍💻 𝓒𝓻𝓪𝓯𝓽𝓮𝓭 𝓫𝔂**: [Suraj Kumar Choudhary](https://github.com/Surajkumar4-source) | 📩 **𝓕𝓮𝓮𝓵 𝓯𝓻𝓮𝓮 𝓽𝓸 𝓓𝓜 𝓯𝓸𝓻 𝓪𝓷𝔂 𝓱𝓮𝓵𝓹**: [csuraj982@gmail.com](mailto:csuraj982@gmail.com)





<br>

