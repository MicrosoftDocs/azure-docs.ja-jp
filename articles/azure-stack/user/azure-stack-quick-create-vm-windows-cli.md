---
title: "Azure CLI を使用して Azure Stack に Windows 仮想マシンを作成する | Microsoft Docs"
description: "Azure CLI を使用して Azure Stack に Windows VM を作成する方法を説明します"
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
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 196bf4351ebd2bf977102571de385edae6f9612b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Azure CLI を使用して Azure Stack に Windows 仮想マシンを作成する

Azure CLI は、コマンドラインで Azure Stack リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して、Azure Stack に Windows Server 2016 仮想マシンを作成する方法について詳しく説明します。 仮想マシンが作成されたら、リモート デスクトップで接続し、IIS をインストールしてから、既定の Web サイトを表示します。 

## <a name="prerequisites"></a>前提条件 

* Azure Stack オペレーターが Azure Stack Marketplace に "Windows Server 2016" のイメージを追加していることを確認します。  

* リソースを作成して管理するため、Azure Stack には Azure CLI の特定のバージョンが必要です。 Azure Stack 用に Azure CLI を構成していない場合は、[Azure CLI のインストールと構成](azure-stack-connect-cli.md)の手順に従います。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループとは、Azure Stack リソースのデプロイ先となって管理される論理コンテナーです。 開発キットまたは Azure Stack 統合システムから、[az group create](/cli/azure/group#create) コマンドを実行してリソース グループを作成します。 このドキュメントではすべての変数に値を割り当てていますが、そのまま使用することも、異なる値を割り当てることもできます。 次の例では、myResourceGroup という名前のリソース グループをローカルの場所に作成します。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#create) コマンドを使用して VM を作成します。 次の例では、myVM という名前の VM を作成します。 この例では、管理ユーザーの名前に Demouser、パスワードに Demouser@123 を使用します。 これらの値を、環境に適した内容に更新します。 これらの値は、仮想マシンに接続する際に必要です。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

VM が作成されたら、出力である *PublicIPAddress* パラメーターを書き留めます。これは、VM にアクセスするために使用します。
 
## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く

Azure Stack にデプロイされている Windows 仮想マシンに対しては、既定で RDP 接続のみが許可されます。 この VM を Web サーバーとして使用する場合は、インターネットからポート 80 を開く必要があります。 [az vm open-port](/cli/azure/vm#open-port) コマンドを使用して、目的のポートを開きます。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続

次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンのパブリック IP アドレスに置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>PowerShell を使用した IIS のインストール

Azure VM にログインしたら、1 行の PowerShell を使用して、IIS をインストールし、Web トラフィックを許可するローカル ファイアウォール規則を有効にできます。 PowerShell プロンプトを開き、次のコマンドを実行します。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS のようこそページの表示

IIS をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して IIS の既定のようこそページを表示することができます。 上の手順で指定したパブリック IP アドレスを使用して既定のページにアクセスします。 

![IIS の既定のサイト](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group#delete) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、簡単な Windows 仮想マシンをデプロイしました。 Azure Stack 仮想マシンの詳細については、「[Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)」に進んでください。
