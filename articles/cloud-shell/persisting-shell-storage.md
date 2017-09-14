---
title: "Azure Cloud Shell (プレビュー) でファイルを永続化する | Microsoft Docs"
description: "Azure Cloud Shell でファイルを永続化する方法についてのチュートリアルです。"
services: 
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
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 61a8bfcf3704f361432400771d8fcc8b81927b53
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud Shell でファイルを永続化する
Cloud Shell では Azure File ストレージを利用してセッション間でファイルを維持します。

## <a name="set-up-a-clouddrive-file-share"></a>`clouddrive` ファイル共有を設定する
Cloud Shell の初回起動時に、セッション間でファイルを維持するために新しいまたは既存のファイル共有を関連付けることを求められます。

### <a name="create-new-storage"></a>新しいストレージの作成

基本設定を使用し、サブスクリプションのみを選択すると、Cloud Shell では、最寄りのサポートされるリージョンに 3 つのリソースが自動的に作成されます。
* リソース グループ: `cloud-shell-storage-<region>`
* ストレージ アカウント: `cs<uniqueGuid>`
* ファイル共有: `cs-<user>-<domain>-com-<uniqueGuid>`

![サブスクリプション設定](media/basic-storage.png)

ファイル共有は `clouddrive` ディレクトリに `$Home` という名前でマウントされます。 自動的に作成され、`$Home` ディレクトリの更新と永続化を自動的に行う 5 GB のイメージを保存する場合にも、ファイル共有が使用されます。 これは 1 度限りのアクションであり、ファイル共有は以降のセッションで自動的にマウントされます。

### <a name="use-existing-resources"></a>既存のリソースの使用

詳細設定オプションを使用すると、既存のリソースを関連付けることができます。 ストレージのセットアップを促す画面が表示されたら、**[詳細設定の表示]** を選択して、追加オプションを表示します。 既存のファイル共有には、`$Home` ディレクトリの永続化に使用できる 5 GB のユーザー イメージが割り当てられます。 ドロップダウン メニューは、ユーザーの Cloud Shell リージョン、ローカル冗長ストレージ アカウント、geo 冗長ストレージ アカウントに合わせて絞り込まれます。

