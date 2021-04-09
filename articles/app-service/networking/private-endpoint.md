---
title: プライベート エンドポイントを使用して非公開で Azure Web アプリに接続する
description: Azure プライベート エンドポイントを使用して非公開で Web アプリに接続する
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/16/2021
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 4de405e07a9ae9d1efb33f2cee3630a1eefdef33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655905"
---
# <a name="using-private-endpoints-for-azure-web-app"></a>Azure Web アプリでプライベート エンドポイントを使用する

> [!IMPORTANT]
> プライベート エンドポイントは、次の App Service プランでホストされている Windows および Linux の Web アプリ (コンテナー化されているかどうかにかかわらず) で使用できます:**Isolated**、**PremiumV2**、**PremiumV3**、**Functions Premium** (Elastic Premium プランとも呼ばれます)。 

Azure Web アプリにプライベート エンドポイントを使用すると、プライベート ネットワーク内のクライアントが Private Link 経由で安全にアプリにアクセスできるようになります。 プライベート エンドポイントは、Azure VNet アドレス空間からの IP アドレスを使用します。 プライベート ネットワーク上のクライアントと Web アプリ間のネットワーク トラフィックは、VNet および Microsoft バックボーン ネットワーク上の Private Link を経由することで、パブリック インターネットにさらされないようにします。

Web アプリにプライベート エンドポイントを使用することで、次のことが可能になります。

- プライベート エンドポイントを構成することにより Web アプリをセキュリティで保護し、公開されないようにする。
- VPN または ExpressRoute のプライベート ピアリングを使用して VNet に接続するオンプレミス ネットワークから Web アプリに安全に接続する。
- VNet からデータが一切流出しないようにしてください。 

単に、VNet と Web アプリ間のセキュリティで保護された接続が必要な場合、サービス エンドポイントが最も簡単なソリューションです。 さらに、Azure ゲートウェイ、リージョンでピアリングされた VNet、またはグローバルにピアリングされた VNet を介してオンプレミスから Web アプリに接続する必要がある場合は、プライベート エンドポイントがソリューションになります。  

詳細については、 [サービス エンドポイント][serviceendpoint]に関するページを参照してください。

## <a name="conceptual-overview"></a>概念の概要

プライベート エンドポイントは、仮想ネットワーク (VNet) 内のサブネットにある Azure Web アプリ用の特別なネットワーク インターフェイス (NIC) です。
Web アプリのプライベート エンドポイントを作成すると、プライベート ネットワーク上のクライアントと Web アプリ間の安全な接続が提供されます。 プライベート エンドポイントには、VNet の IP アドレス範囲から IP アドレスが割り当てられます。
プライベート エンドポイントと Web アプリ間の接続には、セキュリティで保護された [Private Link][privatelink] が使用されます。 プライベート エンドポイントは、Web アプリへの受信フローにのみ使用されます。 送信フローではこのプライベート エンドポイントは使用されませんが、[VNet 統合機能][vnetintegrationfeature]を使用して、別のサブネット内のネットワークに送信フローを挿入できます。

プライベート エンドポイントを接続するサブネットには他のリソースを含めることができます。専用の空のサブネットは必要ありません。
また、プライベート エンドポイントを Web アプリとは別のリージョンにデプロイすることもできます。 

> [!Note]
>VNet 統合機能では、プライベート エンドポイントと同じサブネットを使用することはできません。これは、VNet 統合機能の制限です。

セキュリティの観点から:

- Web アプリに対してプライベート エンドポイントを有効にすると、すべてのパブリック アクセスは無効になります。
- 他のリージョン内の VNet を含め、他の VNet およびサブネットで複数のプライベート エンドポイントを有効にすることができます。
- プライベート エンドポイントの NIC の IP アドレスは動的である必要がありますが、そのプライベート エンドポイントを削除するまでは変わりません。
- プライベート エンドポイントの NIC に NSG を関連付けることはできません。
- プライベート エンドポイントをホストするサブネットには NSG を関連付けることができますが、プライベート エンドポイントに対するネットワーク ポリシーの適用を無効にする必要があります。「[プライベート エンドポイントのネットワーク ポリシーを無効にする][disablesecuritype]」を参照してください。 このため、プライベート エンドポイントへのアクセスを NSG でフィルタに掛けることはできません。
- Web アプリに対してプライベート エンドポイントを有効にすると、Web アプリの[アクセス制限][accessrestrictions]構成は評価されません。
- 宛先が "インターネット" または "Azure サービス" になっているすべての NSG ルールを削除することによって、VNet からのデータ流出リスクをなくすことができます。 Web アプリのプライベート エンドポイントをデプロイするときに、この特定の Web アプリには、このプライベート エンドポイントからのみできます。 別の Web アプリがある場合は、この別の Web アプリの別の専用プライベート エンドポイントをデプロイする必要があります。

