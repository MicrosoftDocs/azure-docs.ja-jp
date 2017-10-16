---
title: "アプリを BizTalk Services から Azure Logic Apps に移動する | Microsoft Docs"
description: "Azure BizTalk Services MABS を Logic Apps に移動または移行する"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: e58c6950d1d9420f32fc98ca917216dc5fae4fc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>BizTalk Services から Logic Apps に移動する

Microsoft Azure BizTalk Services (MABS) は廃止されました。 MABS 統合ソリューションを Azure Logic Apps に移動するには、このトピックを使用します。 

## <a name="overview"></a>概要

BizTalk Services は、次の 2 つのサブサービスで構成されています。

1.  Microsoft BizTalk Services ハイブリッド接続
2.  EAI および EDI ブリッジ ベースの統合

ハイブリッド接続の移動を検討している場合は、[Azure App Service ハイブリッド接続](../app-service/app-service-hybrid-connections.md)が、このサービスの変更と機能を説明しています。 Azure ハイブリッド接続は、BizTalk Services ハイブリッド接続を置き換えます。 Azure ハイブリッド接続は Azure App Service で使用可能であり、Azure Portal で提供されます。 Azure ハイブリッド接続はまた、既存の BizTalk Services ハイブリッド接続と、ポータルで作成する新しいハイブリッド接続を管理するための新しいハイブリッド接続マネージャーも提供します。 Azure App Service ハイブリッド接続は一般公開 (GA) されています。

EAI および EDI ブリッジ ベースの統合については、Logic Apps が置き換えになります。 Logic Apps は、BizTalk Services と同じすべての機能とその他の多くの機能を提供します。 Logic Apps は、ブラウザーを使用して、または Visual Studio のツールを使用して複雑な統合ソリューションを迅速かつ容易に構築できる、クラウド規模の消費ベースのワークフローおよびオーケストレーション機能を提供します。

次の表は、BizTalk Services 機能の Logic Apps へのマッピングを示しています。

| BizTalk Services   | Logic Apps            | 目的                  |
| ------------------ | --------------------- | ---------------------------- |
| コネクタ          | コネクタ             | データの送受信   |
| ブリッジ             | ロジック アプリ             | パイプライン プロセッサ           |
| 検証段階     | XML 検証アクション      | スキーマに対して XML ドキュメントを検証する             |
| 強化段階       | データ トークン      | プロパティをメッセージに、または決定をルーティングするために昇格させる             |
| 変換段階    | 変換アクション      | XML メッセージをある形式から別の形式に変換する             |
| デコード段階       | フラット ファイルのデコード アクション      | フラット ファイルから XML に変換する             |
| エンコード段階       |  フラット ファイルのエンコード アクション      | XML からフラット ファイルに変換する             |
| Message Inspector       |  Azure Functions または API Apps      | 統合内のカスタム コードを実行する             |
| ルート アクション      |  条件またはスイッチ      | メッセージを指定されたコネクタのいずれかにルーティングする             |

BizTalk Services にはさまざまな種類のアーティファクトがあります。

## <a name="connectors"></a>コネクタ
BizTalk Services のコネクタにより、ブリッジ (HTTP ベースの要求/応答相互通信を可能にする双方向ブリッジを含む) はデータを送受信できます。 Logic Apps では、同じ用語が使用されます。 ロジック アプリのコネクタは同じ目的に対応しており、広範囲のテクノロジやサービス (オンプレミスのデータ ゲートウェイ (BizTalk Services によって使用された BizTalk Adapter Service を置き換えます) を使用したオンプレミスと、OneDrive、Office365、Dynamics CRM などのクラウド SaaS および PaaS サービスの両方) に接続できる 140 を超えるブリッジも含みます。

BizTalk Services のソースは、FTP、SFTP、および Service Bus Queue または Topic サブスクリプションに制限されています。

![](media/logic-apps-move-from-mabs/sources.png)

各ブリッジには、既定ではブリッジの [Runtime Address] \(ランタイム アドレス) および [Relative Address] \(相対アドレス) プロパティで構成される HTTP エンドポイントがあります。 Logic Apps と同じ機能を実現するには、[要求および応答](../connectors/connectors-native-reqres.md)アクションを使用します。

