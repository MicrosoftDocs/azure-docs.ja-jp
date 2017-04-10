---
title: "Azure API Management で仮想ネットワークを使用する方法"
description: "Azure API Management で仮想ネットワークへの接続を設定して Web サービスにアクセスする方法について説明します。"
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 43618268aa0b234d271dcee7f95dfad2f2a1bb2b
ms.lasthandoff: 03/18/2017


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Azure API Management で仮想ネットワークを使用する方法
Azure Virtual Network (VNET) を使用すると、任意の Azure リソースをインターネット以外のルーティング可能なネットワークに配置し、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用して、オンプレミスのネットワークに接続できます。 Azure Virtual Network の詳細については、まず [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページに記載されている情報をご覧ください。

Azure API Management は、仮想ネットワーク (VNET) の内部でデプロイできるため、ネットワーク内でバックエンド サービスにアクセスできます。 開発者ポータルと API ゲートウェイは、インターネットから、または仮想ネットワーク内でのみアクセスできるように構成可能です。

> [!NOTE]
> Azure API Management では、従来型および Azure Resource Manager の両方の VNet がサポートされています。
>
>

## <a name="enable-vpn"> </a>VNET 接続の有効化
> [!NOTE]
> VNET 接続は、**Premium** レベルと **Developer** レベルで利用できます。 レベルを切り替えるには、Azure Portal で API Management サービスを開き、**[スケールと料金]** タブを開きます。 **[価格レベル]** セクションで、Premium または Developer レベルを選択し、[保存] をクリックします。
>

VNET 接続を有効にするには、Azure Portal で API Management サービスを開き、**[仮想ネットワーク]** ページに切り替えます。

![API Management の [仮想ネットワーク] メニュー][api-management-using-vnet-menu]

目的のアクセスの種類を選択します。

* **外部**: API Management のゲートウェイと開発者ポータルには、パブリック インターネットから外部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

![パブリック ピアリング][api-management-vnet-public]

* **内部**: API Management のゲートウェイと開発者ポータルには、仮想ネットワークから内部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

![プライベート ピアリング][api-management-vnet-private]

API Management サービスがプロビジョニングされているすべてのリージョンの一覧が表示されます。 すべてのリージョンについて、VNET とサブネットを選択します。 この一覧には、構成しているリージョンで設定された Azure サブスクリプションで使用できる従来型および Resource Manager の仮想ネットワークが含まれます。

> [!NOTE]
> 上の図の**サービス エンドポイント**には、ゲートウェイ/プロキシ、パブリッシャー ポータル、開発者ポータル、GIT、およびダイレクト管理エンドポイントが含まれています。
> 上の図の**管理エンドポイント**は、Azure Portal と Powershell を使用して構成を管理するためにサービスでホストされるエンドポイントです。
> さらに、図にはさまざまなエンドポイントの IP アドレスが示されていますが、API Management サービスは、構成済みのホスト名で**のみ**で応答することに注意してください。

> [!IMPORTANT]
> Azure API Management インスタンスを Resource Manager VNET にデプロイする場合、サービスは Azure API Management インスタンス以外のリソースを含まない専用サブネットにある必要があります。 他のリソースが含まれる Resource Manager VNET サブネットに Azure API Management インスタンスをデプロイしようとすると、そのデプロイは失敗します。
>
>

![VPN の選択][api-management-setup-vpn-select]

画面の上部にある **[保存]** をクリックします。

> [!NOTE]
> API Management インスタンスの VIP アドレスは VNET を有効または無効にするたびに変更されます。  
> また、VIP アドレスは、API Management が**外部**から**内部**に、またはその逆に移動された場合にも変更されます。
>


> [!IMPORTANT]
> VNET から API Management を削除するか、VNET にデプロイされる API Management を変更した場合、それまで使用されていた VNET が最大 4 時間ロックされる可能性があります。 この期間中は、VNET の削除も、新しいリソースのデプロイも実行できません。

## <a name="enable-vnet-powershell"> </a>PowerShell コマンドレットを使用して VNET 接続を有効にする
PowerShell コマンドレットを使用して VNET 接続を有効にすることもできます。

* **VNET 内に API Management サービスを作成する**: コマンドレット [New-AzureRmApiManagement](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/new-azurermapimanagement) を使用して、VNET 内に Azure API Management サービスを作成します。

* **VNET 内に既存の API Management サービスをデプロイする**: コマンドレット [Update-AzureRmApiManagementDeployment](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/update-azurermapimanagementdeployment) を使用して、既存の Azure API Management サービスを Virtual Network 内に移動します。

## <a name="connect-vnet"> </a>仮想ネットワーク内でホストされる Web サービスに接続する
API Management サービスが VNET に接続された後で VNET 内のバックエンド サービスにアクセスする方法は、パブリック サービスにアクセスする方法と同じです。 単に、新しい API を作成するときや既存の API を編集するときに Web サービスのローカル IP アドレスまたはホスト名 (VNET に対して DNS サーバーが構成されている場合) を **[Web サービスの URL]** ボックスに入力するだけです。

![VPN からの API の追加][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>ネットワーク構成に関する一般的な問題
API Management サービスを Virtual Network にデプロイするときに発生する可能性のある一般的な誤った構成の一覧を以下に示します。

* **カスタム DNS サーバーのセットアップ**: API Management サービスは、複数の Azure サービスに依存します。 カスタム DNS サーバーを使用して VNET で API Management をホストする場合、その DNS サーバーはこれらの Azure サービスのホスト名を解決する必要があります。 カスタム DNS のセットアップについては、 [こちらの](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) ガイダンスに従ってください。 下にあるポートの表とその他のネットワーク要件を参照してください。

> [!IMPORTANT]
> VNET でカスタム DNS サーバーを使用する場合は、API Management サービスをデプロイする**前**にサーバーをセットアップすることをお勧めします。 これを行わなかった場合は、新しい DNS サーバーの設定を認識するために、サービスをホストしている CloudService を再起動する必要があります。
>

* **API Management に必要なポート**: API Management がデプロイされるサブネットへの受信トラフィックと送信トラフィックは[ネットワーク セキュリティ グループ][Network Security Group]を使用して制御できます。 これらのポートのいずれかが利用できない場合、API Management は正しく動作しない可能性があり、アクセス不能になる場合があります。 VNET で API Management を使用した場合の不正な構成に関するその他の一般的な問題として、これらの 1 つまたは複数のポートがブロックされていることが挙げられます。

API Management サービス インスタンスが VNET でホストされている場合は、次の表のポートが使用されます。

| ソース / ターゲット ポート | 方向 | トランスポート プロトコル | 目的 | ソース / ターゲット | アクセスの種類 |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |受信 |TCP |API Management へのクライアント通信 |INTERNET / VIRTUAL_NETWORK |外部 |
| * / 3443 |受信 |TCP |Azure Portal と Powershell 用の管理エンドポイント |INTERNET / VIRTUAL_NETWORK |外部 / 内部 |
| * / 80, 443 |送信 |TCP |Azure Storage および Azure Service Bus への依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| * / 1433 |送信 |TCP |Azure SQL への依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| * / 11000 - 11999 |送信 |TCP |Azure SQL V12 との依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| * / 14000 - 14999 |送信 |TCP |Azure SQL V12 との依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| * / 9350 - 9354 |送信 |TCP |Service Bus への依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| * / 5671 |送信 |AMQP |Event Hub へのログ記録ポリシーのための依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| 6381 - 6383 / 6381 - 6383 |受信および送信 |UDP |Redis Cache への依存関係 |VIRTUAL_NETWORK / VIRTUAL_NETWORK |外部 / 内部 |-
| * / 445 |送信 |TCP |GIT のための Azure ファイル共有への依存関係 |VIRTUAL_NETWORK / INTERNET |外部 / 内部 |
| * / * | 受信 |TCP |Azure インフラストラクチャの Load Balancer | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK |外部 / 内部 |

* **SSL 機能**: SSL 証明書チェーンの構築と検証を有効にするには、API Management サービスに ocsp.msocsp.com、mscrl.microsoft.com、および crl.microsoft.com に対する送信ネットワーク接続が必要です。

* **Express Route セットアップ**: 顧客の一般的な構成として、発信インターネット トラフィックを強制的にオンプレミスにフローさせる独自の既定のルート (0.0.0.0/0) を定義します。 このトラフィック フローでは、Azure API Management を使用した接続は必ず切断されます。これは、発信トラフィックがオンプレミスでブロックされるか、さまざまな Azure エンドポイントで有効ではなくなった、認識できないアドレス セットに NAT 処理されることが原因です。 解決策は、Azure API Management を含むサブネット上で 1 つ (以上) のユーザー定義ルート ([UDR][UDRs]) を定義することです。 UDR は、既定のルートに優先するサブネット固有のルートを定義します。
  可能であれば、次の構成を使用することをお勧めします。
 * ExpressRoute 構成は 0.0.0.0/0 をアドバタイズし、既定でオンプレミスのすべての発信トラフィックを強制的にトンネリングします。
 * Azure API Management を含むサブネットに適用される UDR では、次ホップの種類がインターネットである 0.0.0.0/0 を定義します。
 これらの手順を組み合わせた結果として、サブネット レベル UDR は ExpressRoute 強制トンネリングよりも優先されるので、Azure API Management からの発信インターネット アクセスを確保できます。

>[!WARNING]  
>Azure API Management は、**パブリック ピアリング パスからプライベート ピアリング パスにルートを正しくクロスアドバタイズしていない** ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、大規模な Microsoft Azure の IP アドレス範囲について Microsoft からルート アドバタイズを受信します。 これらのアドレス範囲がプライベート ピアリング パスで誤ってクロスアドバタイズされている場合、Azure API Management インスタンスのサブネットからのすべての発信ネットワーク パケットは、誤って顧客のオンプレミス ネットワーク インフラストラクチャに強制的にトンネリングされます。 このネットワーク フローでは、Azure API Management が機能しません。 この問題を解決するには、パブリック ピアリング パスからプライベート ピアリング パスへのルートのクロスアドバタイズを停止します。

## <a name="limitations"> </a>制限事項
* API Management インスタンスが含まれるサブネットには、その他の Azure リソースの種類を含めることはできません。
* サブネットと API Management サービスは、同じサブスクリプション内になければなりません。
* API Management インスタンスが含まれるサブネットは、サブスクリプション間で移動できません。
* 内部仮想ネットワークを使用する場合、[RFC 1918](https://tools.ietf.org/html/rfc1918) に記載されている範囲の IP アドレスのみが使用可能であり、パブリック IP アドレスを提供することはできません。
* 内部仮想ネットワークが構成された複数リージョンの API Management デプロイでは、DNS を所有するユーザーが分散負荷の管理を担当します。


## <a name="related-content"> </a>関連コンテンツ
* [VPN Gateway を使用して Virtual Network をバックエンドに接続する](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-ipsecike-vpn-tunnel)
* [異なるデプロイ モデルの Virtual Network を PowerShell を使用して接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Azure API Management で API Inspector を使用して呼び出しをトレースする方法](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md

