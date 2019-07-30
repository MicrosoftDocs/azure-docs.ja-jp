---
title: Azure Resource Manager による Load Balancer のサポート | Microsoft Docs
description: Azure Resource Manager で Load Balancer に PowerShell を使用します。 ロード バランサーにテンプレートを使用します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 839b607b7787d51151401737848a46d7b66229dd
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275482"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Azure Load Balancer での Azure Resource Manager のサポートの使用



Azure Resource Manager は、Azure のサービスの優先管理フレームワークです。 Azure Resource Manager ベースの API とツールを使用して Azure Load Balancer を管理できます。

## <a name="concepts"></a>概念

Resource Manager を使用する場合、Azure Load Balancer には次の子リソースが含まれています。

* フロントエンド IP 構成: ロード バランサーには、仮想 IP (VIP) とも呼ばれる 1 つ以上のフロントエンド IP アドレスを含めることができます。 これらの IP アドレスは、受信トラフィック用として機能します。
* バックエンド アドレス プール: 負荷分散先の仮想マシンのネットワーク インターフェイス カード (NIC) に関連付けられた IP アドレスです。
* 負荷分散規則: 規則のプロパティでは、特定のフロントエンド IP とポートの組み合わせを、一連のバックエンド IP アドレスとポートの組み合わせにマップします。 1 つのロード バランサーで複数の負荷分散規則を使用できます。 各規則は、VM に関連付けられた、フロントエンド IP/ポートとバックエンド IP/ポートの組み合わせです。
* プローブ: プローブを使用すると、VM インスタンスの正常性を追跡できます。 正常性プローブが失敗した場合、VM インスタンスは自動的にローテーションから除外されます。
* インバウンド NAT 規則: フロントエンド IP を通過し、バックエンド IP に配信されるインバウンド トラフィックを定義する NAT 規則です。

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>クイック スタート テンプレート

Azure リソース マネージャーでは、宣言型のテンプレートを使用してアプリケーションをプロビジョニングすることができます。 1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。 アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用してアプリケーションを繰り返しデプロイします。

テンプレートには、Virtual Machines、Virtual Network、可用性セット、ネットワーク インターフェイス (NIC)、ストレージ アカウント、Load Balancer、ネットワーク セキュリティ グループ、パブリック IP の定義を含めることができます。 テンプレートを使用して、複雑なアプリケーションに必要なあらゆるものを作成できます。 テンプレート ファイルは、バージョン管理とコラボレーション用のコンテンツ管理システムにチェックインできます。

[テンプレートの詳細](../azure-resource-manager/resource-manager-template-walkthrough.md)

[ネットワーク リソースの詳細](../networking/networking-overview.md)

Azure Load Balancer を使用したクイックスタート テンプレートについては、[GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)を参照してください。GitHub リポジトリには、コミュニティで作成された一連のテンプレートがホストされています。

テンプレートの例を次に示します。

* [ロード バランサー内の 2 台の VM と負荷分散規則](https://go.microsoft.com/fwlink/?LinkId=544799)
* [内部ロード バランサーを使用した VNET 内の 2 台の VM と負荷分散規則](https://go.microsoft.com/fwlink/?LinkId=544800)
* [ロード バランサー内の 2 台の VM と LB の NAT 規則の構成](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>CLI または PowerShell を使用した Azure Load Balancer の設定

Azure Resource Manager コマンドレット、コマンド ライン ツール、REST API を使用する場合は、以下をご覧ください。

* [Azure ネットワークのコマンドレット](https://docs.microsoft.com/powershell/module/az.network#networking) を使用して、ロード バランサーを作成できます。
* [Azure リソース マネージャーを使用したロード バランサーの作成方法](load-balancer-get-started-ilb-arm-ps.md)
* [Azure リソース管理での Azure CLI の使用](../xplat-cli-azure-resource-manager.md)
* [Load Balancer REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>次の手順

[インターネットに接続するロード バランサーの作成を開始](load-balancer-get-started-internet-arm-ps.md)し、特定のロード バランサーのネットワーク トラフィックの動作に適した種類の[分散モード](load-balancer-distribution-mode.md)を構成することもできます。

[ロード バランサーの TCP アイドル タイムアウト設定](load-balancer-tcp-idle-timeout.md)を管理する方法を確認します。 これは、ロード バランサーの背後にあるサーバーに対してアプリケーションの接続を維持する必要がある場合に重要となります。
