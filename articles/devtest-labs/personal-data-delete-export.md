---
title: Azure DevTest Labs から個人データを削除およびエクスポートする方法
description: Azure DevLast Labs サービスから個人データを削除およびエクスポートして、一般データ保護規則 (GDPR) での義務を果たす方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2c44b2f3aa6f2dfad18ed53804842a5dad8bd94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483518"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Azure DevTest Labs から個人データをエクスポートまたは削除する
この記事では、Azure DevTest Labs サービスから個人データを削除およびエクスポートする手順を説明します。 

## <a name="what-personal-data-does-devtest-labs-collect"></a>DevTest Labs によって収集される個人データ
DevTest Labs では、ユーザーから 2 つの主な個人データが収集されます。 これらは、ユーザーの電子メール アドレスとユーザー オブジェクト ID です。 この情報は、サービスがラボ管理者とラボ ユーザーに現在の機能を提供するために不可欠です。

### <a name="user-email-address"></a>ユーザーの電子メール アドレス
DevTest Labs では、ユーザーの電子メール アドレスを使用して、ラボ ユーザーに自動シャットダウンの電子メール通知を送信します。 電子メールでは、ユーザーのマシンがシャットダウンされることをそのユーザーに通知します。 ユーザーは、必要に応じてシャットダウンを延期またはスキップできます。 電子メール アドレスは、ラボ レベルまたは VM レベルで構成します。

**ラボでの電子メールの設定:**

![ラボ レベルでの電子メールの設定](./media/personal-data-delete-export/lab-user-email.png)

**VM での電子メールの設定:**

