# Standard Operating Procedure
## Creating and Deploying Azure Disks Using ARM and Bicep Templates

| Field | Details |
|---|---|
| **Lab Title** | Deploy Azure Disks Using ARM and Bicep Templates |
| **Platform** | Microsoft Azure — Portal, PowerShell, Bash (Azure CLI) |
| **Templates Used** | ARM (JSON) · Bicep |
| **Disks Deployed** | 5 total — across Portal, PowerShell, and Bash |
| **Resource Group** | rg1 |

---

## Objective

This SOP documents the end-to-end process for creating and deploying Azure managed disks using both ARM (JSON) and Bicep templates across three deployment methods: the Azure Portal custom template editor, PowerShell, and Bash (Azure CLI). The lab demonstrates how Infrastructure as Code (IaC) can be applied consistently across multiple toolchains within a single Azure environment.

---

## Prerequisites

- Active Azure subscription with permissions to create resources
- Resource group `rg1` created in your target region (e.g., East US)
- Access to Azure Portal and Azure Cloud Shell (PowerShell and Bash modes)
- Basic familiarity with JSON syntax and Azure resource naming conventions

---

## Step 1 — Create a Disk in the Azure Portal

This step creates the source disk manually and exports the ARM and Bicep templates that will be used for all subsequent deployments.

<img width="2502" height="1188" alt="image" src="https://github.com/user-attachments/assets/d5df672d-72c6-42c2-bc11-d03243ea46e3" />


- Navigate to the Azure Portal and search for **Disks** in the search bar
- Click **Create a disk**
- Select resource group `rg1` and name the disk `AZ-104 Disk 1`
- Set the disk type to **Standard HDD** and select the smallest available size
- Click **Review + create**, then **Create**
- Once the disk is created, open the disk resource
- Under **Automation** in the left menu, select **Export template**
- Download both the ARM template (`template.json`) and the Bicep template (`.bicep` file)
- Save both files locally — these will be uploaded to a storage account in Step 3

---

## Step 2 — Deploy Disk 2 via ARM Template (Azure Portal)

Deploy a second disk using the exported ARM template through the Azure Portal's custom deployment editor.

<img width="2501" height="1148" alt="image" src="https://github.com/user-attachments/assets/1bfbe511-3815-43ac-8cf4-73d5da804674" />


- In the Azure Portal, search for **Deploy a custom template**
- Click **Build your own template in the editor**
- Load the ARM template JSON file downloaded in Step 1
- Locate the disk name parameter in the template
- Change the disk name value to `AZ104Disk2`
- Click **Save**, then **Review + create**, then **Create**
- Confirm the deployment completes successfully in the Azure Portal notifications

---

## Step 3 — Create a Storage Account for Template Files

A storage account is required to host the ARM and Bicep files for CLI-based deployments in PowerShell and Bash.

<img width="1793" height="370" alt="image" src="https://github.com/user-attachments/assets/ef987fd3-a4dd-4239-9e92-b815b7ce0e8e" />


- Open Azure Cloud Shell and select **PowerShell** mode
- Run the following command to create a storage account:

```powershell
New-AzStorageAccount -ResourceGroupName rg1 -Name test12345 -Location EastUS -SkuName Standard_LRS
```

- Wait for the storage account to provision
- In the Azure Portal, navigate to the new storage account
- Click **Upload** and upload both the ARM template JSON file and the Bicep file
- Confirm both files appear in the storage container before proceeding

---

## Step 4 — Deploy Disk 3 via PowerShell (ARM Template)

Use PowerShell and the ARM template to deploy a third disk into `rg1`.

<img width="2515" height="769" alt="image" src="https://github.com/user-attachments/assets/82295527-ddb3-4509-aa28-426512fddac4" />


