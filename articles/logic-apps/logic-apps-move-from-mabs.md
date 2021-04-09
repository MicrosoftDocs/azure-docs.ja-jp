---
title: アプリを BizTalk Services から Azure Logic Apps に移行する
description: アプリとソリューションを Microsoft Azure BizTalk Services (MABS) から Azure Logic Apps に移動する方法
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 6c07ab4b18c017bd29723d2640129b8e67374e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023654"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>アプリとソリューションを BizTalk Services から Azure Logic Apps に移行する

Microsoft Azure BizTalk Services (MABS) は廃止されました。 MABS 統合ソリューションを [Azure Logic Apps](../logic-apps/logic-apps-overview.md) に移行するときは、この記事のガイダンスに従ってください。 

## <a name="introduction"></a>はじめに

BizTalk Services は、次の 2 つのサブサービスで構成されています。

* Microsoft BizTalk Services ハイブリッド接続
* EAI および EDI ブリッジ ベースの統合

[Azure App Service ハイブリッド接続](../app-service/app-service-hybrid-connections.md)は、BizTalk Services ハイブリッド接続に代わるものです。 Azure ハイブリッド接続は、Azure Portal を通じて Azure App Service で使用できます。 このサービスは、既存の BizTalk Services ハイブリッド接続に加え、ポータルで作成する新しいハイブリッド接続も管理できるハイブリッド接続マネージャーを提供します。 

[Logic Apps](../logic-apps/logic-apps-overview.md) は、EAI および EDI ブリッジ ベースの統合に代わるものであり、BizTalk Services のすべての機能とさらに多くの機能を備えています。 このサービスは、ブラウザーまたは Visual Studio を使用して複雑な統合ソリューションを迅速かつ簡単に構築できる、クラウド規模の使用ベースのワークフローおよびオーケストレーション機能を提供します。

次の表では、BizTalk Services の機能を Logic Apps にマップしています。

| BizTalk Services   | Logic Apps            | 目的                      |
| ------------------ | --------------------- | ---------------------------- |
| コネクタ          | コネクタ             | データを送受信する   |
| ブリッジ             | ロジック アプリ             | パイプライン プロセッサ           |
| 検証段階     | XML 検証アクション | スキーマに対して XML ドキュメントを検証する | 
| 強化段階       | データ トークン           | プロパティをメッセージに、または決定をルーティングするために昇格させる |
| 変換段階    | 変換アクション      | XML メッセージをある形式から別の形式に変換する |
| デコード段階       | フラット ファイルのデコード アクション | フラット ファイルから XML に変換する |
| エンコード段階       | フラット ファイルのエンコード アクション | XML からフラット ファイルに変換する |
| Message Inspector  | Azure Functions または API Apps | 統合内のカスタム コードを実行する |
| ルート アクション       | 条件またはスイッチ | メッセージを指定されたコネクタのいずれかにルーティングする |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services のアーティファクト

BizTalk Services には、数種類のアーティファクトがあります。 

## <a name="connectors"></a>Connectors

BizTalk Services のコネクタにより、HTTP ベースの要求/応答の対話を可能にする双方向ブリッジを含め、各ブリッジはデータを送受信できます。 Logic Apps でも同じ用語が使用されており、さまざまなテクノロジやサービスに接続することで同じ目的を果たす数百個のコネクタがあります。 たとえば、クラウド SaaS および PaaS サービス (OneDrive、Office365、Dynamics CRM など) 用のコネクタに加え、オンプレミス データ ゲートウェイ (BizTalk Services の BizTalk Adapter サービスに代わるもの) を介したオンプレミス システム用のコネクタも用意されています。 BizTalk Services のソースは、FTP、SFTP、および Service Bus Queue または Topic サブスクリプションに制限されています。

![BizTalk Services のフローを示す図。](media/logic-apps-move-from-mabs/sources.png)

既定では、各ブリッジには、ブリッジの [Runtime Address]\(ランタイム アドレス\) および [Relative Address]\(相対アドレス\) プロパティで構成される HTTP エンドポイントがあります。 Logic Apps で同じ結果を得るには、[要求および応答](../connectors/connectors-native-reqres.md)アクションを使用します。

## <a name="xml-processing-and-bridges"></a>XML の処理とブリッジ

BizTalk Services では、ブリッジは処理パイプラインに似ています。 ブリッジはコネクタから受信したデータを取得し、そのデータで何らかの処理を実行して、結果を別のシステムに送信できます。 Logic Apps では、BizTalk Services と同じパイプライン ベースの対話パターンをサポートし、他の統合パターンも提供することによって、同じ処理を実行します。 BizTalk Services の [XML 要求-応答ブリッジ](/previous-versions/azure/hh689781(v=azure.100))は、VETER パイプラインと呼ばれます。これは、次のタスクを実行する各段階で構成されます。

