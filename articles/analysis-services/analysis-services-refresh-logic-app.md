---
title: Logic Apps を使用した Azure Analysis Services モデルの更新 | Microsoft Docs
description: この記事では、Azure Logic Apps を使用して Azure Analysis Services の非同期更新をコーディングする方法について説明します。
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: 8a8d434fca7cab4432f38fc64093cf1fe060bd5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019088"
---
# <a name="refresh-with-logic-apps"></a>Logic Apps を使用した更新

Logic Apps と REST 呼び出しを使用すると、Azure Analysis 表形式モデルで自動データ更新操作 (クエリのスケール アウトの読み取り専用レプリカの同期など) を実行することができます。

Azure Analysis Services での REST API の使用に関する詳細については、「[REST API を使用した非同期更新](analysis-services-async-refresh.md)」を参照してください。

## <a name="authentication"></a>認証

すべての呼び出しを、有効な Azure Active Directory (OAuth 2) トークンで認証する必要があります。  この記事の例では、サービス プリンシパル (SPN) を使用して Azure Analysis Services を認証します。 詳細については、[Azure portal を使用したサービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

## <a name="design-the-logic-app"></a>ロジック アプリを設計する

> [!IMPORTANT]
> 次の例では、Azure Analysis Services ファイアウォールが無効になっていることを前提としています。 ファイアウォールが有効になっている場合は、要求イニシエーターのパブリック IP アドレスが、Azure Analysis Services ファイアウォールで許可リストに追加されている必要があります。 リージョンごとの Azure Logic Apps の IP 範囲の詳細については、「[Azure Logic Apps の制限と構成情報](../logic-apps/logic-apps-limits-and-config.md#configuration)」を参照してください。

### <a name="prerequisites"></a>前提条件

#### <a name="create-a-service-principal-spn"></a>サービス プリンシパル (SPN) を作成する

サービス プリンシパルの作成の詳細については、[Azure portal を使用したサービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

#### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services でアクセス許可を構成する
 
作成するサービス プリンシパルには、サーバーでのサーバー管理者のアクセス許可が必要です。 詳細については、「[サーバー管理者ロールへのサービス プリンシパルの追加](analysis-services-addservprinc-admins.md)」を参照してください。

### <a name="configure-the-logic-app"></a>ロジック アプリを構成する

この例では、HTTP 要求を受信したときにトリガーするようにロジック アプリが設計されています。 これにより、Azure Analysis Services モデルの更新をトリガーするため、Azure Data Factory などのオーケストレーション ツールの使用が有効になります。

ロジック アプリを作成したら、次を実行します。

1. ロジック アプリ デザイナーで、 **[HTTP 要求の受信時]** の最初のアクションを選択します。

   ![受信した HTTP アクティビティを追加する](./media/analysis-services-async-refresh-logic-app/1.png)

ロジック アプリを保存すると、このステップに HTTP POST URL が入力されます。

2. 新しいステップを追加し、**HTTP** を検索します。  

   ![[アクションを選択してください] セクションのスクリーンショット。[HTTP] タイルが選択されています。](./media/analysis-services-async-refresh-logic-app/9.png)

   ![[HTTP] ウィンドウのスクリーンショット。[HTTP - HTTP] タイルが選択されています。](./media/analysis-services-async-refresh-logic-app/10.png)

3. **[HTTP]** を選択してこのアクションを追加します。

   ![HTTP アクティビティを追加する](./media/analysis-services-async-refresh-logic-app/2.png)

次のように、HTTP アクティビティを構成します。

|プロパティ  |値  |
|---------|---------|
|**方法**     |POST         |
|**URI**     | https://*サーバーのリージョン*/servers/*aas サーバー名*/models/*データベース名*/refreshes <br /> <br /> 例:  https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**ヘッダー**     |   Content-Type, application/json <br /> <br />  ![ヘッダー](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**本文**     |   要求本体の形成の詳細については、[POST/refreshes - REST API を使用した非同期更新](analysis-services-async-refresh.md#post-refreshes)に関するセクションを参照してください。 |
|**認証**     |Active Directory OAuth         |
|**テナント**     |ご使用の Azure Active Directory のテナント ID を入力します         |
|**対象ユーザー**     |https://*.asazure.windows.net         |
|**クライアント ID**     |サービス プリンシパル名のクライアント ID を入力します         |
|**資格情報の種類**     |Secret         |
|**シークレット**     |サービス プリンシパル名のシークレットを入力します         |

例:

![完了した HTTP アクティビティ](./media/analysis-services-async-refresh-logic-app/7.png)

ここでロジック アプリをテストします。  ロジック アプリ デザイナーで **[実行]** をクリックします。

![ロジック アプリをテストする](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Azure Data Factory でロジック アプリを使用する

ロジック アプリを保存したら、**HTTP 要求の受信時** アクティビティを確認してから、生成されたばかりの **HTTP POST URL** をコピーします。  Azure Data Factory でこの URL を使用して、ロジック アプリをトリガーする非同期呼び出しを実行できます。

この操作を実行する Azure Data Factory Web アクティビティの例を次に示します。

![Data Factory Web アクティビティ](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>自己完結型のロジック アプリを使用する

Data Factory などのオーケストレーション ツールを使用してモデルの更新をトリガーする予定がない場合は、スケジュールに基づいて更新をトリガーするようにロジック アプリを設定できます。

上記の例を使用して、最初のアクティビティを削除して、それを **スケジュール** アクティビティに置き換えます。

![[Logic Apps] ページのスクリーンショット。"スケジュール" タイルが選択されています。](./media/analysis-services-async-refresh-logic-app/12.png)

![[トリガー] ページのスクリーンショット。](./media/analysis-services-async-refresh-logic-app/13.png)

この例では **繰り返し** を使用します。

アクティビティを追加したら、間隔と頻度を構成してから、新しいパラメーターを追加して **[設定時刻 (時間)]** を選択します。

![[繰り返し] セクションのスクリーンショット。[設定時刻 (時間)] パラメーターが選択されています。](./media/analysis-services-async-refresh-logic-app/16.png)

必要な時間を選択します。

![スケジュール アクティビティ](./media/analysis-services-async-refresh-logic-app/15.png)

ロジック アプリを保存します。

## <a name="next-steps"></a>次のステップ

[サンプル](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)