# 📦 Logical Volume Management (LVM) 

<img src="https://github.com/bhuvan-raj/Logical-Volume-Management/blob/main/lvm.png" alt="Banner" />

## 🧠 What is LVM?

LVM (Logical Volume Management) is a flexible storage management solution for Linux systems. Instead of working directly with physical disks and partitions, LVM provides an abstraction layer that lets you create logical volumes from one or more physical volumes.

### ✅ Key Concepts:

* **PV (Physical Volume):** Physical storage device (e.g., partition, disk, etc.)
* **VG (Volume Group):** Group of one or more PVs
* **LV (Logical Volume):** Flexible, resizable partition carved from VG

### 🧾 Why Use LVM?

* Dynamically resize partitions (LVs) without downtime
* Combine multiple disks into a single VG (volume pool)
* Take snapshots of volumes
* Easier storage management

### 🚀 Use Cases

* Servers that need dynamic storage scaling
* Databases with fluctuating storage requirements
* Systems requiring backup snapshots
* Combining EBS volumes in EC2 for redundancy or capacity

---

## 🧰 Common LVM Commands

```bash
# List all available block devices
lsblk

# Partition disk into two
sudo fdisk /dev/xvdf
# (Inside fdisk, create two primary partitions)

# Create physical volumes
pvcreate /dev/xvdf1 /dev/xvdf2

# Create volume group from both partitions
vgcreate myvg /dev/xvdf1 /dev/xvdf2

# Create logical volume using all space
lvcreate -l 100%FREE -n mylv myvg

# Create filesystem
mkfs.ext4 /dev/myvg/mylv

# Mount logical volume
mkdir /mnt/mylvm
mount /dev/myvg/mylv /mnt/mylvm

# Show UUID for persistent mount
blkid /dev/myvg/mylv
```

---

## 🛠️ LVM Setup on Amazon Linux EC2 with a 1GB EBS Volume (2 Partitions)

### ✅ Step 1: Attach EBS Volume to EC2 Instance

1. Go to **AWS Console > EC2 > Volumes**
2. Create a new **1GB** volume (same AZ as EC2)
3. Attach to EC2 (e.g., as `/dev/xvdf`)

---

### ✅ Step 2: Log in to EC2 & Verify Volume

```bash
lsblk
```

You should see `/dev/xvdf`

---

### ✅ Step 3: Partition the Disk into Two

```bash
sudo fdisk /dev/xvdf
```

Inside fdisk:

* `n` (new partition)
* `p` (primary)
* Partition 1: Accept defaults or set half of 1GB (e.g., +500M)
* Repeat to create Partition 2
* `w` to write changes

Then:

```bash
lsblk
```

You’ll see `/dev/xvdf1` and `/dev/xvdf2`

---

### ✅ Step 4: Create Physical Volumes

```bash
sudo pvcreate /dev/xvdf1 /dev/xvdf2
```

### ✅ Step 5: Create Volume Group from Both

```bash
sudo vgcreate myvg /dev/xvdf1 /dev/xvdf2
```

### ✅ Step 6: Create Logical Volume Using All Space

```bash
sudo lvcreate -l 100%FREE -n mylv myvg
```

### ✅ Step 7: Create Filesystem

```bash
sudo mkfs.ext4 /dev/myvg/mylv
```

### ✅ Step 8: Mount the Logical Volume

```bash
sudo mkdir /mnt/mylvm
sudo mount /dev/myvg/mylv /mnt/mylvm
```

---

### ✅ Step 9: Make Mount Persistent

```bash
sudo blkid /dev/myvg/mylv
```

Copy the UUID, then:

```bash
sudo nano /etc/fstab
```

Add line:

```
UUID=<your-uuid> /mnt/mylvm ext4 defaults 0 0
```

---

## ✅ Verification

```bash
df -hT
lsblk
sudo vgdisplay
sudo lvdisplay
```

---

## 🔚 That's it!

You now have a working LVM setup using two partitions from a single 1GB EBS volume on Amazon Linux EC2.
