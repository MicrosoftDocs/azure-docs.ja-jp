---
title: Application Gateway サービス エンドポイントとの統合 - Azure App Service | Microsoft Docs
description: Application Gateway がサービス エンドポイントで保護された Azure App Service と統合する方法について説明します。
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 58886a8f7dc505a7e68d69eb00b4a2ebd776dd5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209860"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>サービス エンドポイントと Application Gateway の統合
App Service には、Azure Application Gateway との統合において少し異なる構成を必要とする 3 つのバリエーションがあります。 バリエーションには、通常の App Service (マルチテナント、内部ロード バランサー (ILB) App Service Environment (ASE)、外部 ASE とも呼ばれます) が含まれます。 この記事では、App Service (マルチテナント) を使用して構成し、ILB と外部 ASE に関する考慮事項について説明します。

## <a name="integration-with-app-service-multi-tenant"></a>App Service (マルチテナント) との統合
App Service (マルチテナント) には、インターネットに接続するパブリック エンドポイントがあります。 [サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)を使用すると、Azure Virtual Network 内の特定のサブネットからのトラフィックのみを許可し、他のすべてのトラフィックをブロックすることができます。 次のシナリオでは、この機能を使用して、App Service インスタンスが特定の Application Gateway インスタンスからのトラフィックのみを受信できるようにします。

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="図には、インターネットが Azure Virtual Network 内の Application Gateway に流れ、そこからファイアウォール アイコンを経由して App Service 内のアプリのインスタンスに送信されることが示されています。":::

この構成には、App Service と Application Gateway の作成以外に 2 つの部分があります。 最初の部分は、Application Gateway がデプロイされている Virtual Network のサブネットでサービス エンドポイントを有効にすることです。 サービス エンドポイントは、サブネットから App Service に向かうすべてのネットワーク トラフィックに、特定のサブネット ID でタグ付けされるようにします。 2 つ目の部分は、特定の Web アプリのアクセス制限を設定して、この特定のサブネット ID でタグ付けされたトラフィックのみが許可されるようにすることです。 設定に応じて、さまざまなツールを使用して構成できます。

## <a name="using-azure-portal"></a>Azure Portal の使用
Azure portal では、4 つの手順に従ってセットアップのプロビジョニングと構成を行います。 既存のリソースがある場合は、最初の手順を省略できます。
1. [.NET Core のクイック スタート](../quickstart-dotnetcore.md)などの App Service ドキュメントのクイック スタートのいずれかを使用して、App Service を作成します。
2. [ポータルのクイック スタート](../../application-gateway/quick-create-portal.md)を使用して Application Gateway を作成します。この際、[バックエンド ターゲットの追加] セクションはスキップします。
3. [Application Gateway で App Service をバックエンドとして](../../application-gateway/configure-web-app-portal.md)構成します。この際、[アクセスを制限する] セクションはスキップします。
4. 最後に、[サービス エンドポイントを使用してアクセス制限](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule)を作成します。

Application Gateway を通じて App Service にアクセスできるようになりましたが、App Service に直接アクセスしようとすると、Web サイトが停止していることを示す 403 HTTP エラーが表示されます。

![[エラー 403 - 許可されていません] のテキストを示すスクリーンショット。](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
[Resource Manager デプロイ テンプレート][template-app-gateway-app-service-complete]では、完全なシナリオがプロビジョニングされます。 このシナリオでは Application Gateway からのトラフィックのみを受信するため、サービス エンドポイントおよびアクセス制限を使用してロックダウンされた App Service インスタンスが含まれます。 このテンプレートには、簡単にするために、リソース名に追加された多数のスマート既定値と固有の接尾辞が含まれています。 これらをオーバーライドするには、リポジトリを複製するか、テンプレートをダウンロードして編集する必要があります。 

テンプレートを適用するには、テンプレートの説明にある [Azure に配置する] ボタンを使用するか、適切な PowerShell/CLI を使用します。

## <a name="using-azure-command-line-interface"></a>Azure コマンド ライン インターフェイスを使用する
[Azure CLI サンプル](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md)では、Application Gateway からのトラフィックのみを受信するため、サービス エンドポイントおよびアクセス制限を使用してロックダウンされた App Service をプロビジョニングします。 既存の Application Gateway から既存の App Service へのトラフィックのみを分離する必要がある場合は、次のコマンドで十分です。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

既定の構成では、コマンドによって、サブネットのサービス エンドポイント構成と App Service のアクセス制限の両方が設定されます。

## <a name="considerations-for-ilb-ase"></a>ILB ASE に関する考慮事項
ILB ASE はインターネットに公開されず、インスタンスと Application Gateway 間のトラフィックは既に Virtual Network に分離されています。 次の[ハウツーガイド](../environment/integrate-with-application-gateway.md)では、ILB ASE を構成し、Azure portal を使用して Application Gateway と統合します。 

Application Gateway サブネットからのトラフィックのみが ASE に到達するようにする場合は、ASE 内のすべての Web アプリに影響するネットワーク セキュリティ グループ (NSG) を構成できます。 NSG では、サブネットの IP 範囲と、必要に応じてポート (80/443) を指定できます。 ASE が正常に機能するために[必要な NSG ルール](../environment/network-info.md#network-security-groups)をオーバーライドしないようにしてください。

個々の Web アプリにトラフィックを分離するには、サービス エンドポイントが ASE に対して機能しないので、IP ベースのアクセス制限を使用する必要があります。 この IP アドレスは、Application Gateway インスタンスのプライベート IP である必要があります。

## <a name="considerations-for-external-ase"></a>外部 ASE に関する考慮事項
外部 ASE には、マルチテナント App Service のようなパブリックに接続するロード バランサーがあります。 サービス エンドポイントは ASE では機能しないため、Application Gateway インスタンスのパブリック IP を使用して、IP ベースのアクセス制限を使用する必要があります。 Azure portal を使用して外部 ASE を作成するには、[クイック スタート](../environment/create-external-ase.md)に従ってください。

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "完全なシナリオのための Azure Resource Manager テンプレート"

## <a name="considerations-for-kuduscm-site"></a>Kudu/SCM サイトに関する考慮事項
SCM サイト (kudu とも呼ばれます) は、すべての Web アプリに存在する管理者サイトです。 SCM サイトをリバース プロキシすることはできません。また、ほとんどの場合は、個々の IP アドレスまたは特定のサブネットにもロックダウンすることをお勧めします。

メイン サイトと同じアクセス制限を使用する場合は、次のコマンドを使用して設定を継承できます。

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

SCM サイトに個別のアクセス制限を設定する場合は、次に示すように、--scm-site フラグを使用してアクセス制限を追加できます。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>次のステップ
App Service Environment の詳細については、[App Service Environment に関するドキュメント](/azure/app-service/environment)を参照してください。

Web アプリをさらにセキュリティで保護するために、Application Gateway の Web アプリケーション ファイアウォールに関する情報は、[Azure Web アプリケーション ファイアウォールのドキュメント](../../web-application-firewall/ag/ag-overview.md)に記載されています。
