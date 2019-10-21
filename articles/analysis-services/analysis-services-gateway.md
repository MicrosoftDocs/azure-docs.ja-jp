---
title: Azure Analysis Services でのオンプレミス データ ゲートウェイ | Microsoft Docs
description: Azure の Analysis Services サーバーがオンプレミスのデータ ソースに接続する場合、オンプレミスのゲートウェイが必要です。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8d9df32070ff252dff791650788888d1d9a6ce84
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294940"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>オンプレミス データ ゲートウェイを使用してオンプレミスのデータ ソースに接続する

オンプレミスのデータ ゲートウェイでは、オンプレミスのデータ ソースとクラウドの Azure Analysis Services サーバーの間のセキュリティで保護されたデータ転送を提供します。 同じリージョン内の複数の Azure Analysis Services サーバーで機能するだけでなく、最新バージョンのゲートウェイは、Azure Logic Apps、Power BI、Power Apps、および Microsoft Flow でも機能します。 同じサブスクリプションと同じリージョン内の複数のサービスを 1 つのゲートウェイに関連付けることができます。 インストールするゲートウェイはこれらすべてのサービスで同じですが、Azure Analysis Services と Logic Apps には追加の手順がいくつかあります。

Azure Analysis Services の場合、ゲートウェイの初回のセットアップは、4 つのプロセスで構成されます。

- **ダウンロードしてセットアップする**: この手順では、組織のコンピューターにゲートウェイ サービスをインストールします。 また、お使いの[テナントの](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD アカウントを使用して Azure にサインインします。 Azure の B2B (ゲスト) アカウントはサポートされていません。

- **ゲートウェイを登録する**: この手順では、ゲートウェイの名前と回復キーを指定し、リージョンを選択し、ゲートウェイを Gateway Cloud Service に登録します。 ゲートウェイ リソースは任意のリージョンに登録できますが、Analysis Services サーバーと同じリージョンに登録することをお勧めします。 

- **ゲートウェイ リソースを Azure 内に作成する**: この手順では、Azure サブスクリプションにゲートウェイ リソースを作成します。

- **サーバーをゲートウェイ リソースに接続する**: サブスクリプションにゲートウェイ リソースを用意したら、サーバーの接続を開始できます。 複数のサーバーとその他のリソースを接続できます。ただし、同じサブスクリプションと同じリージョンにあることが条件です。

## <a name="how-it-works"> </a>動作のしくみ
組織のコンピューターにインストールしたゲートウェイは、Windows サービス **オンプレミスのデータ ゲートウェイ**として実行されます。 このローカル サービスは、Azure Service Bus を通して Gateway Cloud Service に登録されます。 次に、Azure サブスクリプション用のオンプレミス データ ゲートウェイ リソースを作成します。 Azure Analysis Services サーバーは、Azure ゲートウェイ リソースに接続されます。 サーバー上のモデルが、クエリや処理を行うためにオンプレミスのデータ ソースに接続する必要がある場合、クエリとデータ フローは、ゲートウェイ リソース、Azure Service Bus、ローカルのオンプレミスのデータ ゲートウェイ サービスを経由してデータ ソースに接続します。 

![動作のしくみ](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

クエリとデータ フロー:

1. クエリは、オンプレミス データ ソースに対する暗号化された資格情報を使用して、クラウド サービスによって作成されます。 その後、処理のためにゲートウェイのキューに送信されます。
2. ゲートウェイ クラウド サービスはクエリを分析して、[Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) に要求をプッシュします。
3. オンプレミス データ ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。
4. ゲートウェイは、クエリを取得し、資格情報を復号化し、その資格情報によってデータ ソースに接続します。
5. ゲートウェイは、実行するためにクエリをデータ ソースに送信します。
6. 結果が、データ ソースからゲートウェイを経て、クラウド サービスとサーバーに返送されます。

## <a name="installing"></a>インストール

Azure Analysis Services 環境にインストールする場合、重要なのは「[オンプレミスのデータ ゲートウェイをインストールして構成する](analysis-services-gateway-install.md)」に説明されている手順に従うことです。 この記事は Azure Analysis Services に固有のものです。 Azure でオンプレミス データ ゲートウェイ リソースをセットアップし、Azure Analysis Services サーバーをリソースに接続するために必要な追加の手順が含まれます。

## <a name="ports-and-communication-settings"></a>ポートと通信の設定

ゲートウェイは、Azure Service Bus への送信接続を作成します。 通信を行う送信ポートは、TCP 443 (既定)、5671、5672、9350 から 9354 です。  ゲートウェイでは受信ポートは必要ありません。

データ領域の IP アドレスを使用するファイアウォールのホワイトリストに登録することが必要な場合があります。 [Microsoft Azure データセンターの IP リスト](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードできます。 このリストは毎週更新されます。 Azure データセンターの IP リストには、IP アドレスが CIDR 表記法で記載されています。 詳細については、[クラスのないドメイン間ルーティング ](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)に関するページを参照してください。

ゲートウェイで使用される完全修飾ドメイン名を次に示します。

| ドメイン名 | 送信ポート | 説明 |
| --- | --- | --- |
| *.powerbi.com |80 |インストーラーのダウンロードに使用される HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net、login.live.com、aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671 ～ 5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443、9350 ～ 9354 |TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) |
| *. frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |443 |Power BI サービスによってゲートウェイにアクセスできない場合、インターネット接続のテストに使用されます。 |
| *.microsoftonline-p.com |443 |構成によっては認証に使用されます。 |
| dc.services.visualstudio.com  |443 |テレメトリを収集するために AppInsights によって使用されます。 |

### <a name="force-https"></a>Azure Service Bus との HTTPS 通信の強制

ダイレクト TCP ではなく HTTPS を使用して Azure Service Bus と通信するようにゲートウェイに強制できます。ただし、これを行うと、パフォーマンスが大幅に低下します。 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* ファイルの値を `AutoDetect` から `Https` に変更することで、このファイルを変更できます。 このファイルは、通常は *C:\Program Files\On-premises data gateway* に配置されます。

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>次の手順 

次の記事は、ゲートウェイがサポートするすべてのサービスに適用される、オンプレミス データ ゲートウェイの一般的なコンテンツに含まれています。

* [オンプレミス データ ゲートウェイに関する FAQ](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [オンプレミス データ ゲートウェイのアプリの使用](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [テナント レベルの管理](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [プロキシ設定の構成](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [通信設定の調整](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [ログ ファイルの構成](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [トラブルシューティング](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [ゲートウェイのパフォーマンスの監視と最適化](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