## <a name="xml-processing-and-bridges"></a>XML の処理とブリッジ
BizTalk Services 内のブリッジは、処理パイプラインに似ています。 ブリッジはコネクタから受信されたデータを取得でき、そのデータに対して何からの処理を実行してから、それを別のシステムに送信します。 Logic Apps は、BizTalk Services と同じパイプライン ベースの相互通信パターンをサポートすることによって同じ処理を実行し、またその他の統合パターンもいくつか提供します。 BizTalk Services の [XML 要求-応答ブリッジ](https://msdn.microsoft.com/library/azure/hh689781.aspx)は、次を実行できる各段階から成る VETER パイプラインと呼ばれます。

* (V) 検証
* (E) 強化
* (T) 変換
* (E) 強化
* (R) ルート

次の図でわかるように、この処理は要求と応答に分かれており、要求と応答のパスを (たとえば、それぞれに異なるマップを使用して) 個別に制御できます。

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

さらに、XML 一方向ブリッジは処理の開始と終了にデコードおよびエンコード段階を追加し、パススルー ブリッジには単一の強化段階が含まれています。

### <a name="message-processing-and-decodingencoding"></a>メッセージの処理とデコード/エンコード
BizTalk Services では、ユーザーは異なる種類の XML メッセージを受信し、受信したメッセージに一致するスキーマを判定します。 これは、受信処理パイプラインの**メッセージの種類**段階で実行されます。 次に、デコード段階では検出されたメッセージの種類を使用し、提供されたスキーマを使用してそれをデコードします。 スキーマがフラットファイル スキーマである場合、そのスキーマは受信フラットファイルを XML に変換します。 

Logic Apps は、同様の機能を提供します。 ユーザーは、さまざまなコネクタ トリガー (ファイル システム、FTP、HTTP など) を使用して多数の異なるプロトコル経由でフラットファイルを受信し、[フラット ファイルのデコード](../logic-apps/logic-apps-enterprise-integration-flatfile.md) アクションを使用して受信データを XML に変換します。 何も変更しなくても、既存のフラット ファイル スキーマをロジック アプリに直接移動してから、スキーマを統合アカウントにアップロードできます。

### <a name="validation"></a>検証
受信データが XML に変換された後 (または受信されたメッセージ形式が XML であった場合)、そのメッセージが XSD スキーマに従っているかどうかを判定するために検証が実行されます。 これを Logic Apps で行うには、[XML の検証](../logic-apps/logic-apps-enterprise-integration-xml-validation.md)アクションを使用します。 この場合も、何も変更せずに BizTalk Services と同じスキーマを使用できます。

### <a name="transform-messages"></a>メッセージを変換する
BizTalk Services では、変換段階で XML ベースのあるメッセージ形式を別の形式に変換します。 これは、TRFM ベースのマッパーを使用して、マップを適用することによって実行されます。 Logic Apps でのプロセスは同様です。 変換アクションでは、統合アカウントからマップを実行します。 主な違いは、Logic Apps のマップが XSLT 形式であることです。 XSLT には、既に保有している既存の XSLT (Functoid を含む BizTalk Server 用に作成されたマップを含む) を再利用する機能が含まれています。 

### <a name="routing-rules"></a>ルーティング ルール
BizTalk Services は、どのエンドポイント/コネクタで受信メッセージ/データを送信するかというルーティングの決定を行います。 事前に構成された複数のエンドポイントのうちの 1 つを選択する機能は、ルーティング フィルター オプションを使用して可能になります。

![](media/logic-apps-move-from-mabs/route-filter.png)

Logic Apps は[条件](../logic-apps/logic-apps-use-logic-app-features.md)と[スイッチ](../logic-apps/logic-apps-switch-case.md)によるより高度なロジック機能を提供するため、高度な制御フローおよびルーティングが可能になります。 BizTalk Services でのルーティング フィルターの変換は、オプションが 2 つしか存在しない*場合*、**条件**を使用して最適に実現されます。 3 つ以上存在する場合は、**スイッチ**を使用します。

### <a name="enrich"></a>強化
BizTalk Services 処理での強化段階は、受信されたデータに関連付けられたメッセージ コンテキストにプロパティを追加します。 たとえば、ルーティングに使用するプロパティ (下で説明します) をデータベース検索から、または XPath 式を使用して値を抽出することによって昇格させます。 前のアクションからのすべてのコンテキスト データ出力へのアクセスを提供するため、同じ動作のレプリケーションが単純明快になります。 たとえば、`Get Row` SQL 接続アクションを使用して SQL Server データベースからのデータを返し、そのデータをルーティングのための決定アクションで使用します。 同様に、トリガーによってキューに入れられた受信 Service Bus メッセージに関するプロパティや、xpath ワークフロー定義言語式を使用した XPath をアドレス指定できます。

### <a name="use-custom-code"></a>カスタム コードを使用する
BizTalk Services は、独自のアセンブリでアップロードされた[カスタム コードを実行する](https://msdn.microsoft.com/library/azure/dn232389.aspx)機能を提供します。 これは、[IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx) インターフェイスによって実装されます。 ブリッジ内の各段階には、作成した .NET 型を提供するこのインターフェイスを実装する 2 つのプロパティ ([On Enter Inspector] \(開始時の Inspector) と [On Exit Inspector] \(終了時の Inspector)) が含まれています。 カスタム コードを使用すると、データに対してより複雑な処理を実行したり、一般的なビジネス ロジックを実行するアセンブリ内の既存のコードを再利用したりすることができます。 

Logic Apps には、カスタム コードを実行するための 2 つの主な方法である Azure Functions と API Apps が用意されています。 Azure Functions は作成して、ロジック アプリから呼び出すことができます。 「[Add and run custom code for logic apps through Azure Functions (Azure Functions を使用してロジック アプリのカスタム コードを追加して実行する)](../logic-apps/logic-apps-azure-functions.md)」を参照してください。 Azure App Service の一部である API Apps は、独自のトリガーやアクションを作成するために使用します。 詳細については、「[Logic Apps で使用するカスタム API の作成](../logic-apps/logic-apps-create-api-app.md)」を参照してください。 

BizTalk Services から呼び出すアセンブリにカスタム コードが含まれている場合は、実装内容に応じてこのコードを Azure Functions に移動するか、または API Apps でカスタム API を作成することができます。 たとえば、Logic Apps にコネクタが存在しない別のサービスをラップするコードがある場合は、API アプリを作成し、その API アプリが提供するアクションをロジック アプリ内で使用します。 ヘルパ関数またはライブラリがある場合は、Azure Functions が最も適している可能性があります。

### <a name="edi-processing-and-trading-partner-management"></a>EDI 処理と取引先管理
BizTalk Services には、AS2 (Applicability Statement 2)、X12、および EDIFACT のサポートを含む EDI および B2B 処理が含まれています。 Logic Apps も同様です。 BizTalk Services では、EDI ブリッジを作成し、専用の追跡および管理ポータルで取引先や契約を作成/管理します。

Logic Apps では、この機能は [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) に含まれています。 これは、EDI および B2B 処理のための統合アカウントと B2B アクションで構成されています。 [統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)は、[取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)と[契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)を作成および管理するために使用されます。 統合アカウントを作成したら、そのアカウントに 1 つ以上のロジック アプリを関連付けることができます。 関連付けたら、B2B アクションを使用して、ロジック アプリ内で取引先情報にアクセスできます。 次のアクションが提供されています。

* AS2 エンコード
* AS2 デコード
* X12 エンコード
* X12 デコード
* EDIFACT エンコード
* EDIFACT デコード

BizTalk Services とは異なり、これらのアクションはトランスポート プロトコルから分離されています。 そのため、ロジック アプリを作成するとき、データを送受信するためにどのコネクタを使用するかについての柔軟性が高くなります。 たとえば、電子メールからの添付ファイルとして X12 ファイルを受信できる場合は、これらのファイルをロジック アプリで処理します。 

## <a name="manage-and-monitor"></a>管理および監視
取引先管理だけでなく、BizTalk Services の専用ポータルは、問題を監視およびトラブルシューティングするための追跡機能を提供していました。 

Logic Apps は、[Azure Portal](../logic-apps/logic-apps-monitor-your-logic-apps.md) および [Operations Management Suite B2B ソリューション](../logic-apps/logic-apps-monitor-b2b-message.md)でより豊富な追跡および監視機能 (移動中に監視するためのモバイル アプリを含む) を提供します。

## <a name="high-availability"></a>高可用性
BizTalk Services で高可用性 (HA) を実現するには、処理の負荷を共有するために、特定のリージョンで複数のインスタンスを使用します。 ロジック アプリにはリージョン内 HA が組み込まれており、追加コストなしで使用できます。 BizTalk Services の B2B 処理のためのリージョン外障害復旧には、バックアップおよび復元プロセスが必要です。 Logic Apps では、リージョン間アクティブ/パッシブ [DR 機能](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)が提供されます。これにより、ビジネス継続性のための異なるリージョン内の統合アカウントにわたる B2B データの同期が可能になります。

## <a name="next"></a>次へ
* [Logic Apps とは](logic-apps-what-are-logic-apps.md)
* [初めてのロジック アプリの作成](logic-apps-create-a-logic-app.md)または[既製のテンプレート](logic-apps-use-logic-app-templates.md)の使い方に関するページ  
* ロジック アプリで[利用できるすべてのコネクタの一覧](../connectors/apis-list.md)
