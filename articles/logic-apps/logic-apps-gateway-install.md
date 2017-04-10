---
title: "オンプレミス データ ゲートウェイのインストール - Azure Logic Apps | Microsoft Docs"
description: "オンプレミス データ ゲートウェイをインストールしてロジック アプリからオンプレミスのデータにアクセスします"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 23b806556667539156ee399e90c7bd4af808804f
ms.lasthandoff: 03/10/2017


---
# <a name="install-an-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps 向けのオンプレミス データ ゲートウェイのインストール

オンプレミス データ ゲートウェイでは、以下のデータ ソース接続がサポートされています。

*   BizTalk Server
*    DB2  
*   ファイル システム
*   Informix
*   MQ
*    Oracle Database 
*   SAP アプリケーション サーバー 
*   SAP メッセージ サーバー
*    SQL Server

これらの接続の詳細については、[Azure Logic Apps のコネクタ](https://docs.microsoft.com/azure/connectors/apis-list)に関するページを参照してください。

## <a name="installation-and-configuration"></a>インストールと構成

### <a name="requirements"></a>必要条件

最小:

* .NET Framework 4.5
* Windows 7 または Windows Server 2008 R2 (以降) の 64 ビット バージョン

推奨:

* 8 コア CPU
* 8 GB メモリ
* Windows 2012 R2 (以降) の 64 ビット バージョン

関連する考慮事項:

* オンプレミス データ ゲートウェイはローカル コンピューターにのみインストールしてください。
ドメイン コントローラーにはゲートウェイをインストールできません。

* 電源がオフになったり、スリープ状態に移行したりする可能性のあるコンピューターや、インターネットに接続していないコンピューターにはゲートウェイをインストールしないようにしてください。このような状況下では、ゲートウェイが動作しない場合があります。 また、ワイヤレス ネットワークではゲートウェイのパフォーマンスが低下する可能性もあります。

* オンプレミス データ ゲートウェイを Azure Active Directory ベースのアカウントに関連付けることができるように、使用できる電子メールアドレスは Azure での職場または学校の電子メール アドレスのみに限定されています。

    @outlook.com などの Microsoft アカウントを使用している場合は、Azure アカウントを使用して  [職場または学校の電子メール アドレスを作成](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)できます。

### <a name="install-the-gateway"></a>ゲートウェイのインストール

1.    オンプレミス データ ゲートウェイのインストーラーを[こちら](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)からダウンロードします。

2.    モードとして **[オンプレミスのデータ ゲートウェイ]** を指定します。

3. Azure の職場または学校アカウントでサインインします。 

4. 新しいゲートウェイを設定するか、既存のゲートウェイの移行、復元、または引き継ぎを行います。

    ゲートウェイを構成するには、ゲートウェイの名前と回復キーを指定し、**[構成]** を選択します。
  
    8 文字以上の回復キーを指定します。キーは安全な場所に記録しておいてください。 このキーは、ゲートウェイの移行、復元、または引き継ぎを行う場合に必要です。

    既存のゲートウェイの移行、復元、または引き継ぎを行うには、ゲートウェイの作成時に指定した回復キーを入力します。

### <a name="restart-the-gateway"></a>ゲートウェイの再起動

ゲートウェイは Windows サービスとして実行されるため、他の Windows サービスと同様に、開始や停止は複数の方法で行うことができます。 たとえば、ゲートウェイが実行されているコンピューターで管理者特権を使用してコマンド プロンプトを開き、以下に示すコマンドのいずれかを実行することができます。

* サービスを停止するには、次のコマンドを実行します。
  
    `net stop PBIEgwService`

* サービスを開始するには、次のコマンドを実行します。
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>ファイアウォールまたはプロキシの構成

ゲートウェイのプロキシ情報を指定するには、[プロキシ設定の構成](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/)に関するページを参照してください。

PowerShell プロンプトから次に示すコマンドを実行すると、ファイアウォールやプロキシによって接続がブロックされているかどうかを確認できます。 このコマンドでは Azure Service Bus への接続テストが行われます。テストの対象はネットワークの接続性に限られ、クラウド サーバー サービスやゲートウェイに関するテストは行われません。 このテストは、コンピューターから実際にインターネットに接続できるかどうかを判断するために役立ちます。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

結果は、次の例のようになります。 **TcpTestSucceeded** が true でない場合、ファイアウォールによってブロックされている可能性があります。

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

包括的にテストしたい場合は、**ComputerName** と **Port** の値を、このトピックの「[ポートの構成](#configure-ports)」の一覧に記載されている値に置き換えます。

Azure Service Bus から Azure データ センターへの接続も、ファイアウォールによってブロックされる可能性があります。 その場合は、リージョン内にあるそれらのデータ センターの IP アドレスをすべて承認 (ブロック解除) してください。
[ここで Azure IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653)の一覧を入手できます。

### <a name="configure-ports"></a>ポートの構成
ゲートウェイによって Azure Service Bus への送信接続が作成され、送信ポートの TCP 443 (既定)、5671、5672、9350 ～ 9354 で通信が行われます。 ゲートウェイでは受信ポートは必要ありません。

[ハイブリッド ソリューション](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)の詳細についてはこちらをご覧ください。

| ドメイン名 | 送信ポート | Description |
| --- | --- | --- |
| *. analysis.windows.net | 使用します | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671 -&5672; | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443、9350 - 9354 | TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) | 
| *. frontend.clouddatahub.net | 使用します | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *. msftncsi.com | 443 | Power BI サービスによってゲートウェイにアクセスできない場合、インターネット接続性のテストに使用されます。 | 

ドメインではなく IP アドレスを承認する必要がある場合は、[Microsoft Azure データセンター IP 範囲リスト](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードして使用してください。 Azure Service Bus 接続は、完全修飾ドメイン名ではなく IP アドレスを使用して行われる場合があります。

### <a name="sign-in-accounts"></a>サインイン アカウント

所属する組織のアカウントである職場または学校アカウントを使用してサインインできます。 Office 365 プランにサインアップして、実際の職場の電子メール アドレスを指定しなかった場合、サインイン アドレスは jeff@contoso.onmicrosoft.com のようになります。 クラウド サービス内ではアカウントが Azure Active Directory (Azure AD) のテナントに格納されます。 通常、Azure AD アカウントの UPN は電子メール アドレスと一致します。

### <a name="windows-service-account"></a>Windows サービス アカウント

オンプレミス データ ゲートウェイは、Windows サービスのログオン資格情報として NT SERVICE\PBIEgwService を使用するように設定されています。 既定でゲートウェイには、そのゲートウェイがインストールされているコンピューターのコンテキスト内で "サービスとしてログオン" する権限があります。

このサービス アカウントは、オンプレミスのデータ ソースへの接続に使用するアカウントとも、クラウド サービスへのサインインに使用する職場または学校アカウントとも異なります。

## <a name="how-the-gateway-works"></a>ゲートウェイのしくみ
他のユーザーがオンプレミスのデータ ソースに接続されている要素を操作する場合:

1. クラウド サービスが、クエリと、データ ソース用の暗号化された資格情報を作成し、ゲートウェイが処理するようにクエリをキューに送信します。
2. このサービスはクエリを分析して、Azure Service Bus に要求をプッシュします。
3. オンプレミス データ ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。
4. ゲートウェイはクエリを取得して資格情報を復号化し、その資格情報でデータ ソースに接続します。
5. ゲートウェイは、実行するためにクエリをデータ ソースに送信します。
6. 結果がデータ ソースからゲートウェイに返送され、その後クラウド サービスに送信されます。 サービスが結果を使用します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="general"></a>全般

**質問**: SQL Azure など、クラウドのデータ ソースにゲートウェイは必要ですか? <br/>
**回答**: いいえ。 ゲートウェイは、オンプレミス データ ソースのみに接続します。

**質問**: 実際の Windows サービスは何と呼ばれていますか?<br/>
**回答**: サービスでは、ゲートウェイは、Power BI エンタープライズ ゲートウェイ サービスと呼ばれています。

**質問**: は、クラウドからゲートウェイへの着信接続はありますか? <br/>
**回答**: いいえ。 ゲートウェイは、Azure Service Bus への発信接続を使用します。

**質問**: 発信接続をブロックしたらどうなりますか? 開くために何をする必要がありますか? <br/>
**回答**: ゲートウェイが使用するポートとホストを確認してください。

**質問**: ゲートウェイはデータ ソースと同じコンピューターにインストールする必要がありますか? <br/>
**回答**: いいえ。 ゲートウェイは、指定された接続情報を使用してデータ ソースに接続します。 その意味ではゲートウェイをクライアント アプリケーションと見なすことができます。 ゲートウェイに必要なのは、指定されたサーバー名に接続する機能だけです。

**質問**: ゲートウェイからデータ ソースにクエリを実行する際に待機時間が発生するのはなぜですか? 最適なアーキテクチャとは何ですか? <br/>
**回答**: ネットワークの待機時間を減らすには、できるだけデータ ソースの近くにゲートウェイをインストールします。 実際のデータ ソース上にゲートウェイをインストールできれば、その近接性によって待ち時間の発生を最小限に抑えることができます。 データ センターについても同様に検討してください。 たとえば、提供するサービスで米国西部のデータ センターを使用しており、SQL Server が Azure VM でホストされている場合は、その Azure VM も米国西部のデータ センターに配置されている必要があります。 こうすることで待ち時間は最短となり、Azure VM でデータ送信料金が発生することもありません。

**質問**: のネットワーク帯域幅の要件はありますか? <br/>
**回答**: スループットの高いネットワーク接続の確保をお勧めします。 スループットは環境ごとに異なり、送信されるデータの量も結果に影響を与えます。 ExpressRoute を使用すると、オンプレミスと Azure データ センター間で一定レベルのスループットを保証するために役立ちます。

サード パーティ製のツールである Azure Speed Test アプリを使用すると、スループットを測定できます。

**質問**: ゲートウェイ Windows サービスは、Azure Active Directory アカウントを使用して実行できますか? <br/>
**回答**: いいえ。 Windows サービスには有効な Windows アカウントが必要です。 既定では、Windows サービスはサービス SID の NT SERVICE\PBIEgwService を使用して実行されます。

**質問**: 結果はどのようにクラウドに送られますか? <br/>
**回答**: 結果は Azure Service Bus を介して送信されます。

**質問**: 自分の資格情報はどこに格納されますか? <br/>
**回答**: データ ソース用に入力した資格情報は、暗号化されてゲートウェイ クラウド サービスに格納されます。 資格情報の復号化はオンプレミスのゲートウェイで行われます。

### <a name="high-availabilitydisaster-recovery"></a>高可用性と障害復旧
**質問**: ゲートウェイで高可用性のシナリオを有効にする予定はありますか? <br/>
**回答**: そのようなシナリオへの対応は計画中ですが、具体的な時期はまだ決まっていません。

**質問**: 障害復旧のためにはどのようなオプションを使用できますか? <br/>
**回答**: 回復キーを使用して、ゲートウェイを復元または移動することができます。 ゲートウェイをインストールするときに、回復キーを指定します。

**質問**: 回復キーの利点は何ですか? <br/>
**回答**: 回復キーを利用すると、災害発生後にゲートウェイの設定を移行または回復することができます。

## <a name="troubleshooting"></a>トラブルシューティング

**質問**: ゲートウェイのログはありますか? <br/>
**回答**: このトピックの後半の「ツール」を参照してください。

**質問**: オンプレミス データ ソースに送信されるクエリはどのようにして確認できますか? <br/>
**回答**: 送信されるクエリを含むクエリ トレースを有効にすることができます。 クエリ トレースは、トラブルシューティングが完了したら忘れずに元の値に戻してください。 クエリ トレースをオンのままにしておくと、ログのサイズが増大します。

クエリをトレースするためにデータ ソースに用意されているツールを使用することもできます。 たとえば、SQL Server 用の拡張イベントまたは SQL Profiler や Analysis Services を使用できます。

### <a name="update-to-the-latest-version"></a>最新バージョンへの更新

ゲートウェイのバージョンが古くなるにつれ、数多くの問題が表面化する可能性があります。 一般的には、常に最新バージョンを使用することが最適な対処法となります。 1 か月以上ゲートウェイを更新していない場合は、最新バージョンのゲートウェイをインストールし、問題が再発するかどうかを確認してください。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>エラー: ユーザーをグループに追加できませんでした。 (-2147463168 PBIEgwService Performance Log Users)

ドメイン コントローラー (サポート対象外) にゲートウェイをインストールしようとすると、このエラーが表示されることがあります。 ゲートウェイは、必ずドメイン コントローラー以外のコンピューターにデプロイしてください。

## <a name="tools"></a>ツール
### <a name="collect-logs-from-the-gateway-configurer"></a>ゲートウェイ構成ツールからのログの収集

ゲートウェイのいくつかのログを収集することができます。 必ず最初にログを確認します。

#### <a name="installer-logs"></a>インストーラー ログ

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>構成ログ

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>エンタープライズ ゲートウェイ サービスのログ

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>イベント ログ

Data Management Gateway と PowerBIGateway のログは、**[アプリケーションとサービス ログ]** で確認できます。

### <a name="fiddler-trace"></a>Fiddler のトレース

[Fiddler](http://www.telerik.com/fiddler) は、HTTP トラフィックを監視する Telerik の無料ツールです。 クライアント コンピューターからの Power BI サービスによる HTTP トラフィックを確認できます。 このサービスを利用することで、エラーやその他の関連情報が明らかになる場合があります。

## <a name="next-steps"></a>次のステップ
    
* [ロジック アプリからオンプレミスのデータに接続する](../logic-apps/logic-apps-gateway-connection.md)
* [エンタープライズ統合機能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)