Web アプリの Web HTTP ログには、クライアントのソース IP が含まれています。 この機能は、TCP プロキシ プロトコルを使用して実装されており、Web アプリにクライアント IP プロパティを転送します。 詳細については、「[TCP プロキシ v2 を使用した接続情報の取得][tcpproxy]」を参照してください。


  > [!div class="mx-imgBorder"]
  > ![Web アプリのプライベート エンドポイントの全体像](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Web アプリにプライベート エンドポイントを使用する場合は、要求する URL が Web アプリの名前と一致している必要があります。 既定値は mywebappname.azurewebsites.net です。

既定では、プライベート エンドポイントを使用しない場合、Web アプリのパブリック名がクラスターの正規の名前になります。
たとえば、名前解決は次のようになります。

|名前 |Type |値 |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


プライベート エンドポイントをデプロイすると、DNS エントリは正規名 mywebapp.privatelink.azurewebsites.net を指すように更新されます。
たとえば、名前解決は次のようになります。

|名前 |Type |値 |注記 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|<-- このパブリック IP はプライベート エンドポイントではありません。403 エラーを受け取ります|

テスト コンピューターのホスト エントリを変更できることをテストするには、プライベート DNS サーバーまたは Azure DNS プライベート ゾーンを設定する必要があります。
作成する必要がある DNS ゾーンは、**privatelink.azurewebsites.net** です。 A レコードとプライベート エンドポイント IP を使用して、Web アプリのレコードを登録します。
たとえば、名前解決は次のようになります。

|名前 |Type |値 |注記 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|<-- Azure パブリック DNS にこのエントリが作成され、App Service がプライベートリンクを指すようになります。これは Microsoft によって管理されます|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|<-- プライベート エンドポイントの IP アドレスを指すように、DNS システムでこのエントリを管理します|

この DNS 構成の後、既定の名前 mywebappname.azurewebsites.net を使用してプライベートに Web アプリに接続できます。 既定の証明書が *.azurewebsites.net に対して発行されるため、この名前を使用する必要があります。


カスタム DNS 名を使用する必要がある場合は、カスタム名を Web アプリに追加する必要があります。 すべてのカスタム名と同様に、パブリック DNS 解決を使用してカスタム名を検証する必要があります。 詳細については、[カスタム DNS 検証][dnsvalidation]に関するページをご覧ください。

Kudu コンソールまたは Kudu REST API (Azure DevOps セルフホステッド エージェントを使用したデプロイなど) の場合は、Azure DNS プライベート ゾーンまたはカスタム DNS サーバーに 2 つのレコードを作成する必要があります。 

| 名前 | Type | 値 |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金][pricing]」をご覧ください。

## <a name="limitations"></a>制限事項

Elastic Premium プランの Azure Function をプライベート エンドポイントとともに使用する場合、Azure Web ポータルで関数を実行するには、直接ネットワークにアクセスする必要があります。そうでないと、HTTP 403 エラーが発生します。 つまり、Azure Web ポータルから関数を実行するには、お使いのブラウザーで、プライベート エンドポイントに到達できる必要があります。 

最大 100 個のプライベート エンドポイントを特定の Web アプリに接続できます。

Web アプリに対してプライベート エンドポイントが有効になっている場合、リモート デバッグ機能は使用できません。 スロットにコードをデプロイし、そこでリモート デバッグすることをお勧めします。

FTP アクセスは、受信パブリック IP アドレスを介して提供されます。 プライベート エンドポイントでは、Web アプリへの FTP アクセスをサポートしていません。

定期的に Private Link 機能とプライベート エンドポイントの改善を行っています。制限に関する最新情報については、[こちらの記事][pllimitations]を確認してください。

## <a name="next-steps"></a>次のステップ

- ポータルを使用して Web アプリのプライベート エンドポイントをデプロイするには、[ポータルを使用して Web アプリに非公開で接続する方法][howtoguide1]に関するページを参照してください
- Azure CLI を使用して Web アプリのプライベート エンドポイントをデプロイするには、[Azure CLI を使用して Web アプリに非公開で接続する方法][howtoguide2]に関するページを参照してください
- PowerShell を使用して Web アプリのプライベート エンドポイントをデプロイするには、[PowerShell を使用して Web アプリに非公開で接続する方法][howtoguide3]に関するページを参照してください
- Azure テンプレートを使用して Web アプリのプライベート エンドポイントをデプロイするには、[Azure テンプレートを使用して Web アプリに非公開で接続する方法][howtoguide4]に関するページを参照してください
- エンド ツー エンドの例: ARM テンプレートにより、フロントエンド Web アプリを、VNet インジェクションとプライベート エンドポイントを使用するセキュリティで保護されたバックエンド Web アプリに接続する方法については、こちらの[クイックスタート][howtoguide5]を参照してください。
- エンド ツー エンドの例: terraform により、フロントエンド Web アプリを、VNet インジェクションとプライベート エンドポイントを使用するセキュリティで保護されたバックエンド Web アプリに接続する方法については、こちらの[サンプル][howtoguide6]を参照してください。


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/tutorial-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md
