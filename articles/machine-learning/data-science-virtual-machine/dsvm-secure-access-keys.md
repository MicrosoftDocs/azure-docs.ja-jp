---
title: アクセス資格情報を安全に保存する
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine にアクセス資格情報を安全に保存する方法を説明します。 マネージド サービス ID と Azure Key Vault を使用してアクセス資格情報を格納する方法を学習します。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: adc66fc794ac6cce3fc0f8fd1552f701a4968860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519679"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine にアクセス資格情報を安全に保存する

クラウド アプリケーションのコードには、クラウド サービスを認証するための資格情報を含めるのが一般的です。 これらの資格情報をどのように管理し、セキュリティで保護するかは、クラウド アプリケーションを構築する際によく知られている課題です。 資格情報は、開発者のワークステーションに一切表示されず、ソース管理でも確認されないことが理想的です。

[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) 機能を使用すると、Azure Active Directory (Azure AD) 内で自動的にマネージド ID を Azure サービスに付与することで、この問題が簡単に解決されます。 この ID を使用すると、コードに資格情報が含まれていなくても、Azure AD 認証をサポートする任意のサービスの認証を行えます。

資格情報をセキュリティで保護するには、Windows インストーラー (MSI) と、シークレットおよび暗号化キーを安全に格納するための Azure のマネージド サービスである [Azure Key Vault](../../key-vault/index.yml) を組み合わせて使用する方法があります。 マネージド ID を使用してキー コンテナーにアクセスし、キー コンテナーから承認済みのシークレットと暗号化キーを取得することができます。

Azure リソースのマネージド ID と Key Vault に関するドキュメントは、これらのサービスの詳細情報についての包括的なリソースで構成されています。 この記事の残りの部分では、データ サイエンス仮想マシン (DSVM) で MSI と Key Vault を使用して Azure リソースにアクセスする基本的な方法について説明します。 

## <a name="create-a-managed-identity-on-the-dsvm"></a>DSVM 上でマネージド ID を作成する

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>VM プリンシパルに Key Vault のアクセス許可を割り当てる

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>DSVM からキー コンテナー内のシークレットにアクセスする

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>DSVM からストレージ キーにアクセスする

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Python からキー コンテナーにアクセスする

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Azure CLI からキー コンテナーにアクセスする

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```