- In Azure Cloud Shell (PowerShell mode), open the editor
- Select the uploaded ARM template file
- Change the disk name parameter to `disk3`
- Save with `Ctrl + S`, then close with `Ctrl + Q`
- Run the following deployment command:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName rg1 -TemplateFile ./template.json
```

- Wait for the deployment to complete and confirm success in the terminal output
- Run the following command to verify `disk3` appears in the resource group:

```powershell
Get-AzDisk
```

---

## Step 5 — Deploy Disk 4 via Bash (Bicep Template)

Switch to Bash in Azure Cloud Shell and use the Bicep template to deploy a fourth disk.

<img width="2518" height="813" alt="image" src="https://github.com/user-attachments/assets/62211ba4-e2b1-479e-9235-a39b36df002f" /> 
<img width="2515" height="386" alt="image" src="https://github.com/user-attachments/assets/f8b02d34-f250-4717-92b9-535122f9f009" />


- Switch Azure Cloud Shell to **Bash** mode
- Run `ls` to confirm both the ARM JSON and Bicep files are present
- Open the editor and select the Bicep file
- Change the disk name parameter to `disk4`
- Save with `Ctrl + S`, then close with `Ctrl + Q`
- Run the following deployment command:

```bash
az deployment group create --resource-group rg1 --template-file azuredeploydisk.bicep
```

- Confirm the deployment completes successfully in the terminal output

---

## Step 6 — Deploy Disk 5 via Bash (Modified Bicep Template)

Modify the Bicep template with custom parameters — a different disk name, size, and SKU — then deploy a fifth disk.

<img width="2528" height="779" alt="image" src="https://github.com/user-attachments/assets/c7858723-7394-485a-939a-3f7d63cdff3d" />
<img width="2537" height="772" alt="image" src="https://github.com/user-attachments/assets/bf4820dc-bfb5-447c-a4a5-dab6709ade20" />


- Upload the modified Bicep file from the [Microsoft Learning AZ-104 GitHub repository](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/blob/master/Allfiles/Labs/03/azuredeploydisk.bicep)
- Open the Bicep file in the Cloud Shell editor
- Change the disk name to `AZ104Disk5`
- Set the disk size to `32 GB`
- Set the SKU to `Standard SSD LRS`
- Save with `Ctrl + S`, then close with `Ctrl + Q`
- Run the deployment command:

```bash
az deployment group create --resource-group rg1 --template-file azuredeploydisk.bicep
```

- Confirm the deployment completes and `AZ104Disk5` appears in `rg1`

---

## Step 7 — Final Verification

Confirm all five disks were successfully deployed into `rg1`.

<img width="2514" height="353" alt="image" src="https://github.com/user-attachments/assets/c33dd462-0939-4cad-91b8-3bc2c82cc6b5" />


- In PowerShell, run the following to list all disks:

```powershell
Get-AzDisk
```

- In Bash, run the following for a clean formatted view:

```bash
az disk list --resource-group rg1 --output table
```

- Confirm all five disks are present: `AZ-104 Disk 1`, `AZ104Disk2`, `disk3`, `disk4`, `AZ104Disk5`
- Verify `AZ104Disk5` shows the correct size (32 GB) and SKU (Standard SSD LRS)
- Take screenshots of the output as evidence for your lab documentation

---

## Cautionary Notes

- Always confirm resource group `rg1` exists before running any deployment commands — deployments to a non-existent group will fail
- Use unique disk names across all deployments to avoid naming conflicts in the same resource group
- Verify the storage account name (`test12345`) is globally unique — Azure storage account names must be unique across all of Azure
- After editing template files in the Cloud Shell editor, always save (`Ctrl + S`) and close (`Ctrl + Q`) before running deployment commands
- Managed disks incur costs while active — delete all lab resources after completing the lab to avoid unexpected charges

---

## Tips for Efficiency

- Keep the ARM and Bicep files in a shared lab folder and reuse them across future deployments — only the parameter values need to change
- Use a parameters file (`disk.parameters.json`) instead of editing the template directly each time — this keeps the template clean and deployment-ready
- Use consistent naming conventions (`AZ104Disk1`, `AZ104Disk2`, etc.) to make verification and cleanup easier
- Test portal connectivity before running CLI deployments — if the portal is slow, Cloud Shell may also be affected
- Pin the Disks blade and Cloud Shell to your Azure Portal dashboard for faster navigation during the lab

---

## Command Reference

| Tool | Command | Purpose |
|---|---|---|
| PowerShell | `New-AzResourceGroupDeployment -ResourceGroupName rg1 -TemplateFile ./template.json` | Deploy ARM template |
| PowerShell | `Get-AzDisk` | Verify all deployed disks |
| Bash (CLI) | `az deployment group create --resource-group rg1 --template-file azuredeploydisk.bicep` | Deploy Bicep template |
| Bash (CLI) | `az disk list --resource-group rg1 --output table` | List disks in rg1 |

