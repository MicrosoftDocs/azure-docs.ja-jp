---
title: セキュリティ操作を Microsoft Graph Security と統合する - Azure Logic Apps
description: セキュリティ操作を Microsoft Graph Security と Azure Logic Apps を使用して管理することによって、アプリの脅威の防止、検出、および対応能力を強化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 24963a35bc3e54b2d140bf4ed1d169b213bd9b2a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673684"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>セキュリティ操作を Microsoft Graph Security および Azure Logic Apps と統合することで脅威の防止能力を強化する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) コネクタを使用して、Microsoft のセキュリティ製品、サービス、およびパートナーを統合する自動化されたワークフローを作成することで、アプリによる脅威の検出、防止、および対応方法を強化できます。 たとえば、アラートなどの Microsoft Graph Security エンティティを監視して管理する [Azure Security Center プレイブック](../security-center/security-center-playbooks.md)を作成できます。 Microsoft Graph Security コネクタによってサポートされるいくつかのシナリオを以下に示します。

* クエリまたはアラート ID に基づいてアラートを取得する。 たとえば、重大度が高いアラートを含む一覧を取得できます。
* アラートを更新する。 たとえば、アラートの割り当ての更新、アラートへのコメントの追加、アラートのタグ付けを実行できます。
* [アラート サブスクリプション (Webhook)](https://docs.microsoft.com/graph/api/resources/webhooks) を作成することで、アラートの作成または変更を監視する。
* アラート サブスクリプションを管理する。 たとえば、アクティブなサブスクリプションの取得、サブスクリプションの有効期限の延長、サブスクリプションの削除を実行できます。

ロジック アプリのワークフローでは、Microsoft Graph Security コネクタから応答を取得するアクションを使用して、その出力をワークフローの他のアクションで使用できるようにすることができます。 Microsoft Graph Security コネクタのアクションからの出力を、ワークフローの他のアクションで使用することもできます。 たとえば、Microsoft Graph Security コネクタを通して重大度が高いアラートを取得したら、これらのアラートを Outlook コネクタを使用して電子メール メッセージで送信できます。 

Microsoft Graph Security の詳細については、「[Microsoft Graph Security API の概要](https://aka.ms/graphsecuritydocs)」を参照してください。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。 Microsoft Flow または PowerApps については、[Microsoft Flow に関するサイト](https://flow.microsoft.com/)、 または [PowerApps に関するサイト](https://powerapps.microsoft.com/)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* Microsoft Graph Security コネクタを使用するには、Azure Active Directory (AD) テナント管理者によって "*明示的に与えられた*" 同意が必要です。これは [Microsoft Graph Security の認証要件](https://aka.ms/graphsecurityauth)の一部です。 この同意には、Microsoft Graph Security コネクタのアプリケーション ID と名前が必要です。これらは [Azure portal](https://portal.azure.com) でも見つけることができます。

   | プロパティ | 値 |
   |----------|-------|
   | **アプリケーション名** | `MicrosoftGraphSecurityConnector` |
   | **アプリケーション ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Azure AD テナント管理者は、次のいずれかの手順に従って、コネクタに同意を与えることができます。

   * [Azure AD アプリケーションにテナント管理者の同意を与える](../active-directory/develop/v2-permissions-and-consent.md)。

   * ロジック アプリの初回の実行時に、[アプリケーションの同意エクスペリエンス](../active-directory/develop/application-consent-experience.md)を通して、Azure AD テナント管理者の同意を要求できます。
   
* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Microsoft Graph Security のエンティティ (アラートなど) にアクセスするロジック アプリ。 現時点では、このコネクタにはトリガーがありません。 そのため、Microsoft Graph Security アクションを使用するには、**Recurrence** トリガーなどのトリガーでロジック アプリを開始します。

## <a name="connect-to-microsoft-graph-security"></a>Microsoft Graph Security に接続する 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com/) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 空のロジック アプリでは、Microsoft Graph Security アクションを追加する前に、トリガーとその他のアクションを追加します。

   または

   既存のロジック アプリでは、Microsoft Graph Security アクションを追加する最後のステップの下で、**[新しいステップ]** を選択します。

   または

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス (+) 記号を選択し、**[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「microsoft graph security」と入力します。 アクションの一覧から、目的のアクションを選択します。

1. Microsoft Graph Security の資格情報でサインインします。

1. 選択したアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="add-actions"></a>アクションの追加

ここでは、Microsoft Graph Security コネクタで利用できるさまざまなアクションの使用についての詳細を説明します。

### <a name="manage-alerts"></a>Manage alerts

フィルター処理、並べ替え、または最新の結果の取得を行うには、[Microsoft Graph でサポートされている ODATA クエリ パラメーター](https://docs.microsoft.com/graph/query-parameters) "*だけ*" を指定します。 完全なベース URL または HTTP アクション (`https://graph.microsoft.com/v1.0/security/alerts`、`GET` 操作、`PATCH` 操作など) は "*指定しないでください*"。 重大度が高いアラートの一覧を取得する場合の**アラートの取得**アクションのパラメーターの例は次のとおりです。

`Filter alerts value as Severity eq 'high'`

このコネクタで使用できるクエリの詳細については、[Microsoft Graph Security アラート リファレンス ドキュメント](https://docs.microsoft.com/graph/api/alert-list)を参照してください。 このコネクタを使用して強化されたエクスペリエンスを構築するには、コネクタでサポートされている[スキーマ プロパティのアラート](https://docs.microsoft.com/graph/api/resources/alert)の詳細を確認してください。

| Action | 説明 |
|--------|-------------|
| **アラートの取得** | 1 つまたは複数の[アラートのプロパティ](https://docs.microsoft.com/graph/api/resources/alert)に基づいてフィルター処理されたアラートを取得します。例: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **ID によるアラートの取得** | アラート ID に基づいて特定のアラートを取得します。 | 
| **アラートの更新** | アラート ID に基づいて特定のアラートを更新します。 <p>必須のプロパティと編集可能なプロパティを要求に確実に渡すには、[アラートの編集可能なプロパティ](https://docs.microsoft.com/graph/api/alert-update)を参照してください。 たとえば、アラートをセキュリティ分析に割り当てて調査できるようにするには、アラートの **Assigned to** プロパティを更新できます。 |
|||

### <a name="manage-alert-subscriptions"></a>アラート サブスクリプションを管理する

Microsoft Graph では、"[*サブスクリプション*](https://docs.microsoft.com/graph/api/resources/subscription)" ([*Webhook*](https://docs.microsoft.com/graph/api/resources/webhooks)) がサポートされています。 サブスクリプションを取得、更新、または削除するには、[Microsoft Graph でサポートされている ODATA クエリ パラメーター](https://docs.microsoft.com/graph/query-parameters)を Microsoft Graph エンティティ コンストラクトに指定し、`security/alerts` と ODATA クエリを含めます。 
ベース URL (`https://graph.microsoft.com/v1.0` など) は "*含めないでください*"。 代わりに、次の例の形式を使用してください。

`security/alerts?$filter=status eq 'New'`

| Action | 説明 |
|--------|-------------|
| **サブスクリプションを作成する** | 変更について通知する[サブスクリプションを作成します](https://docs.microsoft.com/graph/api/subscription-post-subscriptions)。 特定のアラートの種類用にこのサブスクリプションをフィルター処理できます。 たとえば、重大度が高いアラートについて通知するサブスクリプションを作成できます。 |
| **アクティブなサブスクリプションを取得する** | [有効期限が切れていないサブスクリプションを取得します](https://docs.microsoft.com/graph/api/subscription-list)。 | 
| **サブスクリプションを更新する** | サブスクリプション ID を指定して、[サブスクリプションを更新します](https://docs.microsoft.com/graph/api/subscription-update)。 たとえば、サブスクリプションを延長するには、サブスクリプションの `expirationDateTime` プロパティを更新できます。 | 
| **サブスクリプションを削除する** | サブスクリプション ID を指定して、[サブスクリプションを削除します](https://docs.microsoft.com/graph/api/subscription-delete)。 | 
||| 

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](https://aka.ms/graphsecurityconnectorreference)を参照してください。

## <a name="get-support"></a>サポートを受ける

質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
