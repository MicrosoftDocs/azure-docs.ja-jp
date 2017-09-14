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
ms.date: 08/21/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 514b5404e8cbfa0baa657eb41736e20cad502638
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Azure のオンプレミスのデータゲートウェイを使用してオンプレミスのデータ ソースに接続する
オンプレミスのデータ ゲートウェイはブリッジとして機能し、オンプレミスのデータ ソースとクラウドの Azure Analysis Services サーバーの間のセキュリティで保護されたデータ転送を提供します。 同じリージョン内の複数の Azure Analysis Services サーバーで機能するだけでなく、最新バージョンのゲートウェイは、Azure Logic Apps、Power BI、Power Apps、および Microsoft Flow でも機能します。 同じリージョン内の複数のサービスを1 つのゲートウェイに関連付けることができます。 

 Azure Analysis Services では、同じリージョン内のゲートウェイ リソースが必要です。 たとえば、Azure Analysis Services サーバーが米国東部 2 リージョンにある場合は、米国東部 2 リージョン内のゲートウェイ リソースが必要です。 米国東部 2 リージョン内の複数のサーバーが同じゲートウェイを使用できます。

ゲートウェイの初回のセットアップは、4 つのパートで構成されるプロセスです。

- **ダウンロードしてセットアップする**: この手順では、組織のコンピューターにゲートウェイ サービスをインストールします。

- **ゲートウェイを登録する**: この手順では、ゲートウェイの名前と回復キーを指定し、リージョンを選択し、ゲートウェイを Gateway Cloud Service に登録します。

- **ゲートウェイ リソースを Azure 内に作成する**: この手順では、Azure サブスクリプションにゲートウェイ リソースを作成します。

- **サーバーをゲートウェイ リソースに接続する**: サブスクリプションにゲートウェイ リソースを用意したら、サーバーの接続を開始できます。

サブスクリプションにゲートウェイ リソースを構成した後、複数のサーバーと他のサービスをそれに接続できます。 別のリージョンにサーバーや他のサービスがある場合、必要なのは別のゲートウェイをインストールし、別のゲートウェイ リソースを作成することだけです。

今すぐ開始するには、「[オンプレミスのデータ ゲートウェイをインストールして構成する](analysis-services-gateway-install.md)」を参照してください。

## <a name="how-it-works"> </a>動作のしくみ
組織のコンピューターにインストールしたゲートウェイは、Windows サービス **オンプレミスのデータ ゲートウェイ**として実行されます。 このローカル サービスは、Azure Service Bus を通して Gateway Cloud Service に登録されます。 その後、Azure サブスクリプション用のゲートウェイ リソース Gateway Cloud Service を作成します。 Azure Analysis Services サーバーは、ゲートウェイ リソースに接続されます。 サーバー上のモデルが、クエリや処理を行うためにオンプレミスのデータ ソースに接続する必要がある場合、クエリとデータ フローは、ゲートウェイ リソース、Azure Service Bus、ローカルのオンプレミスのデータ ゲートウェイ サービスを経由してデータ ソースに接続します。 

