---
title: "Azure Cloud Shell (プレビュー) の Bash でファイルを永続化する | Microsoft Docs"
description: "Azure Cloud Shell の Bash でファイルを永続化する方法についてのチュートリアルです。"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 8b8a82e1c1328d952a85ea5afd975a95f5a6e740
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell のストレージのしくみ 
Cloud Shell は、次の両方の方法を使用してファイルを永続化します。 
* `$Home` ディレクトリのディスク イメージを作成してディレクトリ内のすべてのコンテンツを永続化します。 このディスク イメージは、指定されたファイル共有の `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` に `acc_<User>.img` として保存され、変更が自動的に反映されます。 
* ファイル共有を直接操作できるように、指定されたファイル共有を `$Home` ディレクトリに `clouddrive` としてマウントします。 `/Home/<User>/clouddrive` は `fileshare.storage.windows.net/fileshare` にマッピングされます。
 
> [!NOTE]
> SSH キーなど、`$Home` ディレクトリ内のすべてのファイルが、マウントされたファイル共有に格納されたユーザー ディスク イメージに永続化されます。 `$Home` ディレクトリおよびマウントされたファイル共有への情報の保存時に、ベスト プラクティスを適用してください。

## <a name="use-the-clouddrive-command"></a>`clouddrive` コマンドを使用する
Cloud Shell の Bash では、`clouddrive` というコマンドを実行できます。このコマンドを使うと、Cloud Shell にマウントされているファイル共有を手動で更新できます。
!["clouddrive" コマンドを実行する](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>新しい clouddrive のマウント

### <a name="prerequisites-for-manual-mounting"></a>手動マウントの前提条件
`clouddrive mount` コマンドを使用して、Cloud Shell と関連付けられているファイル共有を更新することができます。

既存のファイル共有をマウントする場合、ストレージ アカウントは次の条件を満たす必要があります。
* ファイル共有をサポートするローカル冗長ストレージまたは geo 冗長ストレージ。
* 自分の割り当てリージョンに存在すること。 オンボーディング時に、自分に割り当てられているリージョンが、リソース グループ名 `cloud-shell-storage-<region>` として表示されます。

### <a name="the-clouddrive-mount-command"></a>clouddrive mount コマンド

> [!NOTE]
> 新しいファイル共有をマウントすると、新しいユーザー イメージが `$Home` ディレクトリ用に作成されます。 前回の `$Home` イメージは、前のファイル共有に保持されます。

次のパラメーターを指定して `clouddrive mount` コマンドを実行します。

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

詳細情報を表示するには、次のように `clouddrive mount -h` を実行します。

!["clouddrive mount" コマンドを実行する](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>clouddrive のマウント解除
Cloud Shell にマウントされたファイル共有は、いつでもマウントを解除することができます。 Cloud Shell はマウントされたファイル共有を使う必要があるため、次回のセッション時に別のファイル共有を作成してマウントするように求められます。

1. `clouddrive unmount` を実行します。
2. プロンプトに同意して確定します。

作成したファイル共有は、手動で削除するまで存在します。 以降のセッションから、このファイル共有は、Cloud Shell による検索の対象外になります。 詳細情報を表示するには、次のように `clouddrive unmount -h` を実行します。

!["clouddrive unmount" コマンドを実行する](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> このコマンドを実行してもリソースは削除されませんが、Cloud Shell にマップされているリソース グループ、ストレージ アカウント、またはファイル共有を手動で削除すると、ファイル共有内にある `$Home` ディレクトリ ディスク イメージとすべてのファイルは削除されます。 この削除操作は元に戻すことができません。

## <a name="list-clouddrive"></a>`clouddrive` の一覧取得
`clouddrive` としてマウントされているファイル共有を検出するには、`df` コマンドを実行します。 

clouddrive へのファイル パスの URL に、お使いのストレージ アカウント名とファイル共有が表示されます。 たとえば、 `//storageaccountname.file.core.windows.net/filesharename`

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

[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md) <br>
[Azure File Storage について](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[ストレージのタグについて](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
