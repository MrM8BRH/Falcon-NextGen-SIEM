```YAML
sources:
  activeDirectoryEvents:
    type: wineventlog
    sink: AD
    channels:
      # Collect key AD-related events from the Security log
      - name: Security
        onlyEventIDs: 
          - 4624   # Successful logon
          - 4625   # Failed logon
          - 4634   # Logoff
          - 4647   # User initiated logoff
          - 4648   # Logon attempt using explicit credentials (network logon)
          - 4672   # Special privileges assigned to new logon (e.g., Administrator)
          - 4688   # A new process has been created
          - 4689   # A process has exited
          - 4720   # User account created
          - 4722   # User account enabled
          - 4723   # An attempt was made to change an account's password
          - 4724   # An attempt was made to reset an account's password
          - 4725   # User account disabled
          - 4726   # User account deleted
          - 4727   # Security-enabled global group created
          - 4728   # Member added to a security-enabled global group
          - 4729   # Member removed from a security-enabled global group
          - 4735   # Security-enabled local group changed
          - 4737   # Security-enabled global group changed
          - 4738   # User account changed
          - 4740   # Account locked out
          - 4761   # A trust relationship was created or changed
          - 4762   # A trust relationship was removed
          - 4767   # User account unlocked
          - 4776   # Credential validation (used by domain controller)
          - 4780   # Account name enumerated
          - 7045   # A new service was installed on the system
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
    token: $INGEST_TOKEN  # Replace with your actual token
    url: $LOGSCALE_URL    # Replace with your ingest URL (must start with https://)
```
