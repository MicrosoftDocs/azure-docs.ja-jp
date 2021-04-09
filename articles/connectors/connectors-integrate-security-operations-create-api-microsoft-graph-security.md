---
title: セキュリティ操作の統合と管理と Microsoft Graph Security
description: Microsoft Graph Security と Azure Logic Apps を使用して、アプリの脅威の防止、検出、および対応を強化します
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: a83cd68df2f1d722517d6239bf6959075860d0b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888540"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>セキュリティ操作を Microsoft Graph Security および Azure Logic Apps と統合することで脅威の防止能力を強化する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Microsoft Graph Security](/graph/security-concept-overview) コネクタを使用して、Microsoft のセキュリティ製品、サービス、およびパートナーを統合する自動化されたワークフローを作成することで、アプリによる脅威の検出、防止、および対応方法を強化できます。 たとえば、アラートなどの Microsoft Graph Security エンティティを監視して管理する [Azure Security Center プレイブック](../security-center/workflow-automation.md)を作成できます。 Microsoft Graph Security コネクタによってサポートされるいくつかのシナリオを以下に示します。

* クエリまたはアラート ID に基づいてアラートを取得する。 たとえば、重大度が高いアラートを含む一覧を取得できます。

* アラートを更新する。 たとえば、アラートの割り当ての更新、アラートへのコメントの追加、アラートのタグ付けを実行できます。

* [アラート サブスクリプション (Webhook)](/graph/api/resources/webhooks) を作成することで、アラートの作成または変更を監視する。

* アラート サブスクリプションを管理する。 たとえば、アクティブなサブスクリプションの取得、サブスクリプションの有効期限の延長、サブスクリプションの削除を実行できます。

ロジック アプリのワークフローでは、Microsoft Graph Security コネクタから応答を取得するアクションを使用して、その出力をワークフローの他のアクションで使用できるようにすることができます。 Microsoft Graph Security コネクタのアクションからの出力を、ワークフローの他のアクションで使用することもできます。 たとえば、Microsoft Graph Security コネクタを通して重大度が高いアラートを取得したら、これらのアラートを Outlook コネクタを使用して電子メール メッセージで送信できます。 

