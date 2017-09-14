---
title: "オンプレミス データ ゲートウェイのインストール - Azure Logic Apps | Microsoft Docs"
description: "オンプレミスのデータ ソースにアクセスする前に、オンプレミスのデータ ソースとロジック アプリのデータ ソース間でデータ転送と暗号化を高速で行うオンプレミスのデータ ゲートウェイをインストールします"
keywords: "データへのアクセス, オンプレミス, データ転送, 暗号化, データ ソース"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/13/2017
ms.author: LADocs; dimazaid; estfan
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 34e68ae7d35019848b35c785a2715ec458dc6e73
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps 向けのオンプレミス データ ゲートウェイをインストールする

ロジック アプリがオンプレミスのデータ ソースにアクセスする前に、オンプレミス データ ゲートウェイをインストールして設定する必要があります。 このゲートウェイは、オンプレミスのシステムとロジック アプリ間でのデータ転送と暗号化を高速で行うブリッジとして機能します。 ゲートウェイは、オンプレミスのソースから、Azure Service Bus 経由の暗号化されたチャネルでデータを転送します。 すべてのトラフィックは、ゲートウェイ エージェントからの安全な送信トラフィックとして生成されます。 詳細については、[データ ゲートウェイのしくみ](#gateway-cloud-service)に関するセクションを参照してください。

このゲートウェイは、次のオンプレミスのデータ ソースへの接続をサポートします。

*   BizTalk Server 2016
*   DB2  
*   ファイル システム
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP アプリケーション サーバー 
*   SAP メッセージ サーバー
*   SharePoint
*   SQL Server
*   Teradata

以下の手順は、[ゲートウェイとロジック アプリ間の接続を設定](./logic-apps-gateway-connection.md)する前にオンプレミス データ ゲートウェイをインストールする方法を示しています。 サポートされる接続の詳細については、[Azure Logic Apps のコネクタ](https://docs.microsoft.com/azure/connectors/apis-list)に関するページを参照してください。 

他のサービスでゲートウェイを使用する方法については、次の記事を参照してください。

*   [Microsoft Power BI オンプレミス データ ゲートウェイ](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services オンプレミス データ ゲートウェイ](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow オンプレミス データ ゲートウェイ](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps オンプレミス データ ゲートウェイ](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>
## <a name="requirements"></a>必要条件

**最小**:

* .NET Framework 4.5
* Windows 7 または Windows Server 2008 R2 (以降) の 64 ビット バージョン

**推奨**:

* 8 コア CPU
* 8 GB メモリ
* Windows 2012 R2 (以降) の 64 ビット バージョン

**重要な考慮事項**:

* オンプレミス データ ゲートウェイはローカル コンピューターにのみインストールしてください。
ドメイン コントローラーにはゲートウェイをインストールできません。

   > [!TIP]
   > データ ソースと同じコンピューターにゲートウェイをインストールする必要はありません。 待機時間を最小限に抑えるために、アクセス許可があることを前提として、データ ソースにできるだけ近いコンピューターまたは同じコンピューターにゲートウェイをインストールできます。

* 電源がオフになる、スリープ状態に移行する、またはインターネットに接続していないコンピューターにはゲートウェイをインストールしないようにしてください。このような状況下では、ゲートウェイが動作しない場合があります。 また、ワイヤレス ネットワークではゲートウェイのパフォーマンスが低下する可能性もあります。

* インストール時は、Microsoft アカウントではなく、Azure Active Directory (Azure AD) が管理する[職場または学校アカウント](https://docs.microsoft.com/azure/active-directory/sign-up-organization)でサインインする必要があります。 

  後でゲートウェイ リソースを作成し、ゲートウェイのインストールと関連付けるときに、Azure ポータルで同じ職場または学校アカウントを使用する必要があります。 ロジック アプリとオンプレミス データベース ソース間に接続を作成するときに、このゲートウェイ リソースを選択します。 [Azure AD の職場または学校アカウントを使用する必要があるのはなぜですか?](#why-azure-work-school-account)

  > [!TIP]
  > Office 365 プランにサインアップして、実際の職場の電子メール アドレスを指定しなかった場合、サインイン アドレスは jeff@contoso.onmicrosoft.com のようになります。 

* バージョンが 14.16.6317.4 より前のインストーラーで設定を行った既存のゲートウェイがある場合は、最新のインストーラーを実行してもゲートウェイの場所を変更することはできません。 ただし、最新のインストーラーを使用して、目的の場所に新しいゲートウェイを設定できます。
  
  バージョンが 14.16.6317.4 より前のゲートウェイ インストーラーはあるが、ゲートウェイをまだインストールしていない場合は、最新のインストーラーをダウンロードして使用できます。

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>データ ゲートウェイをインストールする

1.  [ゲートウェイ インストーラーをローカル コンピューターにダウンロードして実行します](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

2. 使用条件とプライバシーに関する声明を確認して同意します。

3. ゲートウェイをインストールするローカル コンピューター上のパスを指定します。

4. 求められたら、Microsoft アカウントではなく、Azure の職場または学校のアカウントでサインインします。

   ![Azure の職場または学校アカウントでサインインします](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. [ゲートウェイ クラウド サービス](#gateway-cloud-service)を使用して、インストールされているゲートウェイを登録します。 **[このコンピューターに新しいゲートウェイを登録します]** を選択します。

   ゲートウェイ クラウド サービスでは、データ ソースの資格情報とゲートウェイの詳細を暗号化して格納します。 
   サービスは、ロジック アプリ、オンプレミス データ ゲートウェイ、オンプレミスのデータ ソース間のクエリとその結果もルーティングします。

6. ゲートウェイのインストールの名前を指定します。 回復キーを作成し、回復キーを確認します。 

   > [!IMPORTANT] 
   > 回復キーは、少なくとも 8 文字を含める必要があります。 このキーを安全な場所に保存してください。 既存のゲートウェイの移行、復元、または引き継ぎを行うときにもこのキーが必要です。

   1. ゲートウェイのインストールで使用されるゲートウェイ クラウド サービスと Azure Service Bus の既定のリージョンを変更するには、**[リージョンの変更]** を選択します。

      ![リージョンの変更](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      既定のリージョンは、Azure AD テナントに関連付けられているリージョンです。

   2. 次のウィンドウで **[リージョンの選択]** を開き、別のリージョンを選択します。

      ![別のリージョンの選択](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      たとえば、ロジック アプリと同じリージョンを選択したり、待機時間を短縮するためにオンプレミスのデータ ソースに最も近いリージョンを選択したりできます。 ゲートウェイ リソースとロジック アプリの場所は、違っていてもかまいません。

      > [!IMPORTANT]
      > インストール後は、このリージョンを変更することはできません。 このリージョンは、ゲートウェイ向けの Azure リソースを作成できる場所を決定し、作成できる場所を限定します。 このため、Azure でゲートウェイ リソースを作成するときは、リソースの場所がゲートウェイのインストール中に選択したリージョンと一致していることを確認してください。
      > 
      > 後で、ゲートウェイ向けに別のリージョンを使用したくなった場合は、新しいゲートウェイを設定する必要があります。

   3. 準備ができたら、**[完了]** を選択します。

7. 次に、Azure ポータルで以下の手順に従って、[ゲートウェイ用の Azure リソースを作成](../logic-apps/logic-apps-gateway-connection.md)できます。 

詳細については、[データ ゲートウェイのしくみ](#gateway-cloud-service)に関するセクションを参照してください。

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>既存のゲートウェイの移行、復元、または引き継ぎを行う

これらのタスクを実行するには、ゲートウェイのインストール時に指定した回復キーが必要です。

1. コンピューターの [スタート] メニューから、**[オンプレミス データ ゲートウェイ]** を選択します。

2. インストーラーが開いたら、ゲートウェイのインストールに使用したものと同じ Azure の職場または学校アカウントでサインインします。

3. **[Migrate, restore, or take over an existing gateway]\(既存のゲートウェイの移行、復元、または引き継ぎを行う\)** を選択します。

4. 移行、復元、または引き継ぎを行うゲートウェイの名前と回復キーを指定します。

<a name="restart-gateway"></a>
## <a name="restart-the-gateway"></a>ゲートウェイの再起動

ゲートウェイは Windows サービスとして実行されます。 他の Windows サービスと同じように、開始と停止を複数の方法で行うことができます。 たとえば、ゲートウェイが実行されているコンピューターで管理者特権を使用してコマンド プロンプトを開き、以下に示すコマンドのいずれかを実行することができます。

* サービスを停止するには、次のコマンドを実行します。
  
    `net stop PBIEgwService`

* サービスを開始するには、次のコマンドを実行します。
  
    `net start PBIEgwService`

### <a name="windows-service-account"></a>Windows サービス アカウント

オンプレミス データ ゲートウェイは、Windows サービスのログオン資格情報として `NT SERVICE\PBIEgwService` を使用するように設定されます。 既定により、ゲートウェイには、そのゲートウェイがインストールされているコンピューターで "サービスとしてログオン" する権限があります。

> [!NOTE]
> Windows サービス アカウントは、オンプレミスのデータ ソースへの接続に使用するアカウントとも、クラウド サービスへのサインインに使用する職場または学校アカウントとも異なります。

## <a name="configure-a-firewall-or-proxy"></a>ファイアウォールまたはプロキシの構成

ゲートウェイは、[Azure Service Bus](https://azure.microsoft.com/services/service-bus/) への送信接続を作成します。 ゲートウェイのプロキシ情報を指定するには、[プロキシ設定の構成](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/)に関するページを参照してください。

ファイアウォールまたはプロキシが接続をブロックする可能性があるかどうかを確認するには、コンピューターがインターネットと [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) に実際に接続できるかどうかを確認します。 PowerShell プロンプトから、次のコマンドを実行します。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> このコマンドは、ネットワーク接続と、Azure Service Bus への接続のみをテストします。 したがって、ゲートウェイまたは資格情報とゲートウェイの詳細を暗号化して格納するゲートウェイ クラウド サービスに対しては何の操作も行いません。 
>
> また、このコマンドは、Windows Server 2012 R2 以降と Windows 8.1 以降でのみ使用できます。 それより前のバージョンの OS では、Telnet を使用して接続をテストすることができます。 詳細については、[Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) に関するページを参照してください。

結果は、次の例のようになります。

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

**TcpTestSucceeded** が **True** に設定されていない場合、ファイアウォールによってブロックされている可能性があります。 包括的にテストしたい場合は、**ComputerName** と **Port** の値を、このトピックの「[ポートの構成](#configure-ports)」の一覧に記載されている値に置き換えます。

Azure Service Bus から Azure データ センターへの接続も、ファイアウォールによってブロックされる可能性があります。 その場合は、リージョン内にあるそれらのデータ センターの IP アドレスをすべて承認 (ブロック解除) してください。 これらの IP アドレスについては、[こちら](https://www.microsoft.com/download/details.aspx?id=41653)で Azure の IP アドレスの一覧を取得してください。

## <a name="configure-ports"></a>ポートの構成

ゲートウェイによって [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) への送信接続が作成され、送信ポートの TCP 443 (既定)、5671、5672、9350 ～ 9354 で通信が行われます。 ゲートウェイでは受信ポートは必要ありません。 詳細については、[Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) に関するページを参照してください。

| ドメイン名 | 送信ポート | Description |
| --- | --- | --- |
| *. analysis.windows.net | 使用します | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671 - 5672 | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443、9350 - 9354 | TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) | 
| *. frontend.clouddatahub.net | 使用します | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *. msftncsi.com | 443 | Power BI サービスによってゲートウェイにアクセスできない場合、インターネット接続性のテストに使用されます。 | 

ドメインではなく IP アドレスを承認する必要がある場合は、[Microsoft Azure データセンター IP 範囲リスト](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードして使用してください。 Azure Service Bus 接続は、完全修飾ドメイン名ではなく IP アドレスを使用して行われる場合があります。

<a name="gateway-cloud-service"></a>
## <a name="how-does-the-data-gateway-work"></a>データ ゲートウェイの動作

データ ゲートウェイによって、ロジック アプリ、ゲートウェイ クラウド サービス、オンプレミスのデータ ソース間に迅速かつセキュリティで保護された通信が促進されます。 

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

このため、クラウドのユーザーがオンプレミス データ ソースに接続されている要素を操作すると、次の動作が行われます。

1. ゲートウェイ クラウド サービスが、データ ソース用の暗号化された資格情報と併せてクエリを作成し、そのクエリを処理するためのゲートウェイのキューに送信します。

2. ゲートウェイ クラウド サービスはクエリを分析し、Azure Service Bus に要求をプッシュします。

3. オンプレミス データ ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。

4. ゲートウェイはクエリを取得して資格情報を復号化し、その資格情報でデータ ソースに接続します。

5. ゲートウェイは、実行するためにクエリをデータ ソースに送信します。

6. 結果がデータ ソースからゲートウェイに返送され、その後ゲートウェイ クラウド サービスに送信されます。 ゲートウェイ クラウド サービスが結果を使用します。

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="general"></a>全般

**Q**: SQL Azure など、クラウドのデータ ソースにゲートウェイは必要ですか? <br/>
**A**: いいえ。 ゲートウェイは、オンプレミス データ ソースのみに接続します。

**Q**: ゲートウェイはデータ ソースと同じコンピューターにインストールする必要がありますか? <br/>
**A**: いいえ。 ゲートウェイは、指定された接続情報を使用してデータ ソースに接続します。 その意味ではゲートウェイをクライアント アプリケーションと見なすことができます。 ゲートウェイに必要なのは、指定されたサーバー名に接続する機能だけです。

<a name="why-azure-work-school-account"></a>

**Q**: サインインで Azure の職場または学校のアカウントを使用する必要があるのはなぜですか? <br/>
**A**: オンプレミスのデータ ゲートウェイをインストールするときに、Azure の職場または学校アカウントのみを使用することができます。 サインイン アカウントは、Azure Active Directory (Azure AD) によって管理されるテナントに格納されます。 通常、Azure AD アカウントのユーザー プリンシパル名 (UPN) は電子メール アドレスと一致します。

**Q**: 自分の資格情報はどこに格納されますか? <br/>
**A**: データ ソース用に入力した資格情報は、暗号化されてゲートウェイ クラウド サービスに格納されます。 資格情報の復号化はオンプレミス データ ゲートウェイで行われます。

**Q**: ネットワーク帯域幅の要件はありますか? <br/>
**A**: スループットの高いネットワーク接続の確保をお勧めします。 スループットは環境ごとに異なり、送信されるデータの量も結果に影響を与えます。 ExpressRoute を使用すると、オンプレミスと Azure データ センター間で一定レベルのスループットを保証するために役立ちます。
サード パーティ製のツールである Azure Speed Test アプリを使用すると、スループットを測定できます。

**Q**: ゲートウェイからデータ ソースにクエリを実行する際に待機時間が発生するのはなぜですか? 最適なアーキテクチャとは何ですか? <br/>
**A**: ネットワークの待機時間を減らすには、できるだけデータ ソースの近くにゲートウェイをインストールします。 実際のデータ ソース上にゲートウェイをインストールできれば、その近接性によって待ち時間の発生を最小限に抑えることができます。 データ センターについても同様に検討してください。 たとえば、提供するサービスで米国西部のデータ センターを使用しており、SQL Server が Azure VM でホストされている場合は、その Azure VM も米国西部のデータ センターに配置されている必要があります。 こうすることで待ち時間は最短となり、Azure VM でデータ送信料金が発生することもありません。

**Q**: 結果はどのようにクラウドに送られますか? <br/>
**A**: 結果は Azure Service Bus を介して送信されます。

**Q**: クラウドからゲートウェイへの着信接続はありますか? <br/>
**A**: いいえ。 ゲートウェイは、Azure Service Bus への発信接続を使用します。

**Q**: 発信接続をブロックしたらどうなりますか? 開くために何をする必要がありますか? <br/>
**A**: ゲートウェイが使用するポートとホストを確認してください。

**Q**: 実際の Windows サービスは何と呼ばれていますか?<br/>
**A**: サービスでは、ゲートウェイは、Power BI Enterprise Gateway Service と呼ばれています。

**Q**: ゲートウェイ Windows サービスは、Azure Active Directory アカウントを使用して実行できますか? <br/>
**A**: いいえ。 Windows サービスには有効な Windows アカウントが必要です。 既定では、Windows サービスはサービス SID の NT SERVICE\PBIEgwService を使用して実行されます。

### <a name="high-availability-and-disaster-recovery"></a>高可用性と障害復旧

**Q**: 障害復旧のためにはどのようなオプションを使用できますか? <br/>
**A**: 回復キーを使用して、ゲートウェイを復元または移動することができます。 ゲートウェイをインストールするときに、回復キーを指定します。

**Q**: 回復キーの利点は何ですか? <br/>
**A**: 回復キーを利用すると、災害発生後にゲートウェイの設定を移行または回復することができます。

**Q**: ゲートウェイで高可用性のシナリオを有効にする予定はありますか? <br/>
**A**: そのようなシナリオへの対応は計画中ですが、具体的な時期はまだ決まっていません。

## <a name="troubleshooting"></a>トラブルシューティング

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: オンプレミスのデータ ソースに送信されるクエリはどのようにして確認できますか? <br/>
**A**: 送信されるクエリを含むクエリ トレースを有効にすることができます。 クエリ トレースは、トラブルシューティングが完了したら忘れずに元の値に戻してください。 クエリ トレースをオンのままにしておくと、ログのサイズが増大します。

クエリをトレースするためにデータ ソースに用意されているツールを使用することもできます。 たとえば、SQL Server 用の拡張イベントまたは SQL Profiler や Analysis Services を使用できます。

**Q**: ゲートウェイのログはどこにありますか? <br/>
**A**: このトピックの後半の「ツール」を参照してください。

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

