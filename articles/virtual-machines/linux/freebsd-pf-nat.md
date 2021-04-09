---
title: FreeBSD のパケット フィルターを使用して Azure 内にファイアウォールを作成する
description: FreeBSD の PF を使用して NAT ファイアウォールを Azure にデプロイする方法について説明します。
author: KylieLiang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 673ddf30771f63f559a12dbea47a1f022ea5599a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565922"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>FreeBSD のパケット フィルターを使用してAzure 内にセキュア ファイアウォールを作成する方法
この記事では、一般的な Web サーバー シナリオで、Azure Resource Manager テンプレートを通して FreeBSD の パケット フィルターを使用することで、NAT ファイアウォールをデプロイする方法について説明します。

## <a name="what-is-pf"></a>PF とは何か
PF (パケット フィルター、pf とも表記します) は、BSD ライセンスのステートフルなパケット フィルターであり、ファイアウォール ソフトウェアの中心部です。 PF は急速に進化し、現在は、利用可能なその他のファイアウォールを上回る多様な利点を備えています。 ネットワーク アドレス変換 (NAT) は、最初から PF に含まれており、その後、パケット スケジューラとアクティブ キュー管理が PF に統合され、ALTQ の統合によって PF の構成を通じて構成できるようになっています。 フェールオーバーと冗長性のための pfsync と CARP、セッション認証のための authpf、複雑な FTP プロトコルのファイアウォール処理を容易にするための ftp-proxy などの機能も PF を拡張しています。 要するに、PF は、強力かつ機能豊富なファイアウォールです。 

## <a name="get-started"></a>はじめに
Web サーバー用のクラウドにセキュア ファイアウォールを設定することに関心があるのであれば、今すぐ始めましょう。 Azure Resource Manager テンプレートで使用されているスクリプトを適用して、ネットワーク トポロジを設定することもできます。
Azure Resource Manager テンプレートは、PF を使用して NAT/リダイレクトを実行する 1 台の FreeBSD 仮想マシンと、Nginx Web サーバーがインストールされ構成されている 2 台の FreeBSD 仮想マシンをセットアップします。 NAT/リダイレクト仮想マシンは、2 台の Web サーバーのエグレス トラフィックに対する NAT の実行に加え、HTTP 要求をインターセプトして、ラウンド ロビン方式で 2 つの Web サーバーにリダイレクトします。 VNet はプライベートなルーティング不能 IP アドレス空間 10.0.0.2/24 を使用します。テンプレートのパラメーターは変更することができます。 Azure Resource Manager テンプレートは、VNet 全体のルート テーブルを定義することもできます。これは、宛先 IP アドレスに基づいて Azure の既定のルートをオーバーライドするために使用される個別のルートのコレクションです。 

![NAT インスタンス上のパブリック IP アドレスを示す図。これは、ラウンドロビン方式によって、Nginx Web サーバーをホストする 2 つのバックエンド仮想マシンにリダイレクトされます。](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Azure CLI を使用してデプロイする
最新の [Azure CLI](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしている必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `West US` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

次に、[az deployment group create](/cli/azure/deployment/group) を使用して pf-freebsd-setup テンプレートをデプロイします。 azuredeploy.parameters.json を同じパスにダウンロードし、独自のリソース値 (`adminPassword`、`networkPrefix`、`domainNamePrefix` など) を定義します。 

```azurecli
az deployment group create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

約 5 分後に、情報 (`"provisioningState": "Succeeded"`) が表示されます。 これで、フロントエンド VM (NAT) に対して ssh を実行するか、ブラウザーでフロントエンド VM (NAT) のパブリック IP アドレスまたは FQDN を使用して Nginx Web サーバーにアクセスできます。 次の例は、`myResourceGroup` リソース グループのフロントエンド VM (NAT) に割り当てられている FQDN とパブリック IP アドレスの一覧を示します。 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>次のステップ
Azure 内に独自の NAT を設定することを望んでいますか? 無料だが強力なオープン ソースを使用したいですか? パブリック フォルダーの使用をお勧めします。 pf-freebsd-setup テンプレートを使用することで、一般的な Web サーバー シナリオで FreeBSD の PF を使用してラウンド ロビン方式で負荷分散する NAT ファイアウォールを Azure 内にほんの 5 分で設定できます。 

Azure に提供されている FreeBSD の詳細については、「[Azure の FreeBSD の概要](freebsd-intro-on-azure.md)」を参照してください。

PF の詳細については、[FreeBSD ハンドブック](https://www.freebsd.org/doc/handbook/firewalls-pf.html)または[PF-ユーザーズ ガイド ](https://www.freebsd.org/doc/handbook/firewalls-pf.html)を参照してください。