Microsoft Graph Security の詳細については、「[Microsoft Graph Security API の概要](/graph/security-concept-overview)」を参照してください。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。 Power Automate または PowerApps について詳しい情報をお探しの場合は、[Power Automate](https://flow.microsoft.com/) または [Power Apps](https://powerapps.microsoft.com/) のサイトをご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Microsoft Graph Security コネクタを使用するには、Azure Active Directory (AD) テナント管理者によって "*明示的に与えられた*" 同意が必要です。これは [Microsoft Graph Security の認証要件](/graph/security-authorization)の一部です。 この同意には、Microsoft Graph Security コネクタのアプリケーション ID と名前が必要です。これらは [Azure portal](https://portal.azure.com) でも見つけることができます。

  | プロパティ | 値 |
  |----------|-------|
  | **アプリケーション名** | `MicrosoftGraphSecurityConnector` |
  | **アプリケーション ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Azure AD テナント管理者は、次のいずれかの手順に従って、コネクタに同意を与えることができます。

  * [Azure AD アプリケーションにテナント管理者の同意を与える](../active-directory/develop/v2-permissions-and-consent.md)。

  * ロジック アプリの初回の実行時に、[アプリケーションの同意エクスペリエンス](../active-directory/develop/application-consent-experience.md)を通して、Azure AD テナント管理者の同意を要求できます。
   
* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Microsoft Graph Security のエンティティ (アラートなど) にアクセスするロジック アプリ。 Microsoft Graph Security トリガーを使用するには、空のロジック アプリが必要です。 Microsoft Graph Security アクションを使用するには、シナリオに適したトリガーによって開始されるロジック アプリが必要です。

## <a name="connect-to-microsoft-graph-security"></a>Microsoft Graph Security に接続する 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com/) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 空のロジック アプリでは、Microsoft Graph Security アクションを追加する前に、トリガーとその他の必要なアクションを追加します。

   または

   既存のロジック アプリでは、Microsoft Graph Security アクションを追加する最後のステップの下で、 **[新しいステップ]** を選択します。

   または

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 (+) を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「microsoft graph security」と入力します。 アクションの一覧から、目的のアクションを選択します。

1. Microsoft Graph Security の資格情報でサインインします。

1. 選択したアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="add-triggers"></a>トリガーの追加

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

> [!NOTE] 
> トリガーが発生すると、すべての新しいアラートがトリガーによって処理されます。 アラートを受信しなかった場合、トリガーの実行はスキップされます。 次のトリガーのポーリングは、トリガーのプロパティで指定する繰り返し間隔に基づいて発生します。

この例では、新しいアラートがアプリに送信されたときに、ロジック アプリ ワークフローを開始する方法を示します。

1.  Azure portal または Visual Studio で、ロジック アプリ デザイナーを開いて、空のロジック アプリを作成します。 この例では、Azure Portal を使用します。

1.  デザイナーで、検索ボックスにフィルターとして「microsoft graph security」と入力します。 トリガーの一覧から、 **[On all new alerts]\(すべての新しいアラートに対して\)** を選択します

1.  トリガーには、監視するアラートに関する情報を提供します。 その他のプロパティについては、 **[新しいパラメーターの追加]** 一覧を開き、パラメーターを選択して、そのプロパティをトリガーに追加します。

   | プロパティ | プロパティ (JSON) | 必須 | Type | 説明 |
   |----------|-----------------|----------|------|-------------|
   | **間隔** | `interval` | はい | Integer | ワークフローの実行間隔を、[頻度] に指定された単位に基づいて表す正の整数。 間隔の最小値と最大値は次のとおりです。 <p><p>- Month: 1 から 16 か月 <br>- Day: 1 から 500 日 <br>- Hour: 1 から 12,000 時間 <br>- Minute: 1 から 72,000 分 <br>- Second: 1 から 9,999,999 秒 <p>たとえば間隔が 6 で、頻度が "月" である場合は、繰り返しは 6 か月ごとになります。 |
   | **頻度** | `frequency` | はい | String | 繰り返しの時間の単位: **[秒]** 、 **[分]** 、 **[時間]** 、 **[日]** 、 **[週]** 、または **[月]** |
   | **タイム ゾーン** | `timeZone` | いいえ | String | 開始時刻を指定したときに限り適用されます。このトリガーに [UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を指定することはできないためです。 適用するタイム ゾーンを選択してください。 |
   | **[開始時刻]** | `startTime` | いいえ | String | 開始日時を次の形式で指定します。 <p><p>YYYY-MM-DDThh:mm:ss (タイム ゾーンを選択した場合) <p>または <p>YYYY-MM-DDThh:mm:ssZ (タイム ゾーンを選択しなかった場合) <p>たとえば、2017 年 9 月 18 日午後 2:00 にする場合は、"2017-09-18T14:00:00" と指定し、太平洋標準時などのタイム ゾーンを選択します。 または、タイム ゾーンなしで「2017-09-18T14:00:00Z」と指定します。 <p>**注:** この開始時刻には、最大で 49 年先の時刻を指定できます。また、[UTC の日付と時刻の形式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) (ただし、[UTC オフセット](https://en.wikipedia.org/wiki/UTC_offset)を除く) で [日付と時刻に関する ISO 8601 規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)に従っている必要があります。 タイム ゾーンを選択しない場合は、末尾にスペースを入れず、アルファベットの "Z" を追加してください。 この "Z" は、同等の[航海時間](https://en.wikipedia.org/wiki/Nautical_time)を表します。 <p>単純なスケジュールでは、開始時刻と最初の実行時刻が一致するのに対して、複雑なスケジュールでは、トリガーが作動するのは開始時刻以降となります。 "[*開始日時の使用方法を具体的に教えてください*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)" |
   ||||||

1.  操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

1.  トリガーの結果を使用して実行するタスクの 1 つまたは複数のアクションをロジック アプリに追加する操作に進みます。

## <a name="add-actions"></a>アクションの追加

ここでは、Microsoft Graph Security コネクタで利用できるさまざまなアクションの使用についての詳細を説明します。

### <a name="manage-alerts"></a>Manage alerts

フィルター処理、並べ替え、または最新の結果の取得を行うには、[Microsoft Graph でサポートされている ODATA クエリ パラメーター](/graph/query-parameters) "*だけ*" を指定します。 完全なベース URL または HTTP アクション (`https://graph.microsoft.com/v1.0/security/alerts`、`GET` 操作、`PATCH` 操作など) は "*指定しないでください*"。 重大度が高いアラートの一覧を取得する場合の **アラートの取得** アクションのパラメーターの例は次のとおりです。

`Filter alerts value as Severity eq 'high'`

このコネクタで使用できるクエリの詳細については、[Microsoft Graph Security アラート リファレンス ドキュメント](/graph/api/alert-list)を参照してください。 このコネクタを使用して強化されたエクスペリエンスを構築するには、コネクタでサポートされている[スキーマ プロパティのアラート](/graph/api/resources/alert)の詳細を確認してください。

| アクション | 説明 |
|--------|-------------|
| **アラートの取得** | 1 つまたは複数の[アラートのプロパティ](/graph/api/resources/alert)に基づいてフィルター処理されたアラートを取得します。例: `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`。 | 
| **ID によるアラートの取得** | アラート ID に基づいて特定のアラートを取得します。 | 
| **アラートの更新** | アラート ID に基づいて特定のアラートを更新します。 必須のプロパティと編集可能なプロパティを要求に確実に渡すには、[アラートの編集可能なプロパティ](/graph/api/alert-update)を参照してください。 たとえば、アラートをセキュリティ分析に割り当てて調査できるようにするには、アラートの **Assigned to** プロパティを更新できます。 |
|||

### <a name="manage-alert-subscriptions"></a>アラート サブスクリプションを管理する

Microsoft Graph では、"[*サブスクリプション*](/graph/api/resources/subscription)" ([*Webhook*](/graph/api/resources/webhooks)) がサポートされています。 サブスクリプションを取得、更新、または削除するには、[Microsoft Graph でサポートされている ODATA クエリ パラメーター](/graph/query-parameters)を Microsoft Graph エンティティ コンストラクトに指定し、`security/alerts` と ODATA クエリを含めます。 ベース URL (`https://graph.microsoft.com/v1.0` など) は "*含めないでください*"。 代わりに、次の例の形式を使用してください。

`security/alerts?$filter=status eq 'NewAlert'`

| アクション | 説明 |
|--------|-------------|
| **サブスクリプションを作成する** | 変更について通知する[サブスクリプションを作成します](/graph/api/subscription-post-subscriptions)。 特定のアラートの種類用にこのサブスクリプションをフィルター処理できます。 たとえば、重大度が高いアラートについて通知するサブスクリプションを作成できます。 |
| **アクティブなサブスクリプションを取得する** | [有効期限が切れていないサブスクリプションを取得します](/graph/api/subscription-list)。 | 
| **サブスクリプションを更新する** | サブスクリプション ID を指定して、[サブスクリプションを更新します](/graph/api/subscription-update)。 たとえば、サブスクリプションを延長するには、サブスクリプションの `expirationDateTime` プロパティを更新できます。 | 
| **サブスクリプションを削除する** | サブスクリプション ID を指定して、[サブスクリプションを削除します](/graph/api/subscription-delete)。 | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>脅威インテリジェンス インジケーターを管理する

フィルター処理、並べ替え、または最新の結果の取得を行うには、[Microsoft Graph でサポートされている ODATA クエリ パラメーター](/graph/query-parameters) "*だけ*" を指定します。 完全なベース URL または HTTP アクション (`https://graph.microsoft.com/beta/security/tiIndicators`、`GET` 操作、`PATCH` 操作など) は "*指定しないでください*"。 脅威の種類が `DDoS` であるインジケーターの一覧を取得する場合の **脅威インテリジェンス インジケーターの取得** アクションのパラメーターの具体例を次に示します。

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

このコネクタで使用できるクエリの詳細については、[Microsoft Graph セキュリティ脅威インテリジェンス インジケーター リファレンス ドキュメントの「オプションのクエリ パラメーター」](/graph/api/tiindicators-list)を参照してください。 このコネクタを使用して強化されたエクスペリエンスを構築するには、コネクタでサポートされている[スキーマ プロパティの脅威インテリジェンス インジケーター](/graph/api/resources/tiindicator)の詳細を確認してください。

| アクション | 説明 |
|--------|-------------|
| **脅威インテリジェンス インジケーターを取得する** | 1 つまたは複数の[脅威インテリジェンス インジケーターのプロパティ](/graph/api/resources/tiindicator)に基づいてフィルター処理された脅威インテリジェンス インジケーターを取得します。例: `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **脅威インテリジェンス インジケーターを ID で取得する** | 脅威インテリジェンス インジケーター ID に基づいて特定の脅威インテリジェンス インジケーターを取得します。 | 
| **脅威インテリジェンス インジケーターを作成する** | 脅威インテリジェンス インジケーター コレクションに投稿することで、新しい 脅威インテリジェンス インジケーターを作成します。 必須プロパティを要求に確実に渡すには、[脅威インテリジェンス インジケーターを作成するための必須プロパティ](/graph/api/tiindicators-post)に関する記事を参照してください。 |
| **複数の脅威インテリジェンス インジケーターを送信する** | 脅威インテリジェンス インジケーターのコレクションを投稿することで、複数の新しい脅威インテリジェンス インジケーターを作成します。 必須プロパティを要求に確実に渡すには、[複数の脅威インテリジェンス インジケーターを送信するための必須プロパティ](/graph/api/tiindicator-submittiindicators)に関する記事を参照してください。 |
| **脅威インテリジェンス インジケーターを更新する** | 脅威インテリジェンス インジケーター ID に基づいて特定の脅威インテリジェンス インジケーターを更新します。 必須プロパティと編集可能なプロパティを要求に確実に渡すには、[脅威インテリジェンス インジケーターの編集可能なプロパティ](/graph/api/tiindicator-update)に関する記事を参照してください。 たとえば、targetProduct セキュリティ ツール内でインジケーターが一致した場合に適用するアクションを更新するには、脅威インテリジェンス インジケーターの **action** プロパティを更新します。 |
| **複数の脅威インテリジェンス インジケーターを更新する** | 複数の脅威インテリジェンス インジケーターを更新します。 必須プロパティを要求に確実に渡すには、[複数の脅威インテリジェンス インジケーターを更新するための必須プロパティ](/graph/api/tiindicator-updatetiindicators)に関する記事を参照してください。 |
| **脅威インテリジェンス インジケーターを ID で削除する** | 脅威インテリジェンス インジケーター ID に基づいて特定の脅威インテリジェンス インジケーターを削除します。 |
| **複数の脅威インテリジェンス インジケーターを ID で削除する** | 複数の脅威インテリジェンス インジケーターを ID で削除します。 必須プロパティを要求に確実に渡すには、[複数の脅威インテリジェンス インジケーターを ID で削除するための必須プロパティ](/graph/api/tiindicator-deletetiindicators)に関する記事を参照してください。 |
| **複数の脅威インテリジェンス インジケーターを外部 ID で削除する** | 複数の脅威インテリジェンス インジケーターを外部 ID で削除します。 必須プロパティを要求に確実に渡すには、[複数の脅威インテリジェンス インジケーターを外部 ID で削除するための必須プロパティ](/graph/api/tiindicator-deletetiindicatorsbyexternalid)に関する記事を参照してください。 |
|||

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/microsoftgraphsecurity/)を参照してください。

## <a name="next-steps"></a>次のステップ

他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
