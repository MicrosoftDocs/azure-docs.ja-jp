---
title: "Azure Cloud Shell (プレビュー) のクイック スタート | Microsoft Docs"
description: "Azure Cloud Shell のクイック スタート"
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
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 75676eb0ab784e2adbfd27b170c1dee5599b74ac
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="quickstart-for-using-the-azure-cloud-shell"></a>Azure Cloud Shell を使用するためのクイック スタート

このドキュメントでは、[Azure Portal](https://ms.portal.azure.com/) での Azure Cloud Shell の使い方を詳しく説明します。

## <a name="start-cloud-shell"></a>Cloud Shell の起動
1. Azure Portal 上部のナビゲーションから **Cloud Shell** を起動します。 <br>
![](media/shell-icon.png)
2. ストレージ アカウントと Azure ファイル共有の作成に使用するサブスクリプションを選択します。
3. [Create storage]\(ストレージの作成\) を選択します。

> [!TIP]
> Azure CLI 2.0 では、ユーザーの認証が各セッションで自動的に行われます。

### <a name="set-your-subscription"></a>サブスクリプションの設定
1. 自分が利用できるサブスクリプションを一覧表示します。 <br>
`az account list`
2. 優先するサブスクリプションを設定します。 <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> 設定したサブスクリプションは、`/home/<user>/.azure/azureProfile.json` を使って今後のセッション用に記憶されます。

### <a name="create-a-resource-group"></a>リソース グループの作成
"MyRG" という名前の新しいリソース グループを WestUS に作成します。 <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Linux VM の作成
新しいリソース グループに Ubuntu VM を作成します。 Azure CLI 2.0 によって SSH キーが作成され、そのキーを使って VM が設定されます。 <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> VM の認証に使用される公開キーと秘密キーは、Azure CLI 2.0 によって既定で `/User/.ssh/id_rsa` と `/User/.ssh/id_rsa.pub` に格納されます。 .ssh フォルダーは、接続した Azure ファイル共有の 5 GB イメージに永続化されます。

この VM でのユーザー名が、Cloud Shell で使用されるユーザー名になります ($User@Azure:)。

### <a name="ssh-into-your-linux-vm"></a>Linux VM への SSH 接続
1. Azure Portal の検索バーで VM 名を検索します。
2. [接続] をクリックし、`ssh username@ipaddress` を実行します。

![](media/sshcmd-copy.png)

SSH 接続を確立すると、Ubuntu のウェルカム プロンプトが表示されます。
![](media/ubuntu-welcome.png)

## <a name="cleaning-up"></a>クリーンアップしています 
リソース グループとそこに含まれるリソースを削除するには、 <br>
`az group delete -n MyRG` を実行します。

## <a name="next-steps"></a>次のステップ
[Cloud Shell でのストレージの永続化について](persisting-shell-storage.md) <br>
[Azure CLI 2.0 について](https://docs.microsoft.com/cli/azure/) <br>
[Azure File Storage について](../storage/files/storage-files-introduction.md) <br>
