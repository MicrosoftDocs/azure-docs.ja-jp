---
title: チュートリアル`:` マネージド ID を使用して Azure Storage にアクセスする際に SAS 資格情報を使用する - Azure AD
description: Windows VM のシステム割り当てマネージド ID を使用して、ストレージ アカウント アクセス キーではなく、SAS 資格情報で Azure Storage にアクセスする方法を示すチュートリアル。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b4a6f7915f931e2eff24b56b178957a039e1ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096590"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>チュートリアル: Windows VM のシステム割り当てマネージド ID を使用して SAS 資格情報で Azure Storage にアクセスする

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシン (VM) のシステム割り当て ID を使用して、ストレージ Shared Access Signature (SAS) 資格情報を取得する方法について説明します。 具体的には [Service SAS 資格情報](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)です。 

Service SAS は、アカウント アクセス キーを公開することなく、ストレージ アカウント内のオブジェクトへの限られたアクセス許可を限られた時間にわたって特定のサービス (ここでは BLOB サービス) に対してのみ提供できます。 ストレージ SDK の使用時など、ストレージ操作を実行するときに、SAS 資格情報を通常どおりに使用できます。 このチュートリアルでは、Azure Storage PowerShell を使用して BLOB のアップロードとダウンロードを行う手順を示します。 学習内容:

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * Resource Manager で VM にストレージ アカウント SAS へのアクセス権を付与する 
> * VM の ID を使用してアクセス トークンを取得し、それを使用して Resource Manager から SAS を取得する 

## <a name="prerequisites"></a>前提条件

