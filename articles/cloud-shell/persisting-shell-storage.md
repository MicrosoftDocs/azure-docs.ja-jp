---
title: Azure Cloud Shell の Bash でファイルを永続化する | Microsoft Docs
description: Azure Cloud Shell の Bash でファイルを永続化する方法についてのチュートリアルです。
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 0aa00af543a3d21db9b8ad0ed808a8bff0b534e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60200269"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell のストレージのしくみ 
Cloud Shell は、次の両方の方法を使用してファイルを永続化します。 
* `$Home` ディレクトリのディスク イメージを作成してディレクトリ内のすべてのコンテンツを永続化します。 このディスク イメージは、指定されたファイル共有の `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` に `acc_<User>.img` として保存され、変更が自動的に反映されます。 
* ファイル共有を直接操作できるように、指定されたファイル共有を `$Home` ディレクトリに `clouddrive` としてマウントします。 `/Home/<User>/clouddrive` は `fileshare.storage.windows.net/fileshare` にマッピングされます。
 
> [!NOTE]
> SSH キーなど、`$Home` ディレクトリ内のすべてのファイルが、マウントされたファイル共有に格納されたユーザー ディスク イメージに永続化されます。 `$Home` ディレクトリおよびマウントされたファイル共有への情報の保存時に、ベスト プラクティスを適用してください。

## <a name="bash-specific-commands"></a>Bash 固有のコマンド

### <a name="use-the-clouddrive-command"></a>`clouddrive` コマンドを使用する
Cloud Shell の Bash では、`clouddrive` というコマンドを実行できます。このコマンドを使うと、Cloud Shell にマウントされているファイル共有を手動で更新できます。
!["clouddrive" コマンドを実行する](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>新しい clouddrive のマウント

#### <a name="prerequisites-for-manual-mounting"></a>手動マウントの前提条件
`clouddrive mount` コマンドを使用して、Cloud Shell と関連付けられているファイル共有を更新することができます。

既存のファイル共有をマウントする場合、ストレージ アカウントは選択した Cloud Shell リージョンにある必要があります。 場所を取得するには、Bash から `env` を実行し、`ACC_LOCATION` をチェックします。

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

## <a name="next-steps"></a>次の手順
[Cloud Shell の Bash のクイックスタート](quickstart.md) <br>
[Cloud Shell の PowerShell のクイック スタート](quickstart-powershell.md) <br>
[Microsoft Azure Files ストレージについて](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[ストレージのタグについて](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
