**Hands-On Guide to Azure Managed Identities: A Practical Exploration through Blob Storage Access**


**Introduction**

In the dynamic world of cloud computing, ensuring secure access to cloud resources is a constant challenge. Azure Managed Identities offer a secure and streamlined solution, enabling applications to authenticate seamlessly without storing sensitive information like passwords or keys. This guide provides a practical look at Azure Managed Identities by comparing two access methods to Azure Blob Storage from an Azure Virtual Machine (VM).


**Access Methods Covered:**

1. **Using an Access Key**: The traditional approach using a secret key for access. Although functional, this method requires storing the secret key within the application's code, posing a potential security risk.
2. **Using Azure Managed Identity**: A more secure method that uses Azure's built-in managed identity for the VM. This approach does not require storing secret keys in the code, enhancing security and manageability.

By the end of this guide, you'll have a clear understanding of how Azure Managed Identities work and their benefits over traditional access keys.


**Pre-Requisites**

- An Azure account
- An Azure Storage account with a blob container and a sample text file uploaded
- An Azure VM
- Python setup on the Azure VM

**Preparing Your Environment:**

1. **Create a VM**
2. **Install Python**: Download and install Python from the official Python website on the VM, ensuring it's added to your system's PATH.
3. **Install Required Libraries**: Open the Command Prompt on thge VM and install necessary Azure libraries with: "pip install azure-storage-blob azure-identity"

- **Azure CLI Verification**: Use "az identity list" to verify the identity is created.
  
![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/a58d570f-fc45-4904-b751-e35a5b158300)


4. **Create the Storage Account and Upload the Test File in the Blob Container:**

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/9263a640-e4ca-4901-b8e6-6c9bc3f98a3d)

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/e15bafb9-3fdc-4845-9dae-7bf1bb9cce8e)

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/9e20cea1-d2aa-4506-ac27-58820bdf366f)




**Writing and Running the Application:**

1. **Write Your Application**: You can use Notepad or any text editor to write your Python scripts for both scenarios. Save it with a .py extension, for example, blob-MI.py.
2. **Run the Application**: Navigate to the directory where your script is saved using the cd command in Command Prompt, then run the script with: python blob-MI.py


**Additional Notes:**

- Ensure Python is correctly added to your PATH.
- For beginners, try a simple print("Hello, World!") script to test your Python setup.
- ensure that when setting up your virtual machine and storage account, you select the same Azure region for both.
- Make sure to run the correct version of the script depending on whether you're using an access key or Managed Identity.


**Scenario 1: Accessing Azure Blob Storage Using Access Keys**

**Why Access Keys?**

Access keys are a common method for accessing Azure services but require careful management of the keys to avoid security breaches.

**Risks of Using Access Keys**

- **Security**: Keys need to be securely stored and managed. If leaked, they provide full access to the storage account.
- **Maintenance**: Rotating keys for security requires updating all applications using them.

**Step 1: Obtain Storage Account Access Keys**