![リソース グループ設定](media/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure リソース ポリシーによるリソース作成の制限
Cloud Shell で作成したストレージ アカウントは `ms-resource-usage:azure-cloud-shell` でタグ付けされます。 Cloud Shell からストレージ アカウントを作成できないようにするには、この固有のタグによってトリガーされる[タグの Azure リソース ポリシー](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)を作成します。

## <a name="how-cloud-shell-works"></a>Cloud Shell のしくみ
Cloud Shell は、次の両方の方法を使用してファイルを永続化します。
* `$Home` ディレクトリのディスク イメージを作成してディレクトリ内のすべてのコンテンツを永続化します。 このディスク イメージは、指定されたファイル共有の `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` に `acc_<User>.img` として保存され、変更が自動的に反映されます。

* ファイル共有を直接操作できるように、指定されたファイル共有を `$Home` ディレクトリに `clouddrive` としてマウントします。 `/Home/<User>/clouddrive` は `fileshare.storage.windows.net/fileshare` にマッピングされます。
 
> [!NOTE]
> SSH キーなど、`$Home` ディレクトリ内のすべてのファイルが、マウントされたファイル共有に格納されたユーザー ディスク イメージに永続化されます。 `$Home` ディレクトリおよびマウントされたファイル共有への情報の保存時に、ベスト プラクティスを適用してください。

## <a name="use-the-clouddrive-command"></a>`clouddrive` コマンドを使用する
Cloud Shell では、`clouddrive` というコマンドを実行できます。このコマンドを使用すると、Cloud Shell にマウントされているファイル共有を手動で更新できます。
!["clouddrive" コマンドを実行する](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>新しい `clouddrive` をマウントする

### <a name="prerequisites-for-manual-mounting"></a>手動マウントの前提条件
`clouddrive mount` コマンドを使用して、Cloud Shell と関連付けられているファイル共有を更新することができます。

既存のファイル共有をマウントする場合、ストレージ アカウントは次の条件を満たす必要があります。
* ファイル共有をサポートするローカル冗長ストレージまたは geo 冗長ストレージ。
* 自分の割り当てリージョンに存在すること。 オンボーディング時に、自分に割り当てられているリージョンが、リソース グループ名 `cloud-shell-storage-<region>` として表示されます。

### <a name="supported-storage-regions"></a>サポートされているストレージ リージョン
マウント対象の Cloud Shell マシンと同じリージョンに Azure Files が存在していることが必要です。 Cloud Shell クラスターは、現在は次の各リージョンに存在します。
|領域|リージョン|
|---|---|
|アメリカ|米国東部、米国中南部、米国西部|
|ヨーロッパ|北ヨーロッパ、西ヨーロッパ|
|アジア太平洋|インド中部、東南アジア|

### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` コマンド

> [!NOTE]
> 新しいファイル共有をマウントする場合、前の `$Home` イメージは前のファイル共有に保持されるので、`$Home` ディレクトリには新しいユーザー イメージが作成されます。

次のパラメーターを指定して `clouddrive mount` コマンドを実行します。

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

詳細情報を表示するには、次のように `clouddrive mount -h` を実行します。

!["clouddrive mount" コマンドを実行する](media/mount-h.png)

## <a name="unmount-clouddrive"></a>`clouddrive` のマウントを解除する
Cloud Shell にマウントされたファイル共有は、いつでもマウントを解除することができます。 ファイル共有のマウントを解除するとすぐに、次のセッションの前に新しいファイル共有をマウントするように求められます。

Cloud Shell からファイル共有を削除するには:
1. `clouddrive unmount` を実行します。
2. プロンプトに同意して確定します。

作成したファイル共有は、手動で削除するまで存在します。 以降のセッションから、このファイル共有は、Cloud Shell による検索の対象外になります。

詳細情報を表示するには、次のように `clouddrive unmount -h` を実行します。

!["clouddrive unmount" コマンドを実行する](media/unmount-h.png)

> [!WARNING]
> このコマンドを実行してもリソースは一切削除されません。 Cloud Shell にマッピングされたリソース グループ、ストレージ アカウント、ファイル共有のいずれかを手動で削除すると、ファイル共有内にある `$Home` ディレクトリ イメージと他のすべてのファイルが永久に削除されます。 この削除操作は元に戻すことができません。

## <a name="list-clouddrive-file-shares"></a>`clouddrive` ファイル共有を一覧表示する
`clouddrive` としてマウントされているファイル共有を検出するには、次の `df` コマンドを実行します。 

clouddrive へのファイル パスの URL に、お使いのストレージ アカウント名とファイル共有が表示されます。 たとえば、 `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                               1K-blocks     Used Available Use% Mounted on
overlay                                                   30428648 15585636  14826628  52% /
tmpfs                                                       986704        0    986704   0% /dev
tmpfs                                                       986704        0    986704   0% /sys/fs/cgroup
/dev/sda1                                                 30428648 15585636  14826628  52% /etc/hosts
shm                                                          65536        0     65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName        6291456  5242944   1048512  84% /usr/justin/clouddrive
/dev/loop0                                                 5160576   601652   4296780  13% /home/justin
```

## <a name="transfer-local-files-to-cloud-shell"></a>Cloud Shell にローカル ファイルを転送する
`clouddrive` ディレクトリは、Azure Portal ストレージ ブレードと同期します。 このブレードを使用して、ファイル共有との間でローカル ファイルを移動できます。 Cloud Shell 内からファイルを更新すると、ブレードを更新するときにファイル ストレージ GUI に反映されます。

### <a name="download-files"></a>ファイルをダウンロードする

![ローカル ファイルの一覧](media/download.png)
1. Azure Portal で、マウントされているファイル共有に移動します。
2. 対象のファイルを選択します。
3. **[ダウンロード]** ボタンを選択します。

### <a name="upload-files"></a>ファイルのアップロード

![更新されるローカル ファイル](media/upload.png)
1. マウントされているファイル共有に移動します。
2. **[アップロード]** ボタンを選択します。
3. アップロードする 1 つまたは複数のファイルを選択します。
4. アップロードを確認します。

Cloud Shell の `clouddrive` ディレクトリでファイルにアクセスできるようになったことがわかります。

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md) <br>
[Azure File Storage について](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[ストレージのタグについて](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