- マネージド ID の知識。 Azure リソースのマネージド ID 機能に慣れていない場合は、こちらの[概要](overview.md)を参照してください。 
- Azure アカウント。[無料アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
- 必要なリソース作成とロール管理の手順を実行するための、適切なスコープ (サブスクリプションまたはリソース グループ) の "所有者" アクセス許可。 ロールの割り当てに関するサポートが必要な場合は、[Azure ロールの割り当てによる Azure サブスクリプション リソースへのアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。
- システム割り当てマネージド ID が有効になっている Windows 仮想マシンも必要です。
  - このチュートリアル用に仮想マシンを作成する必要がある場合は、[システム割り当てマネージド ID を有効にした仮想マシンの作成](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)に関する記事に従ってください。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

まだお持ちでない場合は、この時点でストレージ アカウントを作成します。 この手順をスキップし、既存のストレージ アカウントの SAS 資格情報へのアクセス権を、VM のシステム割り当てマネージド ID に付与することもできます。 

1. Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2. **[ストレージ]** 、次に **[ストレージ アカウント]** をクリックすると、新しい [ストレージ アカウントの作成] パネルが表示されます。
3. ストレージ アカウントの名前を入力します。この場前は後ほど使用します。  
4. **[デプロイ モデル]** と **[アカウントの種類]** が "Resource Manager" と "汎用" にそれぞれ設定されている必要があります。 
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。
6. **Create** をクリックしてください。

    ![新しいストレージ アカウントを作成する](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>ストレージ アカウントに BLOB コンテナーを作成する

後で、新しいストレージ アカウントにファイルをアップロードおよびダウンロードします。 ファイルには Blob Storage が必要であるため、ファイルを格納する BLOB コンテナーを作成する必要があります。

1. 新たに作成したストレージ アカウントに戻ります。
2. 左側のパネルで、[Blob service] の下の **[コンテナー]** リンクをクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックすると、[新しいコンテナー] パネルがスライドして現れます。
4. コンテナーに名前を付け、アクセス レベルを選択して、 **[OK]** をクリックします。 指定した名前は、後ほどチュートリアルで使用されます。 

    ![ストレージ コンテナーの作成](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>VM のシステム割り当てマネージド ID にストレージ SAS を使用するためのアクセス権を付与する 

Azure Storage は、ネイティブでは Azure AD 認証をサポートしていません。  ただし、マネージド ID を使用して Resource Manager からストレージ SAS を取得し、その SAS を使用してストレージにアクセスできます。  この手順では、ストレージ アカウントの SAS へのアクセス権を VM のシステム割り当てマネージド ID に付与します。   

1. 新たに作成したストレージ アカウントに戻ります。   
2. 左側のパネルの **[アクセス制御 (IAM)]** リンクをクリックします。  
3. ページの上部にある **[+ ロール割り当ての追加]** をクリックして、VM 用に新しいロールの割り当てを追加します
4. ページの右側で、 **[ロール]** を ［ストレージ アカウントの共同作業者］ に設定します。  
5. 次のドロップダウンで、 **[アクセスの割り当て先]** を ［仮想マシン］ リソースに設定します。  
6. 次に、適切なサブスクリプションが **[サブスクリプション]** ドロップダウンにリストされていることを確認してから、 **[リソース グループ]** を [すべてのリソース グループ] に設定します。  
7. 最後に、**[選択]** のドロップダウンで Windows 仮想マシンを選択し、**[保存]** をクリックします。 

    ![イメージ テキスト](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>VM ID を使用してアクセス トークンを取得し、そのアクセス トークンを使用して Azure Resource Manager を呼び出す 

チュートリアルの残りの部分では、VM から作業を行います。

ここでは、Azure Resource Manager PowerShell コマンドレットを使用する必要があります。  インストールしていない場合は、先に進む前に、[最新バージョンをダウンロード](/powershell/azure/)してください。

1. Azure Portal で **[Virtual Machines]** にナビゲートして Windows 仮想マシンに移動し、**[概要]** ページの上部にある **[接続]** をクリックします。
2. Windows VM を作成したときに追加した **ユーザー名** と **パスワード** を入力します。 
3. これで、仮想マシンとの **リモート デスクトップ接続** が作成されました。
4. リモート セッションで PowerShell を開き、Invoke-WebRequest を使用して、Azure リソース エンドポイントのローカル マネージド ID から Azure Resource Manager トークンを取得します。

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "resource"パラメーターの値は、Azure AD で予期される値と完全に一致している必要があります。 Azure Resource Manager のリソース ID を使用する場合は、URI の末尾にスラッシュを含める必要があります。
    
    次に、$response オブジェクト内で JavaScript Object Notation (JSON) 形式の文字列として格納されている "Content" 要素を抽出します。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    次に、アクセス トークンを応答から抽出します。
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>ストレージ呼び出しを行うために Azure Resource Manager から SAS 資格情報を取得する 

ここで、PowerShell を使用して、前のセクションで取得したアクセス トークンで Resource Manager を呼び出し、ストレージ SAS 資格情報を作成します。 SAS 資格情報を作成したら、ストレージ操作を呼び出すことができます。

この要求のために、次の HTTP 要求のパラメーターを使用して SAS 資格情報を作成します。

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

これらのパラメーターは、SAS 資格情報に対する要求の POST 本文に含まれます。 SAS 資格情報を作成するためのパラメーターの詳細については、[List Service SAS REST リファレンス](/rest/api/storagerp/storageaccounts/listservicesas)に関する記事を参照してください。

最初にパラメーターを JSON に変換し、その後で SAS 資格情報を作成するストレージの `listServiceSas` エンドポイントを呼び出します。

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> URL では大文字小文字が区別されるため、リソース グループの命名時に以前使用したものと同じ大文字小文字が使用されていること ("resourceGroups" の "G" が大文字であることを含む) を確認してください。 

これで、応答から SAS 資格情報を抽出できます。

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

SAS 資格情報を調べると、次のように表示されます。

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

次に、"test.txt" というファイルを作成します。 その後、SAS 資格情報を使用して `New-AzStorageContent` コマンドレットで認証を行い、ファイルを BLOB コンテナーにアップロードしてから、ファイルをダウンロードします。

```bash
echo "This is a test text file." > test.txt
```

必ず、最初に `Install-Module Azure.Storage` を使用して Azure Storage コマンドレットをインストールしてください。 その後、作成した BLOB を、次のように `Set-AzStorageBlobContent` PowerShell コマンドレットを使用してアップロードします。

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

応答:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

アップロードした BLOB を、次のように `Get-AzStorageBlobContent` PowerShell コマンドレットを使用してダウンロードすることもできます。

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

応答:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Windows VM のシステム割り当てマネージド ID を使用して SAS 資格情報で Azure Storage にアクセスする方法について説明しました。  Azure Storage SAS の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
>[Shared Access Signatures (SAS) の使用](../../storage/common/storage-sas-overview.md)