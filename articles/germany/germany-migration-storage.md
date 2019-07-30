---
title: Azure ストレージ リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure ストレージ リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 572313f65d5d97211dcb664d79122ea6be520bab
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786548"
---
# <a name="migrate-storage-resources-to-global-azure"></a>ストレージ リソースをグローバル Azure に移行する

この記事には、Azure ストレージ リソースの Azure Germany からグローバル Azure への移行に役立つ可能性のある情報が含まれています。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="blobs"></a>BLOB

AzCopy は、BLOB、ファイル、およびテーブルをコピーするために使用できる無料のツールです。 AzCopy は、Azure から Azure へ、オンプレミスから Azure へ、および Azure からオンプレミスへの移行に対して機能します。 BLOB を直接 Azure Germany からグローバル Azure にコピーする移行には AzCopy を使用してください。

ソース VM にマネージド ディスクを使用していない場合は、AzCopy を使用して .vhd ファイルをターゲット環境にコピーします。 それ以外の場合は、事前にいくつかの手順を完了する必要があります。 詳細については、[マネージド ディスクに関する推奨事項](#managed-disks)を参照してください。

次の例は、AzCopy の動作を示しています。 完全なリファレンスについては、[AzCopy のドキュメント](../storage/common/storage-use-azcopy.md)を参照してください。

AzCopy では、URI として表された **Source** と **Dest** という用語を使用します。 Azure Germany の URI は常に次の形式です。

```http
https://<storageaccountname>.blob.core.cloudapi.de/<containername>/<blobname>
```

グローバル Azure の URI は常に次の形式です。

```http
https://<storageaccountname>.blob.core.windows.net/<containername>/<blobname>
```

URI の 3 つの部分 (*storageaccountname*、*containername*、*blobname*) は、PowerShell または Azure CLI を使用してポータルからから取得します。 BLOB の名前は URI の一部にすることも、パターン (*vm121314.vhd* など) として指定することもできます。

また、Azure Storage アカウントにアクセスするためのストレージ アカウント キーも必要です。 これらは、PowerShell または CLI を使用してポータルから取得します。 例:

```powershell
Get-AzStorageAccountKey -Name <saname> -ResourceGroupName <rgname>
```

いつものように、各ストレージ アカウントの 2 つのキーのうちの 1 つだけが必要です。

例:

URI の部分 | 値の例
-------- | --------------
ソースの storageAccount | `migratetest`
ソースの container | `vhds`
ソースの blob | `vm-121314.vhd`
ターゲットの storageAccount | `migratetarget`
ターゲットの container | `targetcontainer`

このコマンドは、仮想ハード ディスクを Azure Germany からグローバル Azure にコピーします (キーは読みやすくするために短縮されています)。

```cmd
azcopy -v /source:https://migratetest.blob.core.cloudapi.de/vhds /sourcekey:"0LN...w==" /dest:https://migratetarget.blob.core.windows.net/targetcontainer /DestKey:"o//ucDi5TN...w==" /Pattern:vm-121314.vhd
```

VHD の一貫性のあるコピーを取得するには、VHD をコピーする前に VM をシャットダウンします。 コピー アクティビティのために、ある程度のダウンタイムを計画してください。 VHD がコピーされたら、[ターゲット環境で VM を再構築します](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

詳細:

- [AzCopy のドキュメント](../storage/common/storage-use-azcopy.md)を確認してください。
- [復元されたディスクから VM を作成する](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)方法を学習してください。

## <a name="managed-disks"></a>Managed Disks

Azure Managed Disks は、VM ディスクに関連付けられたストレージ アカウントを管理することによって、IaaS (Azure サービスとしてのインフラストラクチャ) VM のディスク管理を簡素化します。 

.vhd ファイルには直接アクセスできないため、直接 AzCopy などのツールを使用してファイルをコピーすることはできません (「[BLOB](#blobs)」を参照)。 対処法として、まず一時的な Shared Access Signature URI を取得することによってマネージド ディスクをエクスポートし、次にこの情報を使用してそれをダウンロードまたはコピーします。 以降のセクションでは、Shared Access Signature URI を取得する方法と、それを使用して実行する内容の例を示します。

### <a name="step-1-get-the-shared-access-signature-uri"></a>手順 1:Shared Access Signature URI を取得する

1. ポータルで、マネージド ディスクを検索します。 (それは、VM と同じリソース グループにあります。 リソースの種類は **[ディスク]** です。)
1. **[概要]** ページで、最上位メニューの **[エクスポート]** ボタンを選択します (まず VM をシャットダウンして割り当て解除するか、または VM を接続解除する必要があります)。
1. URI を期限切れにする時間を定義します (既定値は 3,600 秒です)。
1. URL を生成します (この手順は数秒しかかかりません)。
1. URL をコピーします (これは 1 回しか表示されません)。

### <a name="step-2-azcopy"></a>手順 2:AzCopy

AzCopy を使用する方法の例については、「[BLOB](#blobs)」を参照してください。 ディスクを直接ソース環境からターゲット環境にコピーするには、AzCopy (または同様のツール) を使用します。 AzCopy で、URI をベース URI と Shared Access Signature の部分に分割する必要があります。 URI の Shared Access Signature の部分は、文字 " **?** " で始まります。 ポータルは、Shared Access Signature URI としてこの URI を提供します。

```http
https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```

次のコマンドは、AzCopy のソース パラメーターを示しています。

```cmd
/source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd"
```

```cmd
/sourceSAS:" ?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```

完全なコマンドを次に示します。

```cmd
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.windows.net/targetcontainer/newdisk.vhd" /DestKey:"o//ucD... Kdpw=="
```

### <a name="step-3-create-a-new-managed-disk-in-the-target-environment"></a>手順 3:ターゲット環境で新しいマネージド ディスクを作成する

新しいマネージド ディスクを作成するにはいくつかのオプションがあります。 Azure Portal で行う方法を次に示します。

1. ポータルで、 **[新規作成]**  >  **[マネージド ディスク]**  >  **[作成]** を選択します。
1. 新しいディスクの名前を入力します。
1. リソース グループを選択します。
1. **[ソースの種類]** で、 **[Storage blob] (ストレージ BLOB)** を選択します。 次に、AzCopy コマンドからターゲット URI をコピーするか、またはターゲット URI を参照して選択します。
1. OS ディスクをコピーした場合は、 **[OS]** の種類を選択します。 その他のディスクの種類の場合は、 **[作成]** を選択します。

### <a name="step-4-create-the-vm"></a>手順 4:VM の作成

先に説明したように、この新しいマネージド ディスクを使用して VM を作成するには複数の方法があります。 2 つのオプションを次に示します。

- ポータルで、ディスクを選択してから、 **[VM の作成]** を選択します。 通常どおりに VM の他のパラメーターを定義します。
- PowerShell の場合は、「[復元されたディスクからの VM の作成](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)」を参照してください。

詳細:

- Shared Access Signature URI を取得することによって [API 経由で](/rest/api/compute/disks/grantaccess)ディスクにエクスポートする方法を学習してください。 
- 非管理対象の BLOB から [API 経由で](/rest/api/compute/disks/createorupdate#create-a-managed-disk-by-importing-an-unmanaged-blob-from-a-different-subscription.)マネージド ディスクを作成する方法を学習してください。


## <a name="next-steps"></a>次の手順

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Web](./germany-migration-web.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [セキュリティ](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)

