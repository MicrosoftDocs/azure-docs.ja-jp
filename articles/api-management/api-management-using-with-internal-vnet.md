---
title: 内部仮想ネットワークで Azure API Management を使用する方法 | Microsoft Docs
description: 内部仮想ネットワークで Azure API Management をセットアップして構成する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: a5d8a724a0b4dd6899a71187176b9d444e5fe19c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051684"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>内部仮想ネットワークでの Azure API Management サービスの使用
Azure Virtual Networksでは、Azure API Management はインターネットでアクセスできない API を管理できます。 多数の VPN テクノロジを利用して接続できます。 API Management は、次の 2 つの主要モードで仮想ネットワークの内部にデプロイできます。
* 外部
* 内部

API Management が内部仮想ネットワーク モードでデプロイされる場合、すべてのサービス エンドポイント (プロキシ ゲートウェイ、開発者ポータル、ダイレクト管理、および Git) は、自分でアクセスを制御している仮想ネットワーク内でのみ認識されます。 いずれのサービス エンドポイントも、パブリック DNS サーバーには登録されません。

> [!NOTE]
> サービス エンドポイント用の DNS エントリは存在しないため、仮想ネットワーク用の [DNS が構成される](#apim-dns-configuration)まで、これらのエンドポイントにはアクセスできません。

API Management を内部モードで使用することにより、次のシナリオを実現できます。

* サイト間または ExpressRoute VPN 接続を使用することで、プライベート データセンターでホストされている API へのアクセスを、外部のサード パーティが安全に行うことができるようにします。
* 一般的なゲートウェイを通じてクラウド ベースの API とオンプレミスの API を公開することによって、ハイブリッド クラウドのシナリオを有効にします。
* 単一のゲートウェイ エンドポイントを使用することで、複数の地理的な場所でホストされている API を管理します。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

+ **有効な Azure サブスクリプション**。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Azure API Management インスタンス**。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
+ API Management サービスが仮想ネットワークにデプロイされている場合は、[ポートの一覧](./api-management-using-with-vnet.md#required-ports)が使用され、開く必要があります。 

## <a name="enable-vpn"> </a>内部仮想ネットワークでの API Management の作成
内部仮想ネットワークでの API Management サービスは、[内部ロード バランサー (クラシック)](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud) の背後でホストされます。 これは使用可能な唯一のオプションで、変更することはできません。

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Azure ポータルで仮想ネットワーク接続を有効にする

1. [Azure ポータル](https://portal.azure.com/)で Azure API Management インスタンスに移動します。
2. **[仮想ネットワーク]** を選択します。
3. 仮想ネットワーク内に展開される API Management インスタンスを構成します。

    ![内部仮想ネットワーク内に Azure API Management をセットアップするためのメニュー][api-management-using-internal-vnet-menu]

4. **[保存]** を選択します。

デプロイが成功すると、[概要] ブレードに API Management サービスの**プライベート**仮想 IP アドレスと**パブリック**仮想 IP アドレスが表示されるはずです。 **プライベート**仮想 IP アドレスは、`gateway`、`portal`、`management`、および`scm`の各エンドポイントにアクセスできる API Management の委任されたサブネット内からロード バランシングされた IP アドレスです。 **パブリック**仮想 IP アドレスは、ポート 3443 での `management` エンドポイントへのコントロール プレーン トラフィックに**のみ**に使用され、[ApiManagement][ServiceTags] サービス タグにロックダウンすることができます。

![内部仮想ネットワークが構成された API Management ダッシュボード][api-management-internal-vnet-dashboard]

> [!NOTE]
> Azure portal で利用可能なテスト コンソールは、**内部の** VNET でデプロイされたサービスでは機能しません。これは、ゲートウェイ URL がパブリック DNS に登録されていないためです。 代わりに、**開発者ポータル**で提供されるテスト コンソールを使用する必要があります。

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>PowerShell コマンドレットを使用して仮想ネットワーク接続を有効にする

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

仮想ネットワークの接続は、PowerShell コマンドレットを使用して有効にすることもできます。

* 仮想ネットワーク内に API Management サービスを作成する。[New-AzureRmApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) コマンドレット を使用して、仮想ネットワーク内に Azure API Management サービスを作成し、このサービスが内部仮想ネットワークの種類を使用するように構成します。

* 仮想ネットワーク内の API Management サービスの既存のデプロイを更新する。[Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) コマンドレットを使用して、仮想ネットワーク内の既存の API Management サービスを移動し、このサービスが内部仮想ネットワークの種類を使用するように構成します。

## <a name="apim-dns-configuration"></a>DNS の構成
API Management が外部仮想ネットワーク モードの場合、DNS は Azure によって管理されます。 内部仮想ネットワーク モードの場合は、自身でルーティングを管理する必要があります。

> [!NOTE]
> API Management サービスは、IP アドレスから送信される要求をリッスンしません。 サービスは、サービス エンドポイントに構成されたホスト名に対する要求のみに応答します。 これらのエンドポイントには、ゲートウェイ、Azure Portal および開発者ポータル、ダイレクト管理エンドポイント、Git が含まれます。

### <a name="access-on-default-host-names"></a>既定のホスト名でのアクセス
たとえば "contosointernalvnet" という名前の API Management サービスを作成すると、次のサービス エンドポイントが既定で構成されます。

   * ゲートウェイまたはプロキシ: contosointernalvnet.azure-api.net

   * Azure portal と開発者ポータル: contosointernalvnet.portal.azure-api.net

   * ダイレクト管理エンドポイント: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

これらの API Management サービス エンドポイントにアクセスするために、API Management がデプロイされている仮想ネットワークに接続しているサブネットに仮想マシンを作成できます。 サービスの内部仮想 IP アドレスを 10.1.0.5 と仮定すると、ホスト ファイル (%SystemDrive%\drivers\etc\hosts) を次のようにマッピングできます。

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

これで、すべてのサービス エンドポイントに、作成した仮想マシンからアクセスできるようになります。
また、仮想ネットワーク内でカスタム DNS サーバーを使用している場合は、DNS レコードを作成して、仮想ネットワーク内のどこからでもこれらのエンドポイントにアクセスできます。

### <a name="access-on-custom-domain-names"></a>カスタム ドメイン名でのアクセス

1. 既定のホスト名を持つ API Management サービスにアクセスしたくない場合は、次の図に示すように、すべてのサービス エンドポイントのカスタム ドメイン名を設定できます。

   ![API Management のカスタム ドメインの設定][api-management-custom-domain-name]

2. その後、DNS サーバーでレコードを作成して、仮想ネットワーク内からのみアクセスできるこれらのエンドポイントにアクセスできます。

## <a name="routing"> </a> ルーティング

* サブネット範囲から負荷分散された*プライベート*仮想 IP アドレスは予約され、仮想ネットワークから API Management サービス エンドポイントにアクセスするために使用されます。 この*プライベート* IP アドレスは、Azure portal のサービス用の [概要] ブレードで確認できます。 このアドレスを、仮想ネットワークによって使用される DNS サーバーに登録する必要があります。
* 負荷分散された*パブリック* IP アドレス (VIP) は、管理サービス エンドポイントへのアクセスをポート 3443 経由で提供するための予約も行われます。 この*パブリック* IP アドレスは、Azure portal のサービス用の [概要] ブレードで確認できます。 *パブリック* IP アドレスは、ポート 3443 での `management` エンドポイントへのコントロール プレーン トラフィックにのみに使用され、[ApiManagement][ServiceTags] サービス タグにロックダウンすることができます。
* サービス内の各 VM には、サブネット IP 範囲 (DIP) の IP アドレスが割り当てられ、仮想ネットワーク内のリソースにアクセスするために使用されます。 パブリック IP アドレス (VIP) は、仮想ネットワークの外部のリソースにアクセスするために使用されます。 IP 制限リストを使用して仮想ネットワーク内のリソースをセキュリティで保護する場合は、API Management サービスがデプロイされるサブネットの範囲全体に対して、サービスからのアクセスを許可するか制限するように指定する必要があります。
* 負荷分散されたパブリック IP アドレスとプライベート IP アドレスは、Azure portal の [概要] ブレードで確認できます。
* サービスが仮想ネットワークから削除された後、再び追加された場合、パブリックとプライベートに対して割り当てられる IP アドレスが変更される可能性があります。 これが発生した場合は、仮想ネットワーク内の DNS 登録、ルーティング規則、および IP 制限リストの更新が必要な場合があります。

## <a name="related-content"> </a>関連コンテンツ
詳細については、次の記事を参照してください。
* [仮想ネットワーク内での Azure API Management の設定時に発生するネットワーク構成に関する一般的な問題][Common network configuration problems]
* [Virtual Network に関する FAQ](../virtual-network/virtual-networks-faq.md)
* [DNS でのレコードの作成](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

