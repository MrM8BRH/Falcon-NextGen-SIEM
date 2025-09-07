```yaml
sources: 
  activeDirectoryEvents: 
    type: wineventlog 
    sink: AD 
    channels: 
      # Collect key AD-related events from the Security log 
      - name: Security 
      - name: DFS Replication 
      - name: Directory Service 
        onlyEventIDs:  
          - 4624   # Successful logon 
          - 4624   # Successful logon
          - 4625   # Failed logon 
          - 4634   # Logoff 
          - 4647   # User initiated logoff
          - 4648   # Logon attempt using explicit credentials (network logon)
          - 4672   # Special privileges assigned to new logon 
          - 4688   # A new process has been created
          - 4689   # A process has exited
          - 4720   # User account created 
          - 4722   # User account enabled 
          - 4723   # Password change attempt 
          - 4724   # Password reset 
          - 4725   # User account disabled 
          - 4726   # User account deleted 
          - 4727   # Security-enabled global group created
          - 4728   # Member added to a security-enabled global group
          - 4729   # Member removed from a security-enabled global group
          - 4735   # Security-enabled local group changed
          - 4737   # Security-enabled global group changed
          - 4738   # User account changed
          - 4739   # Domain policy changed 
          - 4740   # Account locked out 
          - 4761   # A trust relationship was created or changed
          - 4762   # A trust relationship was removed
          - 4767   # A user account was unlocked 
          - 4776   # Credential validation (used by domain controller)
          - 4780   # Account name enumerated
          - 5136   # GPO Modified 
          - 5137   # GPO Created 
          - 5141   # GPO Deleted 
          - 4016   # GPO Applied on a client 
          - 7045   # A new service was installed on the system
          # Replication / DC health
          - 1084   # Replication failed – schema mismatch 
          - 1265   # Replication failed with a specific error code (see message) 
          - 1311   # Replication configuration error – insufficient site links or topology issues 
          - 1388   # Replication of an object failed due to access or schema problems 
          - 1566   # Unable to contact a replication partner 
          - 1798   # KCC cannot create a connection object 
          - 1864   # DC has not received replication data from a partner for too long 
          - 1925   # Domain controller not found when trying to replicate 
          - 1988   # Lingering object detected on a domain controller 
          - 1989   # Lingering object successfully removed 
          - 2042   # USN rollback detected – replication has been halted 
          - 2087   # DNS lookup failure for a domain controller 
          - 2092   # This DC cannot replicate until it has successfully synchronized with a partner 
          - 2103   # NTDS database recovery complete after an unexpected shutdown 
    parser: microsoft-winevent 
    transforms: 
      - type: static_fields 
        fields: 
          source: "active_directory" 
          environment: "production" 
    language: 1033 
    format: renderFieldsOnly 
 
sinks: 
  AD: 
    type: hec 
    token: **** 
    url: **** 
```
