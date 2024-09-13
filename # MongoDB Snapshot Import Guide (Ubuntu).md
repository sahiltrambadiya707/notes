# MongoDB Snapshot Import Guide (Ubuntu)

This guide explains how to import a MongoDB snapshot (.wt files) into a local MongoDB instance on Ubuntu. Two methods are provided: the first replaces the current database, while the second allows switching between databases by modifying the `dbpath` in the MongoDB configuration.

---

## Default MongoDB Paths on Ubuntu:
- **Data Directory (dbpath):** `/var/lib/mongodb`
- **Configuration File:** `/etc/mongod.conf`
- **Log Files:** `/var/log/mongodb/mongod.log`
- **MongoDB Executable:** `/usr/bin/mongod`

> **Note:** These methods allow one database snapshot at a time. You cannot use both the existing database and the new snapshot simultaneously. 

---

## Method 1: Replace Existing Database

### Steps:

1. **Stop MongoDB Server:**
   - Command:
     ```bash
     sudo systemctl stop mongod
     sudo systemctl status mongod  # Ensure the status is 'inactive'
     ```

2. **Backup Existing Data:**
   - Command:
     ```bash
     sudo cp -r /var/lib/mongodb /var/lib/mongodb-backup
     ```

3. **Clear MongoDB Data Directory:**
   - Command:
     ```bash
     sudo rm -rf /var/lib/mongodb/*
     ```

4. **Copy Snapshot Files to Data Directory:**
   - Command:
     ```bash
     sudo cp -r ~/Download/extracted-restore-folder/* /var/lib/mongodb/
     ```

5. **Set Proper Permissions:**
   - Command:
     ```bash
     sudo chown -R mongodb:mongodb /var/lib/mongodb
     ```

6. **Start MongoDB Server:**
   - Command:
     ```bash
     sudo systemctl start mongod
     ```

---

## Method 2: Switch Between Databases by Modifying `dbpath`

### Steps:

1. **Stop MongoDB Server:**
   - Command:
     ```bash
     sudo systemctl stop mongod
     sudo systemctl status mongod  # Ensure the status is 'inactive'
     ```

2. **Create New Directory for Snapshot Data:**
   - Command:
     ```bash
     sudo mkdir /var/lib/mongodb-snap-data
     ```

3. **Copy Snapshot Files to New Directory:**
   - Command:
     ```bash
     sudo cp -r ~/Download/extracted-restore-folder/* /var/lib/mongodb-snap-data/
     ```

4. **Set Proper Permissions:**
   - Command:
     ```bash
     sudo chown -R mongodb:mongodb /var/lib/mongodb-snap-data
     ```

5. **Modify `dbpath` in MongoDB Configuration:**
   - Open the MongoDB config file:
     ```bash
     sudo vim /etc/mongod.conf
     ```
   - In the file, find the `storage:` section and replace the `dbPath`:
     ```yaml
     storage:
         dbPath: /var/lib/mongodb-snap-data  # New snapshot path
     ```
   - **Note:** Keep the original path `/var/lib/mongodb` in mind to switch back later.

6. **Start MongoDB Server:**
   - Command:
     ```bash
     sudo systemctl start mongod
     ```

---

These methods allow you to restore and switch between MongoDB databases using snapshots. If you need to revert to the original database, simply reassign the `dbpath` to `/var/lib/mongodb` and restart the MongoDB service.