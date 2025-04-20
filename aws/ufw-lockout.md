# 🔐 EC2 Instance Rescue Guide (Locked Out via UFW or SSH Block)

If you've accidentally locked yourself out of an EC2 instance (e.g., by enabling `ufw` without allowing SSH), follow this guide to recover access by attaching the root volume to a rescue instance.

---

## 🧰 Prerequisites

- Your **locked EC2 instance** (the one you can't SSH into)
- A **rescue EC2 instance** in the **same Availability Zone**
- SSH access to the rescue instance
- EC2 permissions to manage volumes

---

## 🛠️ Step-by-Step Recovery

### 1. Stop the Locked EC2 Instance

In the AWS Console:

- Navigate to **EC2 → Instances**
- Select the locked instance → Click **Actions → Instance State → Stop**

---

### 2. Detach Its Root Volume

- Go to **EC2 → Volumes**
- Find the volume attached to the locked instance
- Click it → **Actions → Detach Volume**
- Wait until its state is `available`

---

### 3. Attach the Volume to the Rescue Instance

- Click the detached volume → **Actions → Attach Volume**
- Choose your **rescue instance**
- Set device name to `/dev/xvdf` (or similar)

---

### 4. SSH into the Rescue Instance

```bash
ssh -i /path/to/key.pem ubuntu@<rescue-instance-public-ip>
```

---

### 5. Mount the Volume

```bash
# Create mount point
sudo mkdir /mnt/rescue

# Check attached devices
lsblk

# Mount the correct partition (usually xvdf1)
sudo mount /dev/xvdf1 /mnt/rescue
```

---

### 6. Disable UFW or Allow SSH

```bash
# Enter the mounted system
sudo chroot /mnt/rescue

# Option A: Disable UFW
ufw disable

# Option B: Allow SSH
ufw allow OpenSSH

# Exit the chroot environment
exit
```

---

### 7. Unmount and Detach the Volume

```bash
sudo umount /mnt/rescue
```

In AWS Console:

- Go to **Volumes → Actions → Detach** from rescue instance

---

### 8. Reattach Volume to Original Instance

- Select the volume → **Actions → Attach Volume**
- Choose your **original (locked) instance**
- Set the device name back to `/dev/xvda` (or the original root device name)

---

### 9. Start the Original Instance

- Go to **EC2 → Instances**
- Select the original instance → Click **Actions → Start**

---

### 10. SSH Back In

```bash
ssh -i /path/to/key.pem ubuntu@<original-instance-public-ip>
```

---

## ✅ Tips to Avoid This in the Future

Before enabling `ufw` on any remote machine:

```bash
sudo ufw allow OpenSSH
sudo ufw enable
```

This ensures you won't lock yourself out.

---

## 🧹 Optional: Filesystem Check (If You Forgot to Unmount)

If you detached the volume without unmounting:

1. Reattach it to the rescue instance
2. Run:

```bash
sudo fsck /dev/xvdf1
```

3. Let it fix any issues before reattaching it to the original instance.

---
