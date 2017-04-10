---
title: "Azure CLI のサンプル スクリプト - NLB がインストールされた Windows Server 2016 の作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - NLB がインストールされた Windows Server 2016 の作成"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: ee48cbbb0f044c6f178373501cec81d4b4cdcb3b
ms.lasthandoff: 03/21/2017

---

# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>高可用性仮想マシン間でトラフィックの負荷を分散する

このサンプル スクリプトでは、高可用性と負荷分散が設定された構成で、構成済みの複数の Ubuntu 仮想マシンを実行するために必要なものをすべて作成します。 このスクリプトを実行すると、Azure 可用性セットに接続され、Azure Load Balancer 経由でアクセス可能な仮想マシンが 3 つ作成されます。

必要に応じて、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)の手順に従って Azure CLI をインストールし、`az login` を実行して、Azure との接続を作成します。 また、スクリプトの冒頭にある $AdminPassword 変数を、一意かつパスワードの複雑性要件を満たしたものに変更してください。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows で実行する方法については、[Windows での Azure CLI の実行](../virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-windows-vm-nlb.sh "VM の簡易作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Azure 仮想ネットワークとサブネットを作成します。 |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) | 静的 IP アドレスおよび関連付けられる DNS 名を指定してパブリック IP アドレスを作成します。 |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) | Azure ネットワーク ロード バランサー (NLB) を作成します。 |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) | NLB プローブを作成します。 NLB プローブは、NLB セット内の各 VM の監視に使用します。 アクセス不能になった VM には、トラフィックがルーティングされなくなります。 |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | NLB 規則を作成します。 このサンプルでは、ポート 80 に対する規則を作成します。 HTTP トラフィックが NLB で受信されると、NLB セット内のいずれかの VM のポート 80 にルーティングされます。 |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) | NLB ネットワーク アドレス変換 (NAT) 規則を作成します。  NAT 規則により、NLB のポートを VM のポートにマッピングされます。 このサンプルでは、NLB セット内の各 VM への SSH トラフィックに対する NAT 規則を作成します。  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) | インターネットと仮想マシン間のセキュリティ境界となるネットワーク セキュリティ グループ (NSG) を作成します。 |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | 受信トラフィックを許可する NSG 規則を作成します。 このサンプルでは、SSH トラフィック用にポート 22 を開きます。 |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) | 仮想ネットワーク カードを作成し、作成したカードを仮想ネットワーク、サブネット、NSG に接続します。 |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | 可用性セットを作成します。 可用性セットでは、障害が発生した場合でもセット全体に影響が及ばないように物理リソース全体に仮想マシンを分散させることで、アプリケーションの稼働時間を確保します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびNSG に接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Windows VM のドキュメント](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)のページにあります。

