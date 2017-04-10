---
title: "内部仮想ネットワークで Azure API Management を使用する方法 | Microsoft Docs"
description: "内部仮想ネットワークで Azure API Management を設定し構成する方法について説明します。"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: 
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 456c541be7aadcce494bbd54e97deb6f30d5141b
ms.openlocfilehash: dc9af7e4ef9599886d1be6676f88f71f80c20474


---
# <a name="using-azure-api-management-service-with-internal-virtual-network"></a>内部仮想ネットワークでの Azure API Management サービスの使用
Azure Virtual Networks (VNET) では、API Management はインターネットでアクセスできない API を管理できます。 多数の VPN テクノロジを利用して、接続を行うことができます。また、VNET 内の&2; つの主なモードで API Management をデプロイすることが可能です。
* 外部
* 内部

## <a name="overview"> </a>概要
API Management が内部仮想ネットワーク モードでデプロイされる場合、すべてのサービス エンドポイント (ゲートウェイ、開発者ポータル、パブリッシャー ポータル、ダイレクト管理、Git) は、ユーザーがアクセスを制御している仮想ネットワーク内でのみ表示されます。 いずれのサービス エンドポイントも、パブリック DNS サーバーには登録されません。

API Management を内部モードで使用することにより、次のシナリオを実現できます。
* サイト間または ExpressRoute VPN 接続を使用して、プライベート データセンターでホストされている API へのアクセスを、外部のサード パーティが安全に行うことができるようにします。
* 一般的なゲートウェイを通じてクラウド ベースの API とオンプレミスの API を公開することによって、ハイブリッド クラウドのシナリオを有効にします。
* 単一のゲートウェイ エンドポイントを使用して複数の地理的位置でホストされている API を管理します。 

## <a name="enable-vpn"> </a>内部仮想ネットワークでの API Management の作成
内部仮想ネットワークでの API Management サービスは、内部ロード バランサー (ILB) の背後でホストされています。 ILB の IP アドレスは [RFC1918](http://www.faqs.org/rfcs/rfc1918.html) 範囲にあります。  

### <a name="enable-vnet-connection-using-azure-portal"></a>Azure Portal を使用した VNET 接続の有効化
最初に、[API Management サービスを作成する][Create API Management service]手順に従って、API Management サービスを作成します。 次に、仮想ネットワーク内でデプロイする API Management を設定します。

![内部仮想ネットワークで APIM を設定するためのメニュー][api-management-using-internal-vnet-menu]

デプロイが正常に行われたら、サービスの内部仮想 IP アドレスがダッシュボードに表示されます。

![内部 VNET が構成されている API Management ダッシュボード][api-management-internal-vnet-dashboard]

### <a name="enable-vnet-connection-using-powershell-cmdlets"></a>PowerShell コマンドレットを使用した VNET 接続の有効化
PowerShell コマンドレットを使用して VNET 接続を有効にすることもできます。

* **VNET 内に API Management サービスを作成する**: コマンドレット [New-AzureRmApiManagement](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/new-azurermapimanagement) を使用して、VNET 内に Azure API Management サービスを作成し、このサービスが内部 VNET の種類を使用するように構成します。

* **VNET 内に既存の API Management サービスをデプロイする**: コマンドレット [Update-AzureRmApiManagementDeployment](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.apimanagement/v3.1.0/update-azurermapimanagementdeployment) を使用して、既存の Azure API Management サービスを仮想ネットワーク内に移動し、このサービスが内部 VNET の種類を使用するように構成します。

## <a name="a-nameapim-dns-configurationadns-configuration"></a><a name="apim-dns-configuration"></a>DNS の構成
API Management を外部仮想ネットワーク モードを使用する場合、DNS は Azure によって管理されます。 内部仮想ネットワーク モードの場合は、自身で DNS を管理する必要があります。

> [!NOTE]
> API Management サービスは、IP アドレスに送信される要求をリッスンしません。 サービス エンドポイント (ゲートウェイ、開発者ポータル、パブリッシャー ポータル、ダイレクト管理のエンドポイント、および Git を含む) で構成されているホスト名への要求のみに応答します。

### <a name="access-on-default-host-names"></a>既定のホスト名へのアクセス
"contoso" という名前のパブリック Azure クラウドで API Management サービスを作成すると、次のサービス エンドポイントが既定で構成されます。

>   ゲートウェイ/プロキシ - contoso.azure-api.net

> パブリッシャー ポータルと開発者ポータル - contoso.portal.azure-api.net

> ダイレクト管理エンドポイント - contoso.management.azure-api.net

>   Git - contoso.scm.azure-api.net

これらの API Management サービス エンドポイントにアクセスするために、API Management がデプロイされている仮想ネットワークに接続しているサブネットで Virtual Machine を作成できます。 サービスの内部仮想 IP アドレスを 10.0.0.5 と仮定すると、ホスト ファイルのマッピング (%SystemDrive%\drivers\etc\hosts) を次のように行うことができます。

> 10.0.0.5    contoso.azure-api.net

> 10.0.0.5    contoso.portal.azure-api.net

> 10.0.0.5    contoso.management.azure-api.net

> 10.0.0.5    contoso.scm.azure-api.net

これで、すべてのサービス エンドポイントに、作成した Virtual Machine からアクセスできるようになります。 また仮想ネットワーク内でカスタム DNS サーバーを使用している場合、DNS レコードを作成して、仮想ネットワーク内のどこからでもこれらのエンドポイントにアクセスすることができます。 

### <a name="access-on-custom-domain-names"></a>カスタム ドメイン名へのアクセス
既定のホスト名を持つ API Management サービスにアクセスしたくない場合は、次のようにすべてのサービス エンドポイントのカスタム ドメイン名を設定することができます。

![API Management のカスタム ドメインの設定][api-management-custom-domain-name]

その後、DNS サーバーで A レコードを作成して、仮想ネットワーク内からのみアクセスできるこれらのエンドポイントにアクセスできます。

## <a name="related-content"> </a>関連コンテンツ
* [VNET 内における APIM 設定中の共通ネットワークの構成に関する問題][Common Network Configuration Issues]
* [Virtual Network に関する FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS での A レコード作成](https://msdn.microsoft.com/en-us/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: api-management-get-started.md#create-service-instance
[Common Network Configuration Issues]: api-management-using-with-vnet.md#network-configuration-issues



<!--HONumber=Jan17_HO3-->


