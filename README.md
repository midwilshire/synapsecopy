# ADLS Curated to Blob Archive - Move Pipeline

## Architecture Overview
- **Source:** ADLS Gen2 (Container: `curated`) in VNet A.
- **Destination:** Azure Blob Storage in VNet B.
- **Connectivity:** Synapse Managed VNet using Managed Private Endpoints.

  <img width="1260" height="787" alt="image" src="https://github.com/user-attachments/assets/81f26d79-2d37-46ef-bcd0-317899bdbc8d" />


## Pre-Deployment Requirements (Manual Steps)
Before importing the ARM templates or syncing this repo, the following must be set up:

1. **Managed Private Endpoints:**
   - Create a Private Endpoint for Source ADLS (sub-resource: `dfs`).
   - Create a Private Endpoint for Destination Blob (sub-resource: `blob`).
   - **Action:** Approve both in the Azure Portal > Storage Account > Networking.

2. **IAM Permissions:**
   - Assign `Storage Blob Data Contributor` to the Synapse Managed Identity on BOTH storage accounts.
   - Assign `EventGrid EventSubscription Contributor` to the Synapse Identity on the Source account.

3. **Resource Providers:**
   - Ensure `Microsoft.EventGrid` is registered in the Subscription.

## Pipeline Logic
- **Trigger:** Storage Event (BlobCreated) on `*.pdf`.
- **Process:** Copy Data (Binary) -> Success Dependency -> Delete Activity (Source).
- **Audit Logs:** Written to the Destination storage account under `/audit-logs/`.
