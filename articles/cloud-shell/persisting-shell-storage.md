---
title: Azure Cloud Shell でファイルを永続化する | Microsoft Docs
description: Azure Cloud Shell でファイルを永続化する方法についてのチュートリアルです。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: f1846c126e81ca5851cfbb1d782e5315ae10a82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152274"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud Shell でファイルを永続化する
Cloud Shell では Azure File Storage を使用してセッション間でファイルを維持します。 Cloud Shell の初回起動時に、セッション間でファイルを維持するために新しいまたは既存のファイル共有を関連付けることを求められます。

> [!NOTE]
> Bash と PowerShell は、同じファイル共有を共有します。 Cloud Shell で自動マウントと関連付けることができるのは、1 つのファイル共有のみです。

> [!NOTE]
> Cloud Shell ストレージ アカウントでは Azure Storage ファイアウォールはサポートされません。

## <a name="create-new-storage"></a>新しいストレージの作成

基本設定を使用し、サブスクリプションのみを選択すると、Cloud Shell では、最寄りのサポートされるリージョンに 3 つのリソースが自動的に作成されます。
* リソース グループ: `cloud-shell-storage-<region>`
* ストレージ アカウント: `cs<uniqueGuid>`
* ファイル共有: `cs-<user>-<domain>-com-<uniqueGuid>`

![サブスクリプション設定](media/persisting-shell-storage/basic-storage.png)

ファイル共有は `clouddrive` ディレクトリに `$Home` という名前でマウントされます。 これは 1 度限りのアクションであり、ファイル共有は以降のセッションで自動的にマウントされます。 

また、5 GB のイメージを含むファイル共有が作成され、これによって `$Home` ディレクトリに自動的にデータが維持されます。 これは、Bash と PowerShell の両方に適用されます。

## <a name="use-existing-resources"></a>既存のリソースの使用

詳細設定オプションを使用すると、既存のリソースを関連付けることができます。 Cloud Shell リージョンを選択する場合、同じリージョンに併置されたバッキング ストレージ アカウントを選択する必要があります。 たとえば、割り当てリージョンが米国西部である場合、同様に米国西部に存在するファイル共有を関連付ける必要があります。

ストレージのセットアップを促す画面が表示されたら、 **[詳細設定の表示]** を選択して、追加オプションを表示します。 設定されているストレージ オプションは、ローカル冗長ストレージ (LRS) アカウント、 geo 冗長ストレージ (GRS) アカウント、およびゾーン冗長ストレージ (ZRS) アカウントをフィルター処理します。 

> [!NOTE]
> バッキング ファイル共有の回復性を強化するには、GRS または ZRS のストレージ アカウントの使用をお勧めします。 どちらの種類の冗長性を選ぶかは、実際の目標と価格の希望により異なります。 [詳細については、Azure Storage アカウントのレプリケーションのオプションを参照してください](../storage/common/storage-redundancy.md)。