1. **Log into the Azure Portal**: Open your web browser, navigate to the [Azure Portal](https://portal.azure.com), and sign in with your credentials.
2. **Navigate to Your Storage Account**: From the home page or the search bar at the top, find and select your storage account.
3. **Access Keys**: On the left menu under "Security + networking", click on "Access keys". Here, you'll find two access keys and connection strings.
4. **Copy Key**: Click on the "Show keys" option and copy "Key1" for later use.


![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/4fc9c2e9-2a4e-4af3-a932-a2603ed8e640)


   
**Step 2: Access Blob Storage Using Python**

1. **Remote into Your VM**: Connect to your Azure VM where Python is installed.
2. **Python Script for Blob Access**: Create a new one or update the existing Python script.

'''Python
---------------------------------------------------------------------------
from azure.storage.blob import BlobServiceClient, BlobClient

\# Replace these with your Azure Storage account details
STORAGE_ACCOUNT_NAME = 'your_storage_account_name'
STORAGE_ACCOUNT_KEY = 'your_storage_account_key'
CONTAINER_NAME = 'your_container_name'
BLOB_NAME = 'your_blob_name.txt'

\# Create a BlobServiceClient using the account key
blob_service_client = BlobServiceClient(
    account_url=f"https://{STORAGE_ACCOUNT_NAME}.blob.core.windows.net",
    credential=STORAGE_ACCOUNT_KEY
)

\# Get a client to interact with the specified blob
blob_client = blob_service_client.get_blob_client(CONTAINER_NAME, BLOB_NAME)

\# Read the blob's contents and print it
with open('./DownloadedFile.txt', 'wb') as download_file:
    download_file.write(blob_client.download_blob().readall())

\# Output the contents of the text file
with open('./DownloadedFile.txt', 'r') as file:
    content = file.read()
    print(content)
---------------------------------------------------------------------------
- Replace placeholders with your actual details. This script authenticates using the access key, accesses the specified blob, and prints its contents.

  ![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/51c7f493-8727-4af4-8183-a71ad14a3d6d)


3. **Execute the Script**: Run the script via the terminal or command prompt using python blob-MI.py. The content of your blob should be printed to the console.

   ![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/566fbd9d-e885-4ef8-95f8-48f79f871574)

- **Note:** In Scenario 1, running the script isn't limited to an Azure VM. You can execute the script on any computer, including your laptop, to gain access to the blob.


**Scenario 2:**

**Using Azure Managed Identity for Blob Access**

**What Are Azure Managed Identities?**

Azure Managed Identities eliminate the need for developers to manage credentials. Instead, Azure automatically manages the identities of applications, enabling them to secure access to other Azure services they need to interact with.

**Types of Managed Identities**

- **System-Assigned Managed Identity**: Tied directly to a single Azure service instance. When the service is deleted, the identity is automatically cleaned up by Azure.
- **User-Assigned Managed Identity**: A standalone Azure resource that can be assigned to one or more Azure service instances. It needs to be manually deleted when no longer needed.

**Benefits of Using Managed Identities**

- **Security**: No need to manage credentials in your code, reducing the risk of credential leaks.
- **Simplicity**: Managed identities are automatically managed by Azure, simplifying your application's identity and access management.

**Step 1: Enable Managed Identity on Azure VM**

1. **Azure Portal**: Navigate back to the Azure Portal and to your VM's page.
2. **Identity**: In the left menu, find and select "Identity" under the "Security" group.
3. **System Assigned Managed Identity**: Switch the status toggle to "On" and then click "Save" at the top of the page. Azure will automatically create and assign a managed identity to your VM.

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/dc8368e5-9f22-46e8-b390-e1f5f2bcb612)


**Step 2: Grant Managed Identity Access to Blob Storage**

1. **Navigate to Your Blob Storage Account**: In the Azure Portal, go to your storage account.
2. **Access Control (IAM)**: Click on "Access Control (IAM)" on the left menu.
3. **Add Role Assignment**: Click "Add role assignment", select "Storage Blob Data Contributor" as the role, and then select the managed identity of your VM. Save the changes.

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/f5340394-d4cf-490e-8dee-7a607bdbe149)

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/9943b820-0961-4ca0-8301-1d1663cb23dc)

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/71757afb-4bf4-4616-851b-70589f919ac8)

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/94ac6652-6e07-42d0-bb29-f0ffc2bc2ed4)


**Step 3: Access Blob Storage Using Managed Identity from VM**

1. **Python Script for Blob Access Using Managed Identity**:

'''Python
---------------------------------------------------------------------------
from azure.storage.blob import BlobServiceClient, BlobClient
from azure.identity import DefaultAzureCredential

\# Replace these with your Azure Storage account details
STORAGE_ACCOUNT_NAME = 'your_storage_account_name'
CONTAINER_NAME = 'your_container_name'
BLOB_NAME = 'your_blob_name.txt'

\# Create a BlobServiceClient using DefaultAzureCredential
blob_service_client = BlobServiceClient(
    account_url=f"https://{STORAGE_ACCOUNT_NAME}.blob.core.windows.net",
    credential=DefaultAzureCredential()
)

\# Get a client to interact with the specified blob
blob_client = blob_service_client.get_blob_client(CONTAINER_NAME, BLOB_NAME)

\# Read the blob's contents and print it
with open('./DownloadedFile_MI.txt', 'wb') as download_file:
    download_file.write(blob_client.download_blob().readall())

\# Output the contents of the text file

with open('./DownloadedFile_MI.txt', 'r') as file:
    content = file.read()
    print(content)

---------------------------------------------------------------------------

Replace placeholders with your actual details. This script uses DefaultAzureCredential for authentication, which automatically uses the VM's managed identity when accessing Blob Storage.

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/aef3ef94-dddf-436d-aa40-ca8e45a0e59a)

2. **Execute the Script:** Like before, run the script using python blob-MI.py. The blob's content should now be printed using the managed identity for authentication.

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/369d091c-cb96-4146-8591-1a2dadebcddb)


**Here's what's happening behind the scenes:**

When you enable a system-assigned managed identity for an Azure resource such as a VM, Microsoft Entra ID does indeed create an identity object, but it's not a regular user account. Instead, it's an object that represents the identity of the service (in this case, your VM), and it's classified under the "Enterprise Applications" section rather than the directory's "Users" section.

1. **Identity Creation**: When you turn on the managed identity status to 'On' for your VM, Azure automatically creates an identity in Microsoft Entra ID for that VM. This identity is unique to the VM and is tied to its lifecycle.
2. **Enterprise Applications**: In Microsoft Entra ID, this identity is represented as an "Enterprise Application," which is a different category than a standard user or group. Enterprise Applications are typically used to represent applications and services (not users) that need to access resources in Azure.
3. **Object Type**: The object for the managed identity created in Microsoft Entra ID has a type that reflects that it's a "Service Principal" or an "Enterprise Application," which has a different set of properties and behaviors compared to a "User" object.
4. **Permissions and Roles**: You can assign permissions and roles to this managed identity just like you would for a user. For example, you can give it access to a storage account, which it can use to authenticate and perform actions as permitted by the assigned role.
5. **Security**: This identity is secure because the credentials (like client ID, tenant ID, and a certificate or secret used for authentication) are managed by Azure and are not exposed to you as the user.
6. **Access Tokens**: When your VM wants to access another service, it can request an access token from Microsoft Entra ID for its own identity. Microsoft Entra ID will authenticate the VM based on the managed identity and provide a token that the VM can use to access other Azure services.

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/07d755ef-e8f0-49e6-9dd2-6fc6b062910e)

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/174243ce-5c20-472f-88f1-a9779965c0d9)

![image](https://github.com/babakcloud/azure-managed-identities-guide/assets/133662008/77fab6da-354e-4d00-85c2-04cb3cbb68c7)


So, when you find your VM listed as an "Enterprise Application" in Microsoft Entra ID, it indicates that the managed identity has been successfully created and is ready to be used for accessing other Azure services securely.