![VM レベルでの電子メールの設定](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ユーザー オブジェクト ID
DevTest Labs では、ユーザー オブジェクト ID を使用して、ラボ管理者に前月比のコスト傾向とリソース別のコストを示します。 これにより、ラボのコストを追跡し、しきい値を管理することができます。 

**現在のカレンダー月の推定コスト傾向:** 
![現在のカレンダー月の推定コスト傾向](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**リソース別の推定前月比コスト:** 
![リソース別の推定前月比コスト](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>この個人データが必要な理由
DevTest Labs サービスでは、運用の目的で個人データを使用します。 このデータは、サービスが主要機能を提供するために不可欠です。 ユーザーの電子メール アドレスに対してアイテム保持ポリシーを設定した場合、ラボ ユーザーは、電子メール アドレスがシステムから削除された後、自動シャットダウンの電子メール通知をタイムリーに受信しません。 同様に、ラボ管理者は、アイテム保持ポリシーに基づいてユーザー オブジェクト ID が削除された場合に、ラボ内のマシンの前月比コスト傾向とリソース別コストを表示できません。 そのため、このデータは、ラボ内でユーザーのリソースがアクティブになっている限り保持する必要があります。

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>システムに個人データを破棄させる方法
ラボ ユーザーは、この個人データを削除したい場合に、ラボ内の対応するリソースを削除することによって削除できます。 DevTest Labs サービスは、ユーザーによって削除された個人データを 30 日後に匿名化します。

たとえば、VM を削除するか、電子メール アドレスを削除した場合、DevTest Labs サービスでは、リソースの削除から 30 日後にこのデータが匿名化されます。 削除後 30 日間のアイテム保持ポリシーによって、ラボ管理者には正確な前月比コスト予測が確実に提供されます。

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>個人データのエクスポートを要求する方法
Azure portal または PowerShell を使用して個人用およびラボ用の使用状況データをエクスポートできます。 データは 2 つの異なる CSV ファイルとしてエクスポートされます。

- **disks.csv** - さまざまな VM で使用されているディスクに関する情報が含まれています。
- **virtualmachines.csv** - ラボ内の VM に関する情報が含まれています。

### <a name="azure-portal"></a>Azure portal
ラボ ユーザーは、DevTest Labs サービスによって保存される個人データのエクスポートを要求できます。 エクスポートを要求するには、ラボの **[概要]** ページで **[個人データ]** オプションに移動します。 **[エクスポートの要求]** を選択すると、ラボ管理者のストレージ アカウント内でダウンロード可能な excel ファイルの作成が開始します。 ラボ管理者に連絡して、このデータを表示できます。

1. 左側のメニューの **[個人データ]** を選択します。 

    ![[個人データ] ページ](./media/personal-data-delete-export/personal-data-page.png)
2. ラボが含まれる **リソース グループ** を選択します。

    ![リソース グループの選択](./media/personal-data-delete-export/select-resource-group.png)
3. リソース グループ内の **ストレージ アカウント** を選択します。
4. **[ストレージ アカウント]** ページで **[BLOB]** を選択します。

    ![BLOB の選択タイル](./media/personal-data-delete-export/select-blobs-tile.png)
5. コンテナーの一覧で、**labresourceusage** という名前のコンテナーを選択します。

    ![BLOB コンテナーの選択](./media/personal-data-delete-export/select-blob-container.png)
6. ラボにちなんだ名前の **フォルダー** を選択します。 このフォルダー内に、ラボ内の **ディスク** と **仮想マシン** の **csv** ファイルが見つかります。 これらの csv ファイルをダウンロードし、アクセスを要求しているラボ ユーザーのコンテンツをフィルター処理して、それらのユーザーと共有できます。

    ![CSV ファイルのダウンロード](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

上記のサンプルの主要なコンポーネントは次のとおりです。

- Invoke-AzureRmResourceAction コマンド。
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- 2 つのアクション パラメーター
    - **blobStorageAbsoluteSasUri** - Shared Access Signature (SAS) トークンを含むストレージ アカウントの URI。 PowerShell スクリプトでは、ストレージ キーの代わりにこの値を渡すことができます。
    - **usageStartDate** - データを取得する開始日。終了日は、アクションが実行された現在の日付です。 粒度は日単位なので、時間情報を追加しても無視されます。

### <a name="exported-data---a-closer-look"></a>エクスポートされるデータ - 詳細
それでは、エクスポートされたデータを詳しく見てみましょう。 前述のように、データが正常にエクスポートされると、2 つの CSV ファイルが作成されます。 

**virtualmachines.csv** には、次のデータ列が含まれています。

| 列名 | 説明 |
| ----------- | ----------- | 
| SubscriptionId | ラボが存在するサブスクリプション識別子。 |
| LabUId | ラボの一意の GUID 識別子。 |
| LabName | ラボの名前。 |
| LabResourceId | 完全修飾ラボ リソース ID。 |
| ResourceGroupName | VM が含まれているリソース グループの名前 | 
| ResourceId | VM の完全修飾リソース ID。 |
| ResourceUId | VM の GUID |
| 名前 | 仮想マシン名。 |
| CreatedTime | VM が作成された日時。 |
| DeletedDate | VM が削除された日時。 空の場合は、まだ削除されていません。 |
| ResourceOwner | VM の所有者。 値が空の場合、これはクレーム可能 VM です。または、サービス プリンシパルによって作成されます。 |
| PricingTier | VM の価格レベル |
| ResourceStatus | VM の可用性の状態。 まだ存在する場合は Active、VM が削除されている場合は Inactive です。 |
| ComputeResourceId | 完全修飾仮想マシン コンピューティング リソース識別子。 |
| Claimable | VM がクレーム可能 VM の場合は true に設定します | 
| EnvironmentId | 仮想マシンが作成された環境リソース識別子。 VM が環境リソースの一部として作成されていなかった場合は空です。 |
| ExpirationDate | VM の有効期限。 有効期限が設定されていない場合は、空に設定されます。
| GalleryImageReferenceVersion |  VM 基本イメージのバージョン。 |
| GalleryImageReferenceOffer | VM 基本イメージのオファー。 |
| GalleryImageReferencePublisher | VM 基本イメージのパブリッシャー。 |
| GalleryImageReferenceSku | VM 基本イメージの SKU。 |
| GalleryImageReferenceOsType | VM 基本イメージの OS の種類 |
| CustomImageId | VM 基本カスタム イメージの完全修飾 ID。 |

**disks.csv** に含まれるデータ列は次のとおりです。

| 列名 | 説明 | 
| ----------- | ----------- | 
| SubscriptionId | ラボを含むサブスクリプションの ID |
| LabUId | ラボの GUID |
| LabName | ラボの名前 | 
| LabResourceId | ラボの完全修飾リソース ID | 
| ResourceGroupName | ラボが含まれているリソース グループの名前 | 
| ResourceId | VM の完全修飾リソース ID。 |
| ResourceUId | VM の GUID |
 |名前 | 接続されたディスクの名前 |
| CreatedTime |データ ディスクが作成された日時。 |
| DeletedDate | データ ディスクが削除された日時。 |
| ResourceStatus | リソースの状態。 リソースが存在する場合は Active です。 削除された場合は Inactive です。 |
| DiskBlobName | データ ディスクの BLOB 名。 |
| DiskSizeGB | データ ディスクのサイズ。 |
| DiskType | データ ディスクの種類。 Standard の場合は 0、Premium の場合は 1。 |
| LeasedByVmId | データ ディスクが接続されている VM のリソース ID。 |


> [!NOTE]
> 複数のラボを扱っていて全体的な情報を取得する場合、2 つのキー列は **LabUID** と **ResourceUId** です。これらはサブスクリプション全体で一意の ID です。

エクスポートされたデータは、SQL Server、Power BI などのツールを使用して操作および視覚化できます。この機能は、実行時に同じ Azure サブスクリプションを使用していない可能性がある管理チームにラボの使用状況を報告する場合に特に便利です。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 

- [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)
- [よく寄せられる質問](devtest-lab-faq.md)