* (V) 検証
* (E) 強化
* (T) 変換
* (E) 強化
* (R) ルート

次の図に示すように、処理は要求と応答に分かれており、たとえば、パスごとに異なるマップを使用して、要求と応答のパスを個別に制御できます。

![要求と応答の間で処理がどのように分割されるかを示すスクリーンショット。](media/logic-apps-move-from-mabs/xml-request-reply.png)

また、XML 一方向ブリッジでは、処理の開始時と終了時にデコード段階とエンコード段階が追加されます。 パススルー ブリッジには、1 つの強化段階が含まれます。

### <a name="message-processing-decoding-and-encoding"></a>メッセージの処理、デコード、エンコード

BizTalk Services では、さまざまな種類の XML メッセージを受信し、受信したメッセージの一致するスキーマを特定できます。 この処理は、受信処理パイプラインの "*メッセージの種類*" 段階で実行されます。 次に、デコード段階では、検出されたメッセージの種類を使用し、提供されたスキーマを使用してメッセージをデコードします。 スキーマがフラット ファイル スキーマの場合は、この段階で受信フラット ファイルが XML に変換されます。 

Logic Apps は、同様の機能を提供します。 さまざまなコネクタ トリガー (ファイル システム、FTP、HTTP など) を使用して、さまざまなプロトコル経由でフラット ファイルを受信し、[フラット ファイルのデコード](../logic-apps/logic-apps-enterprise-integration-flatfile.md) アクションを使用して受信データを XML に変換します。 何も変更せずに既存のフラット ファイル スキーマを Logic Apps に直接移動し、スキーマを統合アカウントにアップロードできます。

### <a name="validation"></a>検証

受信データが XML に変換された後 (または受信されたメッセージ形式が XML であった場合)、そのメッセージが XSD スキーマに従っているかどうかを判定するために検証が実行されます。 Logic Apps でこのタスクを実行するには、[XML の検証](../logic-apps/logic-apps-enterprise-integration-xml-validation.md)アクションを使用します。 何も変更せずに、BizTalk Services の同じスキーマをご利用いただけます。

### <a name="transform-messages"></a>メッセージを変換する

BizTalk Services では、変換段階で XML ベースのあるメッセージ形式を別の形式に変換します。 この処理は、TRFM ベースのマッパーを使用して、マップを適用することによって実行されます。 Logic Apps でのプロセスは同様です。 変換アクションでは、統合アカウントからマップを実行します。 主な違いは、Logic Apps のマップが XSLT 形式であることです。 XSLT には、既に保有している既存の XSLT (Functoid を含む BizTalk Server 用に作成されたマップを含む) を再利用する機能が含まれています。 

### <a name="routing-rules"></a>ルーティング ルール

BizTalk Services は、どのエンドポイントまたはコネクタで受信メッセージやデータを送信するかというルーティングを決定します。 ルーティング フィルター オプションを使用することで、事前に構成されたエンドポイントの中から選択できます。

![ルーティング フィルター オプションを示すスクリーンショット。](media/logic-apps-move-from-mabs/route-filter.png)

BizTalk Services では、オプションが 2 つしかない場合は、BizTalk Services でのルーティング フィルターの変換に最適な方法として、"*条件*" を使用します。 3 つ以上存在する場合は、**スイッチ** を使用します。

Logic Apps は高度なロジック機能を備え、[条件付きステートメント](../logic-apps/logic-apps-control-flow-conditional-statement.md)と [switch ステートメント](../logic-apps/logic-apps-control-flow-switch-statement.md)を使用した高度な制御フローおよびルーティングを提供します。

### <a name="enrich"></a>強化

BizTalk Services の処理では、強化段階で、受信したデータに関連付けられているメッセージ コンテキストにプロパティが追加されます。 たとえば、データベース検索を使用するか、または XPath 式を使用して値を抽出することによって、ルーティングに使用するプロパティを昇格させます。 前のアクションからのすべてのコンテキスト データ出力へのアクセスを提供するため、同じ動作のレプリケーションが単純明快になります。 たとえば、`Get Row` SQL 接続アクションを使用して SQL Server データベースからのデータを返し、そのデータをルーティングのための決定アクションで使用します。 同様に、トリガーによってキューに入れられた受信 Service Bus メッセージに関するプロパティや、xpath ワークフロー定義言語式を使用した XPath をアドレス指定できます。

