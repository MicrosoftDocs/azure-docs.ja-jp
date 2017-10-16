---
title: "PowerShell を使用した Azure Stack での Key Vault の管理 | Microsoft Docs"
description: "PowerShell を使用して Azure Stack で Key Vault を管理する方法を説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: d1ce9f81006809aa3c3c07744298a8194971e0b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>PowerShell を使用した Azure Stack での Key Vault の管理

この記事は、PowerShell を使用した Azure Stack での Key Vault の作成および管理に取りかかるのに役立ちます。 この記事で説明する Key Vault の PowerShell コマンドレットは、Azure PowerShell SDK の一部として使用できます。 以下のセクションでは、コンテナーの作成、格納、暗号キーとシークレットの管理、およびユーザーまたはアプリケーションに対するコンテナーでの操作の呼び出しの承認に必要な PowerShell コマンドレットについて説明します。 

## <a name="prerequisites"></a>前提条件
* ユーザーは、Key Vault サービスを含むプランをサブスクライブする必要があります。 
* [PowerShell for Azure Stack のインストール。](azure-stack-powershell-install.md)  
* [Azure Stack ユーザーの PowerShell 環境の構成](azure-stack-powershell-configure-user.md)

## <a name="enable-your-tenant-subscription-for-vault-operations"></a>コンテナー操作のテナント サブスクリプションの有効化

キー コンテナーを操作するには、コンテナー操作のテナント サブスクリプションが有効になっていることを確認する必要があります。 確認するには、次のコマンドを実行します。

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```
**Output**

コンテナー操作のサブスクリプションが有効になっている場合、キー コンテナーのリソースの種類すべてについて「RegistrationState」が「Registered」に等しいという出力になります。

![登録の状態](media/azure-stack-kv-manage-powershell/image1.png)

そうでない場合は、次のコマンドを呼び出して、サブスクリプションで Key Vault サービスを登録します。

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Output**

登録に成功した場合は、次の出力が返されます。

![登録](media/azure-stack-kv-manage-powershell/image2.png)

以下のセクションでは、Key Vault サービスがユーザー サブスクリプション内で登録されているものとします。 キー コンテナーのコマンドを呼び出すときに、「The subscription is not registered to use namespace ‘Microsoft.KeyVault」(サブスクリプションは名前空間 ‘Microsoft.KeyVault を使用するように登録されていません) というエラーが発生する場合は、前に述べた手順に従って [Key Vault リソースプロバイダーを有効に](#enable-your-tenant-subscription-for-vault-operations)したかどうか確認してください。

## <a name="create-a-key-vault"></a>Key Vault を作成します 

キー コンテナーを作成するには、リソース グループを作成して、キー コンテナー関連のすべてのリソースがリソース グループに存在するようにします。 次のコマンドを使用して、新しいリソース グループを作成します。

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**Output**

![新しいリソース グループ](media/azure-stack-kv-manage-powershell/image3.png)

ここで、**New-AzureRMKeyVault** コマンドを使用して、前に作成したリソース グループでキー コンテナーを作成します。 このコマンドは、3 つの必須パラメーター (リソース グループ名、キー コンテナー名、地理的な場所) を読み取ります。 

次のコマンドを実行して、キー コンテナーを作成します。

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```
**Output**

![新しいキー コンテナー](media/azure-stack-kv-manage-powershell/image4.png)

このコマンドの出力は、作成したキー コンテナーのプロパティを示します。 アプリケーションは、このコンテナーにアクセスするときに、出力に示される**コンテナー URI** プロパティを使用します。 たとえば、ここでのコンテナー URI は **https://vault01.vault.local.azurestack.external** です。 REST API を介してこのキー コンテナーと対話するアプリケーションは、この URI を使用する必要があります。

ADFS ベースのデプロイでは、PowerShell を使用してキー コンテナーを作成する場合、「Access policy is not set. No user or application has access permission to use this vault」(アクセス ポリシーが設定されていません。ユーザーまたはアプリケーションにこのコンテナーを使用するアクセス権限がありません) という警告が表示されることがあります。 この問題を解決するには、[Set-AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) コマンドを使用して、コンテナーのアクセス ポリシーを設定します。

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value 

#Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation 
```

## <a name="manage-keys-and-secrets"></a>キーとシークレットの管理

コンテナーを作成したら、次の手順を実行して、コンテナー内でキーとシークレットを作成および管理します。

### <a name="create-a-key"></a>キーの作成

**Add-AzureKeyVaultKey** コマンドを使用して、キー コンテナーでソフトウェアで保護されたキーを作成またはインポートします。 

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```
**Destination** パラメーターは、キーをソフトウェアで保護するよう指定するために使用します。 キーが正常に作成されると、作成されたキーの詳細が出力されます。

**Output**

![新しいキー](media/azure-stack-kv-manage-powershell/image5.png)

これで、作成されたキーの URI を使用してそのキーを参照できます。 既存のキーと同じ名前のキーを作成またはインポートした場合、元のキーは、新しいキーで指定される値で更新されます。  前のバージョンにアクセスするには、キーのバージョン固有の URI を使用します。 たとえば、次のように入力します。 

* **https://vault10.vault.local.azurestack.external:443/keys/key01** を使用すると、常に現在のバージョンが取得されます  
* **https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a** を使用すると、この特定のバージョンが取得されます

### <a name="get-a-key"></a>キーの取得

**Get-AzureKeyVaultKey** コマンドを使用して、キーとその詳細を読み取ります。

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>シークレットの作成

**Set-AzureKeyVaultSecret** コマンドを使用して、コンテナーでシークレットを作成または更新します。 シークレットがまだ存在しない場合はシークレットが作成され、シークレットがすでに存在する場合は新しいバージョンのシークレットが作成されます。

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**Output**

![シークレットの作成](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>シークレットの取得

**Get-AzureKeyVaultSecret** コマンドを使用して、キー コンテナーのシークレットを読み取ります。 このコマンドは、シークレットのすべてのバージョンまたは特定のバージョンを返すことができます。 

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

キーとシークレットを作成したら、それらのキーとシークレットを外部アプリケーションが使用することを承認できます。

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>アプリケーションがキーまたはシークレットを使用することの承認

アプリケーションがキー コンテナーでキーまたはシークレットにアクセスすることを承認するには、**Set-AzureRmKeyVaultAccessPolicy** コマンドを使用します。
たとえば、コンテナー名が ContosoKeyVault で、承認するアプリケーションのクライアント ID が 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed だとします。 次のコマンドを実行してアプリケーションを承認します。 必要に応じて、**PermissionsToKeys** パラメーターを指定して、ユーザー、アプリケーション、またはセキュリティ グループのアクセス許可を設定できます。

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

その同じアプリケーションがコンテナーのシークレットを読み取ることを承認する場合は、次のコマンドレットを実行します。

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>次のステップ
* [キー コンテナーに格納されているパスワードを使用した VM のデプロイ](azure-stack-kv-deploy-vm-with-secret.md)  
* [キー コンテナーに格納されている証明書を使用した VM のデプロイ](azure-stack-kv-push-secret-into-vm.md) 