![リソース グループ設定](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>ストレージ アクセスのセキュリティ保護
セキュリティのために、各ユーザーが自分のストレージ アカウントをプロビジョニングする必要があります。  Azure ロールベースのアクセス制御 (Azure RBAC) では、ユーザーはストレージ アカウント レベルで共同作成者以上のアクセス権を持つ必要があります。

Cloud Shell では、指定されたサブスクリプション内のストレージ アカウントで Azure ファイル共有が使用されます。 アクセス許可が継承されるため、サブスクリプションに対する十分なアクセス権を持つユーザーは、サブスクリプションに含まれるすべてのストレージ アカウントとファイル共有にアクセスできます。

ユーザーは、ストレージ アカウントまたはサブスクリプション レベルでアクセス許可を設定することによって、各自のファイルへのアクセスをロックダウンする必要があります。

## <a name="supported-storage-regions"></a>サポートされているストレージ リージョン
現在のリージョンを確認するには、Bash で `env` を実行して変数 `ACC_LOCATION` を見つけるか、PowerShell から `$env:ACC_LOCATION` を実行します。 ファイル共有には、`$Home` ディレクトリの永続化に使用できる 5 GB のイメージが割り当てられます。

Cloud Shell マシンは、次の各リージョンに存在します。

|領域|リージョン|
|---|---|
|アメリカ|米国東部、米国中南部、米国西部|
|ヨーロッパ|北ヨーロッパ、西ヨーロッパ|
|アジア太平洋|インド中部、東南アジア|

お客様は、保存データを特定のリージョンに格納するという要件がない限り、プライマリ リージョンを選択する必要があります。 このような要件がある場合は、セカンダリ ストレージ リージョンを使用する必要があります。

### <a name="secondary-storage-regions"></a>セカンダリ ストレージ リージョン
セカンダリ ストレージ リージョンが使用される場合、関連付けられている Azure ストレージ アカウントの配置場所は、マウント先の Cloud Shell マシンとは別のリージョンになります。 たとえば、Jane は、自分のストレージ アカウントをカナダ東部 (セカンダリ リージョン) に配置するように設定できますが、自分がマウントされるコンピューターは依然としてプライマリ リージョンにあります。 保存データはカナダにありますが、処理は米国で行われます。

> [!NOTE]
> セカンダリ リージョンが使用される場合、Cloud Shell のファイル アクセスと起動時間が遅くなる可能性があります。

ユーザーは、PowerShell で `(Get-CloudDrive | Get-AzStorageAccount).Location` を実行して、ファイル共有の場所を確認できます。

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure リソース ポリシーによるリソース作成の制限
Cloud Shell で作成したストレージ アカウントは `ms-resource-usage:azure-cloud-shell` でタグ付けされます。 Cloud Shell からストレージ アカウントを作成できないようにするには、この固有のタグによってトリガーされる[タグの Azure リソース ポリシー](../governance/policy/samples/index.md)を作成します。

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell のストレージのしくみ 
Cloud Shell は、次の両方の方法を使用してファイルを永続化します。 
* `$Home` ディレクトリのディスク イメージを作成してディレクトリ内のすべてのコンテンツを永続化します。 このディスク イメージは、指定されたファイル共有の `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` に `acc_<User>.img` として保存され、変更が自動的に反映されます。 
* ファイル共有を直接操作できるように、指定されたファイル共有を `$Home` ディレクトリに `clouddrive` としてマウントします。 `/Home/<User>/clouddrive` は `fileshare.storage.windows.net/fileshare` にマッピングされます。
 
> [!NOTE]
> SSH キーなど、`$Home` ディレクトリ内のすべてのファイルが、マウントされたファイル共有に格納されたユーザー ディスク イメージに永続化されます。 `$Home` ディレクトリおよびマウントされたファイル共有への情報の保存時に、ベスト プラクティスを適用してください。

## <a name="clouddrive-commands"></a>clouddrive コマンド

### <a name="use-the-clouddrive-command"></a>`clouddrive` コマンドを使用する
Cloud Shell では、`clouddrive` というコマンドを実行できます。このコマンドを使うと、Cloud Shell にマウントされているファイル共有を手動で更新できます。

!["clouddrive" コマンドの実行](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>`clouddrive` の一覧取得
`clouddrive` としてマウントされているファイル共有を検出するには、`df` コマンドを実行します。 

clouddrive へのファイル パスの URL に、お使いのストレージ アカウント名とファイル共有が表示されます。 たとえば、`//storageaccountname.file.core.windows.net/filesharename` のように指定します。

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>新しい clouddrive のマウント

#### <a name="prerequisites-for-manual-mounting"></a>手動マウントの前提条件
`clouddrive mount` コマンドを使用して、Cloud Shell と関連付けられているファイル共有を更新することができます。

既存のファイル共有をマウントする場合、ストレージ アカウントは選択した Cloud Shell リージョンにある必要があります。 場所を取得するには、`env` を実行し、`ACC_LOCATION` をチェックします。

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` コマンド

> [!NOTE]
> 新しいファイル共有をマウントすると、新しいユーザー イメージが `$Home` ディレクトリ用に作成されます。 前回の `$Home` イメージは、前のファイル共有に保持されます。

次のパラメーターを指定して `clouddrive mount` コマンドを実行します。

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

詳細情報を表示するには、次のように `clouddrive mount -h` を実行します。

!["clouddrive mount" コマンドを実行する](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>clouddrive のマウント解除
Cloud Shell にマウントされたファイル共有は、いつでもマウントを解除することができます。 Cloud Shell はマウントされたファイル共有を使う必要があるため、次回のセッション時に別のファイル共有を作成してマウントするように求められます。

1. `clouddrive unmount` を実行します。
2. プロンプトに同意して確定します。

作成したファイル共有は、手動で削除するまで存在します。 以降のセッションから、このファイル共有は、Cloud Shell による検索の対象外になります。 詳細情報を表示するには、次のように `clouddrive unmount -h` を実行します。

!["clouddrive unmount" コマンドを実行する](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> このコマンドを実行してもリソースは削除されませんが、Cloud Shell にマップされているリソース グループ、ストレージ アカウント、またはファイル共有を手動で削除すると、ファイル共有内にある `$Home` ディレクトリ ディスク イメージとすべてのファイルは削除されます。 この削除操作は元に戻すことができません。
## <a name="powershell-specific-commands"></a>PowerShell 固有のコマンド

### <a name="list-clouddrive-azure-file-shares"></a>`clouddrive` Azure ファイル共有の一覧表示
`Get-CloudDrive` コマンドレットは、Cloud Shell の `clouddrive` によって現在マウントされている Azure ファイル共有の情報を取得します。 <br>
![Get-CloudDrive の実行](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>`clouddrive` のマウントを解除する
Cloud Shell にマウントされた Azure ファイル共有は、いつでもマウントを解除することができます。 Azure ファイル共有が削除された場合は、次回のセッション時に新しい Azure ファイル共有を作成してマウントするように求められます。

`Dismount-CloudDrive` コマンドレットは、現在のストレージ アカウントから Azure ファイル共有をマウント解除します。 `clouddrive` をマウント解除すると、現在のセッションが終了します。 次のセッションでは、新しい Azure ファイル共有を作成してマウントするように求められます。
![Dismount-CloudDrive の実行](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

注:ファイル内に関数を定義して、PowerShell コマンドレットから呼び出す必要がある場合は、ドット演算子が含まれていることが要件になります。 例: . .\MyFunctions.ps1

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md) <br>
[Microsoft Azure Files ストレージについて](../storage/files/storage-files-introduction.md) <br>
[ストレージのタグについて](../azure-resource-manager/management/tag-resources.md) <br>