---
title: App Service 環境によるレイヤード セキュリティ アーキテクチャ - Azure
description: App Service 環境によるレイヤード セキュリティ アーキテクチャの実装
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 5e25de1ad2042ac978c3698165b9d9baba20e816
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274165"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>App Service 環境によるレイヤード セキュリティ アーキテクチャの実装
## <a name="overview"></a>概要
App Service Environment は、Virtual Network にデプロイされる分離されたランタイム環境です。開発者は、セキュリティ アーキテクチャを階層化し、物理的なアプリケーションの層ごとにネットワーク アクセスのレベルに違いを設けることができます。

一般に、API バックエンドは通常のインターネット アクセスから隠し、アップストリームの Web アプリにのみ API の呼び出しを許可することが望ましいと考えられています。  App Service Environment を含んだサブネットに対して[ネットワーク セキュリティ グループ (NSG)][NetworkSecurityGroups] を使用することで、API アプリケーションへのパブリック アクセスを制限することができます。

以下の図は、WebAPI ベースのアプリを App Service Environment にデプロイしたアーキテクチャの例です。  3 つの Web アプリのインスタンスが、独立した 3 つの App Service Environment に別々にデプロイされ、バックエンドにある同じ WebAPI アプリを呼び出します。

![Conceptual Architecture][ConceptualArchitecture] 

"apiase" を含んだサブネットに対するネットワーク セキュリティ グループは、アップストリームの Web アプリから入ってくる呼び出しと、自分自身からの呼び出しを許可します。これを示したのが緑色の正符号です。  一方、インターネットから入ってくる一般的なトラフィックについてはアクセスを明示的に拒否しています。 

以下では、"apiase" を含んだサブネットに対してネットワーク セキュリティ グループを構成する手順について説明します。

## <a name="determining-the-network-behavior"></a>ネットワークの動作の決定
必要なネットワーク セキュリティ ルールを把握するためには、API アプリを含んだ App Service Environment へのアクセスをどのネットワーク クライアントに許可し、どのクライアントをブロックするかを調べる必要があります。

[ネットワーク セキュリティ グループ (NSG)][NetworkSecurityGroups] はサブネットに適用され、そのサブネットの中に App Service Environment がデプロイされます。そのため NSG 内のルールは、App Service Environment 上で動作する**すべて**のアプリに適用されます。  この記事のサンプル アーキテクチャを使用して、"apiase" を含んだサブネットに対してネットワーク セキュリティ グループを適用すると、"apiase" App Service Environment 上で動作するすべてのアプリが同じセキュリティ ルール一式で保護されます。 

* **アップストリームの呼び出し元の送信 IP アドレスを判断する:** アップストリームの呼び出し元の IP アドレスとは何ですか。  これらのアドレスは NSG で明示的にアクセスを許可する必要があります。  App Service Environment 間の呼び出しは "インターネット" を介した呼び出しと見なされるので、3 つのアップストリーム App Service Environment にそれぞれ割り当てられた送信 IP アドレスを、"apiase" サブネットの NSG でアクセスを許可する必要があります。   App Service Environment で実行されるアプリが送信に使う IP アドレスを調べる方法については、[ネットワーク アーキテクチャ][NetworkArchitecture]の概要の記事をご覧ください。
* **バックエンド API アプリは自己呼び出しを行うか。**  バックエンド アプリケーションが自己呼び出しを行うかどうかは、見逃しやすいポイントです。  App Service Environment のバックエンド API アプリケーションに自己呼び出しが伴う場合、それも "インターネット" を介した呼び出しと見なされます。  サンプル アーキテクチャのケースでは、"apiase" App Service Environment の送信 IP アドレスについてもアクセスを許可する必要があります。

## <a name="setting-up-the-network-security-group"></a>ネットワーク セキュリティ グループの設定
一連の送信 IP アドレスが確認できたら、今度はネットワーク セキュリティ グループを構築します。  ネットワーク セキュリティ グループは、Resource Manager ベースの仮想ネットワークとクラシック仮想ネットワークのどちらにも作成できます。  次の例は、PowerShell を使用してクラシック仮想ネットワーク上に NSG を作成して構成する方法を示しています。

このサンプル アーキテクチャの環境は、米国中南部に置かれているため、そのリージョンに空の NSG を作成します。

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

最初に、Azure の管理インフラストラクチャに使用する明示的な許可ルールを追加します (App Service Environment の[受信トラフィック][InboundTraffic]の記事を参照)。

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

次に、1 つ目のアップストリーム App Service Environment ("fe1ase") からの HTTP 呼び出しと HTTPS 呼び出しを許可する 2 つのルールを追加します。

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

2 つ目と 3 つ目のアップストリーム App Service Environment ("fe2ase" と "fe3ase") についても同じ作業を行います。

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

最後に、バックエンド API の App Service Environment が自己呼び出しを行えるよう、その送信 IP アドレスにアクセス権を付与します。

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

すべての NSG には既定のルールのセットがあり、インターネットから入ってくるアクセスを既定でブロックするため、他のネットワーク セキュリティ ルールは必要ありません。

以下に示したのは、このネットワーク セキュリティ グループに含まれる全ルールの一覧です。  最後のルール (ハイライト部分) に注目してください。明示的にアクセスが許可されている呼び出し元以外はすべて、このルールによって受信アクセスがブロックされます。

![NSG Configuration][NSGConfiguration] 

最後に、"apiase" App Service Environment を含んだサブネットにこの NSG を適用する必要があります。

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

このサブネットに NSG を適用すると、アップストリームにある 3 つの App Service Environment と、API バックエンドを含んだ 1 つの App Service Environment とにのみ、"apiase" 環境に対する呼び出しが許可されるようになります。

## <a name="additional-links-and-information"></a>その他のリンクおよび情報
[ネットワーク セキュリティ グループ](../../virtual-network/security-overview.md)に関する情報。

[送信 IP アドレス][NetworkArchitecture]と App Service Environment について。

App Service Environment で使用される[ネットワーク ポート][InboundTraffic]。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