![動作のしくみ](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

クエリとデータ フロー:

1. クエリは、オンプレミス データ ソースに対する暗号化された資格情報を使用して、クラウド サービスによって作成されます。 その後、処理のためにゲートウェイのキューに送信されます。
2. ゲートウェイ クラウド サービスはクエリを分析して、[Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) に要求をプッシュします。
3. オンプレミス データ ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。
4. ゲートウェイは、クエリを取得し、資格情報を復号化し、その資格情報によってデータ ソースに接続します。
5. ゲートウェイは、実行するためにクエリをデータ ソースに送信します。
6. 結果が、データ ソースからゲートウェイを経て、クラウド サービスとサーバーに返送されます。

## <a name="windows-service-account"> </a>Windows サービス アカウント
オンプレミス データ ゲートウェイは、Windows サービスのログオン資格情報に関して *NT SERVICE\PBIEgwService* を使用するように構成されています。 既定では、この資格情報は、ゲートウェイをインストールしているコンピューターのコンテキストで、サービスとしてログオンの権限を持っています。 この資格情報は、オンプレミス データ ソースに接続するために使われるアカウントまたは Azure アカウントと同じではありません。  

認証のためにプロキシ サーバーで問題が発生する場合は、Windows サービス アカウントをドメイン ユーザー アカウントまたは管理されたサービスのアカウントに変更できます。

## <a name="ports"> </a>ポート
ゲートウェイは、Azure Service Bus への送信接続を作成します。 通信を行う送信ポートは、TCP 443 (既定)、5671、5672、9350 ～ 9354 です。  ゲートウェイでは受信ポートは必要ありません。

データ領域の IP アドレスをファイアウォールでホワイトリストにすることをお勧めします。 [Microsoft Azure データセンターの IP リスト](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードできます。 このリストは毎週更新されます。

> [!NOTE]
> Azure データセンターの IP リストには、IP アドレスが CIDR 表記法で記載されています。 たとえば、10.0.0.0/24 は 10.0.0.0 ～ 10.0.0.24 の意味ではありません。 [CIDR の表記法に関するページ](http://whatismyipaddress.com/cidr)をご覧ください。
>
>

ゲートウェイで使われる完全修飾ドメイン名を次に示します。

| ドメイン名 | 送信ポート | 説明 |
| --- | --- | --- |
| *.powerbi.com |80 |インストーラーのダウンロードに使用される HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671 ～ 5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443、9350 ～ 9354 |TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) |
| *. frontend.clouddatahub.net |使用します |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |使用します |Power BI サービスによってゲートウェイにアクセスできない場合、インターネット接続のテストに使用されます。 |
| *.microsoftonline-p.com |443 |構成によっては認証に使用されます。 |

### <a name="force-https"></a>Azure Service Bus との HTTPS 通信の強制
ダイレクト TCP ではなく HTTPS を使用して Azure Service Bus と通信するようにゲートウェイに強制できます。ただし、これを行うと、パフォーマンスが大幅に低下します。 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* ファイルの値を `AutoDetect` から `Https` に変更することで、このファイルを変更できます。 このファイルは、通常は *C:\Program Files\On-premises data gateway* に配置されます。

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="faq"></a>よく寄せられる質問

### <a name="general"></a>全般

**Q**: クラウドにデータ ソースのゲートウェイ (Azure SQL Database など) は必要ですか? <br/>
**A**: いいえ。 ゲートウェイは、オンプレミス データ ソースのみに接続します。

**Q**: ゲートウェイはデータ ソースと同じコンピューターにインストールする必要がありますか? <br/>
**A**: いいえ。 ゲートウェイは、指定された接続情報を使用してデータ ソースに接続します。 その意味ではゲートウェイをクライアント アプリケーションと見なすことができます。 ゲートウェイに必要なのは、通常は同じネットワーク上にある、指定されたサーバー名に接続する機能だけです。

<a name="why-azure-work-school-account"></a>

**Q**: サインインで職場または学校のアカウントを使用する必要があるのはなぜですか? <br/>
**A**: オンプレミスのデータ ゲートウェイをインストールするときに、Azure の職場または学校アカウントのみを使用することができます。 サインイン アカウントは、Azure Active Directory (Azure AD) によって管理されるテナントに格納されます。 通常、Azure AD アカウントのユーザー プリンシパル名 (UPN) は電子メール アドレスと一致します。

**Q**: 自分の資格情報はどこに格納されますか? <br/>
**A**: データ ソース用に入力した資格情報は、暗号化されて Gateway Cloud Service に格納されます。 資格情報の復号化はオンプレミス データ ゲートウェイで行われます。

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
**A**: Service では、ゲートウェイは、オンプレミスのデータ ゲートウェイ サービスと呼ばれています。

**Q**: ゲートウェイ Windows サービスは、Azure Active Directory アカウントを使用して実行できますか? <br/>
**A**: いいえ。 Windows サービスには有効な Windows アカウントが必要です。 既定では、Windows サービスはサービス SID の NT SERVICE\PBIEgwService を使用して実行されます。

### <a name="high-availability"></a>高可用性とディザスター リカバリー

**Q**: 障害復旧のためにはどのようなオプションを使用できますか? <br/>
**A**: 回復キーを使用して、ゲートウェイを復元または移動することができます。 ゲートウェイをインストールするときに、回復キーを指定します。

**Q**: 回復キーの利点は何ですか? <br/>
**A**: 回復キーを利用すると、災害発生後にゲートウェイの設定を移行または回復することができます。

## <a name="troubleshooting"></a>トラブルシューティング

**Q**: オンプレミスのデータ ソースに送信されるクエリはどのようにして確認できますか? <br/>
**A**: 送信されるクエリを含むクエリ トレースを有効にすることができます。 クエリ トレースは、トラブルシューティングが完了したら忘れずに元の値に戻してください。 クエリ トレースをオンのままにしておくと、ログのサイズが増大します。

クエリをトレースするためにデータ ソースに用意されているツールを使用することもできます。 たとえば、SQL Server 用の拡張イベントまたは SQL Profiler や Analysis Services を使用できます。

**Q**: ゲートウェイのログはどこにありますか? <br/>
**A**: このトピックの後半の「ログ」を参照してください。

### <a name="update"></a>最新バージョンへの更新

ゲートウェイのバージョンが古くなるにつれ、数多くの問題が表面化する可能性があります。 一般的には、常に最新バージョンを使用することが最適な対処法となります。 1 か月以上ゲートウェイを更新していない場合は、最新バージョンのゲートウェイをインストールし、問題が再発するかどうかを確認してください。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>エラー: ユーザーをグループに追加できませんでした。 (-2147463168 PBIEgwService Performance Log Users)

ドメイン コントローラー (サポート対象外) にゲートウェイをインストールしようとすると、このエラーが表示されることがあります。 ゲートウェイは、必ずドメイン コントローラー以外のコンピューターにデプロイしてください。

## <a name="logs"></a>ログ

ログ ファイルは、トラブルシューティングを行うときの重要なリソースです。

#### <a name="enterprise-gateway-service-logs"></a>エンタープライズ ゲートウェイ サービスのログ

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>構成ログ

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>イベント ログ

Data Management Gateway と PowerBIGateway のログは、**[アプリケーションとサービス ログ]** で確認できます。


## <a name="telemetry"></a>テレメトリ
監視とトラブルシューティングには、テレメトリを使用できます。 既定

**テレメトリを有効にするには**

1.  コンピューター上のオンプレミス データ ゲートウェイ クライアント ディレクトリを確認します。 通常は、**%systemdrive%\Program Files\On-premises data gateway** です。 または、サービス コンソールを開き、オンプレミス データ ゲートウェイ サービスのプロパティで実行可能ファイルのパスを確認することもできます。
2.  クライアント ディレクトリにある Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config ファイルで、 SendTelemetry の設定を true に変更します。
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  変更を保存し、その Windows サービス (オンプレミス データ ゲートウェイ サービス) を再起動します。




## <a name="next-steps"></a>次のステップ
* [Analysis Services を管理する](analysis-services-manage.md)
* [Azure Analysis Services からデータを取得する](analysis-services-connect.md)