### <a name="run-custom-code"></a>カスタム コードの実行

BizTalk Services では、独自のアセンブリでアップロードされた[カスタム コードを実行](/previous-versions/azure/dn232389(v=azure.100))できます。 この機能は、[IMessageInspector]() インターフェイスによって実装されます。 ブリッジの各段階には、このインターフェイスを実装する作成済みの .NET 型を提供する、2 つのプロパティ ([On Enter Inspector]\(開始時の Inspector\) と [On Exit Inspector]\(終了時の Inspector\)) が含まれています。 カスタム コードを使用すると、データに対してより複雑な処理を実行したり、一般的なビジネス ロジックを実行するアセンブリ内の既存のコードを再利用したりできます。 

Logic Apps には、カスタム コードを実行するための 2 つの主な方法が用意されています。Azure Functions と API Apps です。 Azure Functions は作成して、ロジック アプリから呼び出すことができます。 「[Add and run custom code for logic apps through Azure Functions (Azure Functions を使用してロジック アプリのカスタム コードを追加して実行する)](../logic-apps/logic-apps-azure-functions.md)」を参照してください。 Azure App Service の一部である API Apps は、独自のトリガーやアクションを作成するために使用します。 Logic Apps で使用するカスタム API の作成の詳細については、[こちら](../logic-apps/logic-apps-create-api-app.md)をご覧ください。 

BizTalk Services から呼び出すアセンブリにカスタム コードが含まれている場合は、実装内容に応じて、このコードを Azure Functions に移動することも、API Apps でカスタム API を作成することもできます。 たとえば、Logic Apps にコネクタがない別のサービスをラップするコードがある場合は、API アプリを作成し、その API アプリが提供するアクションをロジック アプリ内で使用します。 ヘルパ関数またはライブラリがある場合は、Azure Functions が最も適している可能性があります。

### <a name="edi-processing-and-trading-partner-management"></a>EDI 処理と取引先管理

BizTalk Services と Logic Apps には、AS2 (Applicability Statement 2)、X12、EDIFACT をサポートする EDI および B2B 処理が含まれています。 BizTalk Services では、EDI ブリッジを作成し、専用の追跡および管理ポータルで取引先や契約を作成または管理します。
Logic Apps では、[Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md) を通じてこの機能を入手します。 EIP は、EDI および B2B 処理のための[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)と B2B アクションを提供します。 また、統合アカウントを使用して、[取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)と[契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)を作成および管理します。 統合アカウントを作成したら、そのアカウントに 1 つ以上のロジック アプリをリンクできます。 その後、B2B アクションを使用して、ロジック アプリから取引先情報にアクセスできます。 次のアクションが提供されています。

* AS2 エンコード
* AS2 デコード
* X12 エンコード
* X12 デコード
* EDIFACT エンコード
* EDIFACT デコード

BizTalk Services とは異なり、これらのアクションはトランスポート プロトコルから分離されています。 そのため、ロジック アプリを作成するときに、データの送受信に使用するコネクタについて柔軟性が高まります。 たとえば、電子メールの添付ファイルとして X12 ファイルを受信し、これらのファイルをロジック アプリで処理できます。 

## <a name="manage-and-monitor"></a>管理および監視

BizTalk Services では、専用ポータルに、問題の監視とトラブルシューティングのための追跡機能が用意されていました。 Logic Apps では、[Azure portal 内のロジック アプリを監視する](../logic-apps/monitor-logic-apps.md)より豊富な追跡および監視機能が提供されます。これには、移動中に監視するためのモバイル アプリも含まれます。

## <a name="high-availability"></a>高可用性

BizTalk Services で高可用性 (HA) を実現するには、特定のリージョンで複数のインスタンスを使用して処理の負荷を分担します。 Logic Apps では、追加コストなしでリージョン内 HA を提供します。 

BizTalk Services では、B2B 処理のためのリージョン外ディザスター リカバリーにはバックアップおよび復元プロセスが必要です。 ビジネス継続性を実現するために、Logic Apps では、リージョン間アクティブ/パッシブ [DR 機能](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)を提供します。この機能により、さまざまなリージョンの統合アカウント間で B2B データの同期が可能になります。

## <a name="next-steps"></a>次のステップ

* [Logic Apps とは](../logic-apps/logic-apps-overview.md)
* [初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)または[既製のテンプレート](../logic-apps/logic-apps-create-logic-apps-from-templates.md)の使い方に関するページ  
* ロジック アプリで[利用できるすべてのコネクタの一覧](../connectors/apis-list.md)