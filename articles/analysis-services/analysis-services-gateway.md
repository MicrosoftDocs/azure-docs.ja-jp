---
title: "オンプレミスのデータ ゲートウェイ | Microsoft Docs"
description: "Azure の Analysis Services サーバーがオンプレミスのデータ ソースに接続する場合、オンプレミスのゲートウェイが必要です。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 90584f60864589744888817ea71d0eb0d4d170ff
ms.lasthandoff: 11/17/2016


---
# <a name="on-premises-data-gateway"></a>オンプレミスのデータ ゲートウェイ
オンプレミスのデータ ゲートウェイはブリッジとして機能し、オンプレミスのデータ ソースとクラウドの Azure Analysis Services サーバーの間のセキュリティで保護されたデータ転送を提供します。

ゲートウェイは、ネットワーク内のコンピューターにインストールされます。 Azure サブスクリプションに存在する Azure Analysis Services サーバーごとに 1 つのゲートウェイをインストールする必要があります。 たとえば、Azure サブスクリプションにオンプレミスのデータ ソースに接続するサーバーが 2 つある場合、ネットワークの 2 台の異なるコンピューターにゲートウェイをインストールする必要があります。

## <a name="requirements"></a>必要条件
**最低限必要なもの**

* .NET Framework 4.5
* Windows 7/Windows Server 2008 R2 (以降) の 64 ビット版

**推奨されるもの:**

* 8 コア CPU
* 8 GB メモリ
* Windows 2012 R2 (以降) の 64 ビット バージョン

**重要な考慮事項**

* ドメイン コントローラーにゲートウェイをインストールすることはできません。
* 1 台のコンピューターにインストールできるゲートウェイは 1 つだけです。
* 常に稼働していてスリープ状態にならないコンピューターにゲートウェイをインストールします。 コンピューターが稼働していないと、Azure Analysis Services サーバーはオンプレミスのデータ ソースに接続してデータを更新できません。
* ネットワークにワイヤレス接続されているコンピューターにはゲートウェイをインストールしないでください。 パフォーマンスが低下することがあります。
* 既に構成されているゲートウェイのサーバー名を変更するには、新しいゲートウェイを再インストールして構成する必要があります。
* 場合によっては、SQL Server Native Client (SQLNCLI11) などのネイティブ プロバイダーを使ってデータ ソースに接続する表形式モデルがエラーを返すことがあります。 詳しくは、「[データ ソースの接続](analysis-services-datasource.md)」をご覧ください。

## <a name="supported-on-premises-data-sources"></a>サポートされているオンプレミスのデータ ソース
プレビューでは、ゲートウェイは Azure Analysis Services サーバーと次のオンプレミスのデータ ソースの間の接続をサポートします。

* SQL Server
* SQL Data Warehouse
* APS
* Oracle
* Teradata

## <a name="download"></a>ダウンロード
 [ゲートウェイをダウンロードする](https://aka.ms/azureasgateway)

## <a name="install-and-configure"></a>インストールと構成
1. セットアップを実行します。
2. インストールする場所を選んで、ライセンス条項に同意します。
3. Azure にサインインします。
4. Azure の分析サーバーの名前を指定します。 指定できるサーバーはゲートウェイごとに 1 つだけです。 **[構成]** をクリックして構成します。

    ![Azure にサインインする](./media/analysis-services-gateway/aas-gateway-configure-server.png)

## <a name="how-it-works"></a>動作のしくみ
ゲートウェイは、組織のネットワーク内のコンピューターで Windows サービス "**オンプレミスのデータ ゲートウェイ**" として実行します。 Azure Analysis Services で使うためにインストールするゲートウェイは、Power BI などの他のサービスに使用されるものと同じゲートウェイに基づいていますが、構成方法にいくつか違いがあります。

![動作のしくみ](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

クエリとデータ フローは次のように動作します。

1. クエリは、オンプレミス データ ソースに対する暗号化された資格情報を使用して、クラウド サービスによって作成されます。 その後、処理のためにゲートウェイのキューに送信されます。
2. ゲートウェイ クラウド サービスはクエリを分析して、[Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) に要求をプッシュします。
3. オンプレミス データ ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。
4. ゲートウェイは、クエリを取得し、資格情報を復号化し、その資格情報によってデータ ソースに接続します。
5. ゲートウェイは、実行するためにクエリをデータ ソースに送信します。
6. 結果が、データ ソースからゲートウェイを経て、クラウド サービスに返送されます。

## <a name="windows-service-account"></a>Windows サービス アカウント
オンプレミス データ ゲートウェイは、Windows サービスのログオン資格情報に関して *NT SERVICE\PBIEgwService* を使用するように構成されています。 既定では、この資格情報は、ゲートウェイをインストールしているコンピューターのコンテキストで、サービスとしてログオンの権限を持っています。 この資格情報は、オンプレミス データ ソースに接続するために使われるアカウントまたは Azure アカウントと同じではありません。  

認証のためにプロキシ サーバーで問題が発生する場合は、Windows サービス アカウントをドメイン ユーザー アカウントまたは管理されたサービスのアカウントに変更できます。

## <a name="ports"></a>ポート
ゲートウェイは、Azure Service Bus への送信接続を作成します。 通信を行う送信ポートは、TCP 443 (既定)、5671、5672、9350 ～ 9354 です。  ゲートウェイでは受信ポートは必要ありません。

データ リージョンの IP アドレスをファイアウォールでホワイトリストにすることをお勧めします。 [Microsoft Azure データセンターの IP リスト](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードできます。 このリストは毎週更新されます。

> [!NOTE]
> Azure データセンターの IP リストには、IP アドレスが CIDR 表記法で記載されています。 たとえば、10.0.0.0/24 は 10.0.0.0 ～ 10.0.0.24 の意味ではありません。 [CIDR の表記法に関するページ](http://whatismyipaddress.com/cidr)をご覧ください。
>
>

ゲートウェイで使われる完全修飾ドメイン名を次に示します。

| ドメイン名 | 送信ポート | 説明 |
| --- | --- | --- |
| *.powerbi.com |80 |インストーラーのダウンロードに使用される HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |使用します |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671 - 5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443、9350 - 9354 |TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) |
| *. frontend.clouddatahub.net |使用します |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |使用します |Power BI サービスによってゲートウェイにアクセスできない場合、インターネット接続のテストに使用されます。 |
| *.microsoftonline-p.com |443 |構成によっては認証に使用されます。 |

### <a name="forcing-https-communication-with-azure-service-bus"></a>Azure Service Bus との HTTPS 通信の強制
ダイレクト TCP ではなく HTTPS を使って Azure Service Bus と通信するようにゲートウェイに強制できます。ただし、大幅にパフォーマンスが低下します。 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* ファイルを変更する必要があります。 値を `AutoDetect` から `Https` に変更します。 既定では、このファイルは *C:\Program Files\On-premises data gateway* にあります。

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>トラブルシューティング
内部的には、Azure Analysis Services をオンプレミス データ ソースに接続するために使用されるオンプレミス データ ゲートウェイは、Power BI で使われるものと同じゲートウェイです。

ゲートウェイのインストールと構成に問題がある場合は、「[オンプレミス データ ゲートウェイのトラブルシューティング](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/)」をご覧ください。 ファイアウォールに問題があると思われる場合は、ファイアウォールまたはプロキシのセクションをご覧ください。

ゲートウェイにプロキシの問題があると思われる場合は、「[オンプレミス データ ゲートウェイのプロキシ設定を構成する](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* [Analysis Services を管理する](analysis-services-manage.md)
* [Azure Analysis Services からデータを取得する](analysis-services-connect.md)

