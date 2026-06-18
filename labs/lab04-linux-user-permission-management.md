# Lab 04 - Linux User and Permission Management

## 1. Overview

This lab focuses on Linux user, group, ownership, and permission management.

The goal was to create a controlled access environment on an Ubuntu VM using Linux users, groups, directory ownership, and file permissions. This lab demonstrates how access control can be enforced using the principle of least privilege.

---

## 2. Lab Objective

The objectives of this lab were to:

- Create Linux groups for different teams
- Create test users
- Assign users to specific groups
- Create protected directories under `/srv`
- Configure directory ownership using `chown`
- Configure permissions using `chmod`
- Test allowed and denied access
- Validate group-based access control
- Document the results as part of a cybersecurity homelab portfolio

---

## 3. Lab Environment

| Component | Description |
|---|---|
| Host Machine | MacBook Intel |
| Virtualization Platform | Oracle VirtualBox |
| Target Machine | Ubuntu VM |
| Main Focus | Linux access control |
| Users Created | `analyst1`, `analyst2`, `developer1` |
| Groups Created | `security-team`, `developers` |
| Protected Directories | `/srv/security-reports`, `/srv/dev-files` |

---

## 4. Lab Scenario

The lab simulates a basic Linux server with different access requirements.

Users:

| User | Role |
|---|---|
| `analyst1` | Security analyst |
| `analyst2` | Security analyst |
| `developer1` | Developer |

Groups:

| Group | Purpose |
|---|---|
| `security-team` | Access to security reports |
| `developers` | Access to development files |

Directories:

| Directory | Authorized Group |
|---|---|
| `/srv/security-reports` | `security-team` |
| `/srv/dev-files` | `developers` |

---

## 5. Creating Groups

The following groups were created:

```bash
sudo groupadd security-team
sudo groupadd developers

```

---

## 6. Creating Users

The following users were created:
```
sudo adduser analyst1
sudo adduser analyst2
sudo adduser developer1
```
To verify the users:
```
id analyst1
id analyst2
id developer1
```

---

## 7. Assigning Users to Groups

Security analysts were added to the security-team group:
```
sudo usermod -aG security-team analyst1
sudo usermod -aG security-team analyst2
```
The developer user was added to the developers group:
```
sudo usermod -aG developers developer1
```
To verify group membership:
```
groups analyst1
groups analyst2
groups developer1
```
Expected result:
```
analyst1 : analyst1 security-team
analyst2 : analyst2 security-team
developer1 : developer1 developers
```

---

## 8. Creating Protected Directories
The lab directories were created under /srv:
```
sudo mkdir -p /srv/security-reports
sudo mkdir -p /srv/dev-files
```

---

## 9. Configuring Ownership

The ownership of each directory was configured using chown.
```
sudo chown root:security-team /srv/security-reports
sudo chown root:developers /srv/dev-files
```
To verify ownership:
```
ls -ld /srv/security-reports /srv/dev-files
```
Result:
```
drwxrwx--- 2 root developers     4096 Jun 17 00:32 /srv/dev-files
drwxrwx--- 2 root security-team  4096 Jun 17 00:32 /srv/security-reports
```

---

## 10. Configuring Permissions

Permissions were configured with chmod 770:
```
sudo chmod 770 /srv/security-reports
sudo chmod 770 /srv/dev-files
```
Permission meaning:
```
rwx rwx ---
│   │   │
│   │   └── Others: no access
│   └────── Group: read, write, execute
└────────── Owner: read, write, execute
```
This means:
- root has full access
- the authorized group has full access
- all other users have no access

---

## 11. Creating Test Files

Test files were created inside each directory:
```
echo "Security report test file" | sudo tee /srv/security-reports/report1.txt
echo "Developer file test" | sudo tee /srv/dev-files/dev1.txt
```
Ownership was configured:
```
sudo chown root:security-team /srv/security-reports/report1.txt
sudo chown root:developers /srv/dev-files/dev1.txt
```
File permissions were configured:
```
sudo chmod 660 /srv/security-reports/report1.txt
sudo chmod 660 /srv/dev-files/dev1.txt
```

---

## 12. Access Testing
### Test 1 - Regular User Access

The regular user mendenson was not a member of either security-team or developers.

Commands tested:
```
ls -l /srv/security-reports
ls -l /srv/dev-files
```
Result:
```
Permission denied
```
This confirmed that users outside the authorized groups cannot access the protected directories.

### Test 2 - Security Analyst Access

The user analyst1 should access /srv/security-reports but should not access /srv/dev-files.

Commands:
```
su - analyst1
ls /srv/security-reports
cat /srv/security-reports/report1.txt
ls /srv/dev-files
exit
```
Expected result:
```
/srv/security-reports -> Access allowed
/srv/dev-files        -> Permission denied
```

### Test 3 - Developer Access

The user developer1 should access /srv/dev-files but should not access /srv/security-reports.

Commands:
```
su - developer1
ls /srv/dev-files
cat /srv/dev-files/dev1.txt
ls /srv/security-reports
exit
```
Expected result:
```
/srv/dev-files          -> Access allowed
/srv/security-reports   -> Permission denied
```

---

## 13. Results
| User | /srv/security-reports | /srv/dev-files |
|---|---|---|
| analyst1 | Allowed | Denied |
| analyst2 | Allowed | Denied |
| developer1 | Denied | Allowed |
| mendenson | Denied | Denied |

---

## 14. Security Analysis

This lab demonstrates group-based access control in Linux.

The protected directories were configured with 770 permissions, which means only the owner and the assigned group can access them. Other users have no permissions.

This supports the principle of least privilege because users only receive access to the directories required for their role.

The configuration prevents unauthorized users from reading or modifying files outside their assigned group.

---

## 15. Key Commands Used
```
# Create groups
sudo groupadd security-team
sudo groupadd developers

# Create users
sudo adduser analyst1
sudo adduser analyst2
sudo adduser developer1

# Add users to groups
sudo usermod -aG security-team analyst1
sudo usermod -aG security-team analyst2
sudo usermod -aG developers developer1

# Verify groups
groups analyst1
groups analyst2
groups developer1

# Create directories
sudo mkdir -p /srv/security-reports
sudo mkdir -p /srv/dev-files

# Set ownership
sudo chown root:security-team /srv/security-reports
sudo chown root:developers /srv/dev-files

# Set permissions
sudo chmod 770 /srv/security-reports
sudo chmod 770 /srv/dev-files

# Verify permissions
ls -ld /srv/security-reports /srv/dev-files
```

---

## 16. Evidence Collected
```
screenshots/lab04-directory-permissions.png
screenshots/lab04-regular-user-denied.png
screenshots/lab04-analyst-access-test.png
screenshots/lab04-developer-access-test.png
```

---

## 17. What I Learned

In this lab, I learned how to:

- Create Linux users and groups
- Assign users to groups
- Configure directory ownership with chown
- Configure permissions with chmod
- Interpret Linux permissions such as rwxrwx---
- Restrict access to sensitive directories
- Test access as different users
- Apply the principle of least privilege
- Document Linux access control in a professional way

---

## 18. Conclusion

This lab successfully demonstrated Linux user and permission management.

By creating users, groups, protected directories, and permission rules, I was able to control access based on user roles.

The final configuration allowed members of the security-team group to access security reports and members of the developers group to access development files. Users outside these groups were denied access.

This lab helped reinforce important Linux security concepts related to ownership, permissions, group-based access control, and least privilege.