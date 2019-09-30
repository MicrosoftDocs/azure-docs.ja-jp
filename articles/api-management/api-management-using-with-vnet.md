---
title: Azure API Management で仮想ネットワークを使用する方法
description: Azure API Management で仮想ネットワークへの接続を設定して Web サービスにアクセスする方法について説明します。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/09/2019
ms.author: apimpm
ms.openlocfilehash: 44fd3f0381c25f2fe0a3e9dc4cf5b8c685486ae0
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861147"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Azure API Management で仮想ネットワークを使用する方法
Azure Virtual Network (VNET) を使用すると、任意の Azure リソースをインターネット以外のルーティング可能なネットワークに配置し、アクセスを制御できます。 これらのネットワークは、さまざまな VPN テクノロジを使用して、オンプレミスのネットワークに接続できます。 Azure Virtual Network の詳細については、まず[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関する記事を参照してください。

Azure API Management は、仮想ネットワーク (VNET) の内部でデプロイできるため、ネットワーク内でバックエンド サービスにアクセスできます。 開発者ポータルと API ゲートウェイは、インターネットから、または仮想ネットワーク内でのみアクセスできるように構成可能です。

> [!NOTE]
> API インポート ドキュメントの URL は、パブリックにアクセス可能なインターネット アドレスでホストされている必要があります。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

## <a name="enable-vpn"> </a>VNET 接続の有効化

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Azure ポータルを使用して VNET 接続を有効にする

1. [Azure ポータル](https://portal.azure.com/)で、APIM インスタンスに移動します。
2. **[仮想ネットワーク]** を選択します。
3. 仮想ネットワーク内にデプロイされる API Management インスタンスを構成します。

    ![API Management の [仮想ネットワーク] メニュー][api-management-using-vnet-menu]
4. 目的のアクセスの種類を選択します。

   * **オフ**: これは既定値です。 API Management は仮想ネットワークにデプロイされません。

   * **外部**: API Management のゲートウェイと開発者ポータルには、パブリック インターネットから外部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

     ![パブリック ピアリング][api-management-vnet-public]

   * **内部**: API Management のゲートウェイと開発者ポータルには、仮想ネットワークから内部ロード バランサーを使用してアクセスできます。 ゲートウェイは、仮想ネットワーク内のリソースにアクセスできます。

     ![プライベート ピアリング][api-management-vnet-private]

     API Management サービスがプロビジョニングされているすべてのリージョンの一覧が表示されます。 すべてのリージョンについて、VNET とサブネットを選択します。 この一覧には、構成しているリージョンで設定された Azure サブスクリプションで使用できる従来型および Resource Manager の仮想ネットワークが含まれます。

     > [!IMPORTANT]
     > Azure API Management インスタンスを Resource Manager VNET にデプロイする場合、サービスは Azure API Management インスタンス以外のリソースを含まない専用サブネットにある必要があります。 他のリソースが含まれる Resource Manager VNET サブネットに Azure API Management インスタンスをデプロイしようとすると、そのデプロイは失敗します。
     >

     ![VPN の選択][api-management-setup-vpn-select]

5. 上部のナビゲーション バーの **[保存]** をクリックします。
6. 上部のナビゲーション バーの **[ネットワーク構成の適用]** をクリックします。

> [!NOTE]
> API Management インスタンスの VIP アドレスは VNET を有効または無効にするたびに変更されます。
> また、VIP アドレスは、API Management が**外部**から**内部**に、またはその逆に移動された場合にも変更されます。
>

> [!IMPORTANT]
> VNET から API Management を削除するか、VNET にデプロイされる API Management を変更した場合、それまで使用されていた VNET が最大 6 時間ロックされる可能性があります。 この期間中は、VNET の削除も、新しいリソースのデプロイも実行できません。 この動作は、api バージョン 2018-01-01 以前を使用しているクライアントに当てはまります。 api バージョン 2019-01-01 以降を使用しているクライアントは、関連付けられている API Management サービスが削除されるとすぐに VNET が解放されます。

## <a name="enable-vnet-powershell"> </a>PowerShell コマンドレットを使用して VNET 接続を有効にする
PowerShell コマンドレットを使用して VNET 接続を有効にすることもできます。

* **VNET 内に API Management サービスを作成する**:コマンドレット [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) を使用して、VNET 内に Azure API Management サービスを作成します。

* **VNET 内に既存の API Management サービスをデプロイする**:コマンドレット [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) を使用して、既存の Azure API Management サービスを仮想ネットワーク内に移動します。

## <a name="connect-vnet"> </a>仮想ネットワーク内でホストされる Web サービスに接続する
API Management サービスが VNET に接続された後で VNET 内のバックエンド サービスにアクセスする方法は、パブリック サービスにアクセスする方法と同じです。 単に、新しい API を作成するときや既存の API を編集するときに Web サービスのローカル IP アドレスまたはホスト名 (VNET に対して DNS サーバーが構成されている場合) を **[Web サービスの URL]** ボックスに入力するだけです。

![VPN からの API の追加][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>ネットワーク構成に関する一般的な問題
API Management サービスを Virtual Network にデプロイするときに発生する可能性のある一般的な誤った構成の一覧を以下に示します。

* **カスタム DNS サーバーのセットアップ**:API Management サービスは、複数の Azure サービスに依存します。 カスタム DNS サーバーを使用して VNET で API Management をホストする場合、その DNS サーバーはこれらの Azure サービスのホスト名を解決する必要があります。 カスタム DNS のセットアップについては、 [こちらの](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) ガイダンスに従ってください。 下にあるポートの表とその他のネットワーク要件を参照してください。

> [!IMPORTANT]
> VNET でカスタム DNS サーバーを使用する予定の場合は、API Management サービスをデプロイする**前**にサーバーをセットアップします。 それ以外の場合、[ネットワーク構成の処理の適用](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates)を実行して DNS サーバーを変更するたびに API Management サービスを更新する必要があります。

* **API Management に必要なポート**:API Management がデプロイされるサブネットへの受信トラフィックと送信トラフィックは[ネットワーク セキュリティ グループ][Network Security Group]を使用して制御できます。 これらのポートのいずれかが利用できない場合、API Management は正しく動作しない可能性があり、アクセス不能になる場合があります。 VNET で API Management を使用した場合の不正な構成に関するその他の一般的な問題として、これらの 1 つまたは複数のポートがブロックされていることが挙げられます。

<a name="required-ports"> </a> API Management サービス インスタンスが VNET でホストされている場合は、次の表のポートが使用されます。

| ソース / ターゲット ポート | Direction          | トランスポート プロトコル |   [サービス タグ](../virtual-network/security-overview.md#service-tags) <br> ソース / ターゲット   | 目的 ( * )                                                 | 仮想ネットワークの種類 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | 受信            | TCP                | INTERNET / VIRTUAL_NETWORK            | API Management へのクライアント通信                      | 外部             |
| * / 3443                     | 受信            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure Portal と Powershell 用の管理エンドポイント         | 外部 / 内部  |
| * / 80, 443                  | 送信           | TCP                | VIRTUAL_NETWORK / Storage             | **Azure Storage への依存関係**                             | 外部 / 内部  |
| * / 80, 443                  | 送信           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Azure Active Directory (該当する場合)                   | 外部 / 内部  |
| * / 1433                     | 送信           | TCP                | VIRTUAL_NETWORK / SQL                 | **Azure SQL エンドポイントへのアクセス**                           | 外部 / 内部  |
| * / 5672                     | 送信           | TCP                | VIRTUAL_NETWORK / EventHub            | Event Hub へのログ ポリシーおよび監視エージェントの依存関係 | 外部 / 内部  |
| * / 445                      | 送信           | TCP                | VIRTUAL_NETWORK / Storage             | GIT のための Azure ファイル共有への依存関係                      | 外部 / 内部  |
| * / 1886                     | 送信           | TCP                | VIRTUAL_NETWORK / INTERNET            | 正常性の状態を Resource Health に公開するために必要          | 外部 / 内部  |
| * / 443                     | 送信           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 診断ログとメトリックの公開                        | 外部 / 内部  |
| * / 25                       | 送信           | TCP                | VIRTUAL_NETWORK / INTERNET            | 電子メールを送信するために SMTP リレーに接続する                    | 外部 / 内部  |
| * / 587                      | 送信           | TCP                | VIRTUAL_NETWORK / INTERNET            | 電子メールを送信するために SMTP リレーに接続する                    | 外部 / 内部  |
| * / 25028                    | 送信           | TCP                | VIRTUAL_NETWORK / INTERNET            | 電子メールを送信するために SMTP リレーに接続する                    | 外部 / 内部  |
| * / 6381 - 6383              | 受信および送信 | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | ロール インスタンス間での Azure Cache for Redis インスタンスへのアクセス          | 外部 / 内部  |
| * / *                        | 受信            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure インフラストラクチャの Load Balancer                          | 外部 / 内部  |

>[!IMPORTANT]
> "*目的*" が**太字**になっているポートは、API Management サービスの正常なデプロイを必要とします。 ただし、その他のポートをブロックすると、実行中のサービスの使用と監視を行う能力が低下します。

+ **SSL 機能**:SSL 証明書チェーンの構築と検証を有効にするには、API Management サービスに ocsp.msocsp.com、mscrl.microsoft.com、および crl.microsoft.com に対する送信ネットワーク接続が必要です。 API Management にアップロードする任意の証明書に CA ルートへの完全なチェーンが含まれている場合、この依存関係は必要ありません。

+ **DNS アクセス**:DNS サーバーとの通信には、ポート 53 での発信アクセスが必要です。 カスタム DNS サーバーが VPN ゲートウェイの相手側にある場合、DNS サーバーは API Management をホストしているサブネットから到達できる必要があります。

+ **メトリックと正常性の監視**:Azure Monitoring エンドポイントへの発信ネットワーク接続、次のドメインで解決されます。

    | Azure 環境 | エンドポイント                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`East US 2` が eastus2.warm.ingestion.msftcloudes.com である `azure region`.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **SMTP リレー**:SMTP リレー用の送信ネットワーク接続。`smtpi-co1.msn.com`、`smtpi-ch1.msn.com`、`smtpi-db3.msn.com`、`smtpi-sin.msn.com`、`ies.global.microsoft.com` の各ホストで解決されます。

+ **開発者ポータル CAPTCHA**: 開発者ポータルの CAPTCHA 用の発信ネットワーク接続。ホスト `client.hip.live.com` と `partner.hip.live.com` で解決されます。

+ **Azure portal 診断**:仮想ネットワーク内から API Management 拡張機能を使用しているときに、Azure portal から診断ログのフローを有効にするには、ポート 443 での `dc.services.visualstudio.com` への送信アクセスが必要です。 これは、拡張機能の使用時に発生する可能性がある問題のトラブルシューティングに役立ちます。

+ **Express Route またはネットワーク仮想アプライアンスを使用したオンプレミスのファイアウォールへのトラフィックの強制トンネリング**: 顧客の一般的な構成では、API Management の委任されたサブネットからのすべてのトラフィックを、オンプレミスのファイアウォールまたはネットワーク仮想アプライアンスに強制的に流す、独自の既定のルート (0.0.0.0/0) が定義されています。 このトラフィック フローでは、Azure API Management を使用した接続は必ず切断されます。これは、発信トラフィックがオンプレミスでブロックされるか、さまざまな Azure エンドポイントで有効ではなくなった、認識できないアドレス セットに NAT 処理されることが原因です。 これを解決するには、いくつかのことを実行する必要があります。

  * API Management サービスがデプロイされているサブネット上でサービス エンドポイントを有効にします。 Azure SQL、Azure Storage、Azure EventHub、Azure ServiceBus の[サービス エンドポイント][ServiceEndpoints]を有効にする必要があります。 これらのサービスに対して、API Management の委任されたサブネットからエンドポイントを直接有効にすると、サービス トラフィックの最適なルーティングを提供する Microsoft Azure バックボーン ネットワークを使用できるようになります。 トンネリングが強制された API Management でサービス エンドポイントを使用すると、上記の Azure サービス のトラフィックが強制的にトンネリングされることはありません。 API Management サービスの他の 依存関係トラフィックは強制的にトンネリングされ、失われることはありません。依存関係トラフィックが失われた場合、API Management サービスが適切に機能しなくなります。
    
  * インターネットから API Management サービスの管理エンドポイントへのすべてのコントロール プレーン トラフィックは、API Management によってホストされている受信 IP の特定のセットを使用してルーティングされます。 トラフィックが強制的にトンネリングされると、応答がこれらの受信送信元 IP に対称的にマップされなくなります。 この制限を克服するには、次のユーザー定義ルート ([UDR][UDRs]) を追加し、これらのホスト ルートの宛先を "Internet" に設定することによってトラフィックを Azure に誘導する必要があります。 コントロール プレーン トラフィックの受信 IP のセットは次のとおりです。
    
     | Azure 環境 | 管理 IP アドレス                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | 13.84.189.17/32、13.85.22.63/32、23.96.224.175/32、23.101.166.38/32、52.162.110.80/32、104.214.19.224/32、13.64.39.16/32、40.81.47.216/32、   51.145.179.78/32、52.142.95.35/32、40.90.185.46/32、20.40.125.155/32、52.159.16.255/32、40.82.157.167/32、51.137.136.0/32、40.81.185.8/32、40.81.47.216/32、51.145.56.125/32、40.81.89.24/32、52.224.186.99/32、51.145.179.78/32、52.140.238.179/32、40.66.60.111/32、52.139.80.117/32、20.46.144.85/32、191.233.24.179/32、40.90.185.46/32、102.133.130.197/32、52.139.20.34/32、40.80.232.185/32、13.71.49.1/32、13.64.39.16/32、20.40.160.107/32、20.37.52.67/32、20.44.33.246/32、13.86.102.66/32、20.40.125.155/32、51.143.127.203/32、52.253.225.124/32、52.253.159.160/32、20.188.77.119/32、20.44.72.3/32、52.142.95.35/32、52.139.152.27/32、20.39.80.2/32、51.107.96.8/32、20.39.99.81/32、20.37.81.41/32、51.107.0.91/32、102.133.0.79/32、51.116.96.0/32、51.116.0.0/32 |
    | Azure Government  | 52.127.42.160/32、52.127.34.192/32 |
    | Azure China       | 139.217.51.16/32、139.217.171.176/32 |

  * 強制的にトンネリングされる、API Management サービスの他の依存関係については、ホスト名を解決し、エンドポイントに到達するための方法が必要です。 次のような方法があります。
      - メトリックと正常性の監視
      - Azure portal 診断
      - SMTP リレー
      - 開発者ポータル CAPTCHA

## <a name="troubleshooting"> </a>トラブルシューティング
* **初回のセットアップ**:API Management サービスのサブネットへの初回のデプロイが成功しなかった場合は、同じサブネットに仮想マシンを先にデプロイすることをお勧めします。 次に、リモート デスクトップを仮想マシンにデプロイし、Azure サブスクリプション内の次のリソースのいずれかに接続できることを確認します。
    * Azure Storage BLOB
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > 接続を確認したら、サブネットにデプロイされているすべてのリソースを必ず削除してから、サブネットに API Management をデプロイしてください。

* **増分更新**:ネットワークを変更するときは、[NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus) を参照して、API Management サービスが依存している重要なリソースへのアクセスが失われていないことを確認してください。 接続状態は、15 分間隔で更新されます。

* **リソース ナビゲーション リンク**:Resource Manager スタイルの VNET サブネットにデプロイすると、API Management では、リソース ナビゲーション リンクを作成することでサブネットが予約されます。 サブネットに別のプロバイダーのリソースが既に含まれている場合、デプロイは**失敗**します。 同様に、API Management サービスを別のサブネットに 移動するか削除すると、そのリソース ナビゲーション リンクが削除されます。

## <a name="subnet-size"></a>サブネットのサイズ要件
Azure は、各サブネット内で一部の IP アドレスを予約し、これらのアドレスを使用することはできません。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Azure VNET インフラストラクチャによって使用される IP アドレスに加えて、サブネットの各 API Management インスタンスは、Premium SKU のユニットごとに 2 つの IP アドレス、または Developer SKU 用に 1 つの IP アドレスを使用します。 各インスタンスによって、外部ロード バランサー用の IP アドレスが別途予約されています。 内部 VNET に展開する場合は、内部ロード バランサー用に追加の IP アドレスが必要です。

前述の計算によると、API Management で展開できるサブネットの最小サイズは /29 で、3 つの IP アドレスが提供されます。

## <a name="routing"> </a> ルーティング
+ 負荷分散されたパブリック IP アドレス (VIP) は、すべてのサービス エンドポイントへのアクセスを提供するために予約されます。
+ サブネット IP 範囲 (DIP) の IP アドレスは VNET 内のリソースにアクセスするために使用され、パブリック IP アドレス (VIP) は VNET の外部のリソースにアクセスするために使用されます。
+ 負荷分散されたパブリック IP アドレスは、Azure Portal の [概要]/[要点] ブレードで確認できます。

## <a name="limitations"> </a>制限事項
* API Management インスタンスが含まれるサブネットには、その他の Azure リソースの種類を含めることはできません。
* サブネットと API Management サービスは、同じサブスクリプション内になければなりません。
* API Management インスタンスが含まれるサブネットは、サブスクリプション間で移動できません。
* 内部仮想ネットワーク モードで構成された複数リージョンの API Management デプロイでは、ルーティングを設定するユーザーが分散負荷の管理を担当します。デプロイでは、ユーザーが、ルーティングを担当するように、複数のリージョンの負荷分散の管理を担当します。
* 別のリージョン内にあるグローバルにピアリングされた VNET 内のリソースから、内部モードの API Management サービスへの接続は、プラットフォームの制限により機能しません。 詳しくは、[ある仮想ネットワーク内のリソースは、ピアリングされた仮想ネットワークの Azure 内部ロード バランサーと通信できない](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)ことに関するページをご覧ください。


## <a name="related-content"> </a>関連コンテンツ
* [VPN Gateway を使用して Virtual Network をバックエンドに接続する](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [異なるデプロイ モデルの Virtual Network を PowerShell を使用して接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Azure API Management で API Inspector を使用して呼び出しをトレースする方法](api-management-howto-api-inspector.md)
* [仮想ネットワークに関する FAQ](../virtual-network/virtual-networks-faq.md)
* [サービス タグ](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
