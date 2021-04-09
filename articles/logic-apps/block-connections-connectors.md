---
title: 特定の API コネクタの接続をブロックする
description: Azure Logic Apps での API 接続の作成と使用を制限します
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 02d9852f6615c3926a02294e0e7eca50f2fbe9a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310035"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Azure Logic Apps でコネクタによって作成された接続をブロックする

Azure Logic Apps で、制限されたリソースまたは未承認のリソースにそれらのコネクタを使用して接続することが組織で許可されていない場合は、ロジック アプリのワークフローでそれらの接続を作成および使用する機能をブロックできます。 [Azure Policy](../governance/policy/overview.md) を使用すると、ブロックするコネクタの接続を作成したり使用したりするのを防止する[ポリシー](../governance/policy/overview.md#policy-definition)を定義して適用できます。 たとえば、セキュリティ上の理由から、特定のソーシャル メディア プラットフォームやその他のサービスまたはシステムへの接続をブロックすることが必要になる場合があります。

このトピックでは、Azure portal を使用して特定の接続をブロックするポリシーを設定する方法について説明しますが、ポリシー定義の作成は、他の方法でも行うことがきます。たとえば、Azure REST API、Azure PowerShell、Azure CLI、および Azure Resource Manager テンプレートを使用できます。 詳細については、[コンプライアンスを強制するポリシーの作成と管理」](../governance/policy/tutorials/create-and-manage.md)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合は、始める前に[無料の Azure アカウントを作成](https://azure.microsoft.com/free/)してください。

* ブロックするコネクタの参照 ID。 詳細については、「[コネクタの参照 ID を見つける](#connector-reference-ID)」を参照してください。

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>コネクタの参照 ID を見つける

ブロックする必要がある接続が含まれたロジック アプリが既にある場合は、[Azure portal の手順](#connector-ID-portal)に従ってください。 それ以外の場合は、次の手順に従います。

1. [Logic Apps コネクタの一覧](/connectors/connector-reference/connector-reference-logicapps-connectors)を参照します。

1. ブロックするコネクタの参照ページを見つけます。

   たとえば、非推奨になっている Instagram コネクタをブロックする場合は、次のページにアクセスします。

   `https://docs.microsoft.com/connectors/instagram/`

1. このページの URL から、末尾のコネクタ参照 ID をスラッシュ (`/`) を付けずにコピーして保存します。たとえば、`instagram` のようになります。

   後でポリシー定義を作成するときに、定義の条件ステートメントでこの ID を使用します。たとえば、次のようになります。

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) で、ご利用のロジック アプリを見つけて選択します。

1. ロジック アプリのメニューで、ロジック アプリの JSON 定義を表示できるように、 **[ロジック アプリ コードビュー]** を選択します。

   !["ロジック アプリ コード ビュー" を開いてコネクタ ID を見つけます。](./media/block-connections-connectors/code-view-connector-id.png)

1. `$connections` オブジェクトを含む `parameters` オブジェクトを見つけます。これには、ロジック アプリ内の各接続の `{connection-name}` オブジェクトが含まれており、その接続に関する情報が指定されています。

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   たとえば、Instagram コネクタの場合、Instagram 接続を識別する `instagram` オブジェクトを見つけます。

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. ブロックする接続について、`id` プロパティと値を見つけます。この形式は次のとおりです。 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   たとえば、Instagram 接続の `id` プロパティと値を次に示します。

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. `id` プロパティ値から、末尾のコネクター参照 ID (たとえば、`instagram`) をコピーして保存します。

   後でポリシー定義を作成するときに、定義の条件ステートメントでこの ID を使用します。たとえば、次のようになります。

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>接続の作成をブロックするポリシーを作成する

ロジック アプリで接続の作成を完全にブロックするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。 portal の検索ボックスに「`policy`」と入力し、 **[ポリシー]** を選択します。

   ![Azure portal で、"ポリシー" を検索して選択する](./media/block-connections-connectors/find-select-azure-policy.png)

1. **[ポリシー]** メニューの **[作成]** で、 **[定義]**  >  **[+ ポリシー定義]** を選択します。

   ![[定義] > [+ ポリシー定義] を選択する](./media/block-connections-connectors/add-new-policy-definition.png)

1. **[ポリシー定義]** で、次の例で説明しているプロパティに基づいて、ポリシー定義の情報を指定します。

   ![[ポリシー定義] のプロパティを示すスクリーンショット。](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **定義の場所** | はい | <*Azure サブスクリプション名*> | ポリシー定義に使用する Azure サブスクリプション <p><p>1.サブスクリプションを見つけるには、省略記号 ( **...** ) ボタンを選択します。 <br>2. **[サブスクリプション]** 一覧からサブスクリプションを見つけて選択します。 <br>3.終了したら、 **[選択]** を選択します。 |
   | **名前** | はい | <*policy-definition-name*> | ポリシー定義に使用する名前 |
   | **説明** | いいえ | <*policy-definition-name*> | ポリシー定義の説明 |
   | **カテゴリ** | はい | **Logic Apps** | ポリシー定義の既存のカテゴリまたは新しいカテゴリの名前 |
   | **ポリシーの適用** | はい | **有効** | この設定は、作業内容を保存するときにポリシー定義を有効にするか無効にするかを指定します。 |
   ||||

1. **[ポリシー ルール]** では、JSON 編集ボックスにポリシー定義テンプレートがあらかじめ設定されています。 このテンプレートを、次の表で説明しているプロパティに基づき、次の構文にしたがって、ご自身の[ポリシー定義](../governance/policy/concepts/definition-structure.md)に置き換えます。

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | `mode` | `All` | ポリシーによって評価されるリソースの種類を決定するモード。 <p><p>このシナリオでは、`mode` が `All` に設定されます。これにより、ポリシーは Azure リソース グループ、サブスクリプション、およびすべてのリソースの種類に適用されます。 <p><p>詳細については、[Policy の定義の構造 - モード](../governance/policy/concepts/definition-structure.md#mode)に関する記事を参照してください。 |
   | `if` | `{condition-to-evaluate}` | どのような場合にポリシー ルールを適用するかを決定する条件 <p><p>このシナリオでは、`{condition-to-evaluate}` により、`Microsoft.Web/connections/api.id` の `api.id` 値が、ワイルドカード (*) 値が指定されている `*managedApis/{connector-name}` で一致しているかどうかが判断されます。 <p><p>詳細については、[Policy の定義の構造 - ポリシー ルール](../governance/policy/concepts/definition-structure.md#policy-rule)に関する記事を参照してください。 |
   | `field` | `Microsoft.Web/connections/api.id` | 条件と比較する `field` 値 <p><p>このシナリオで、`field` は "[*別名*](../governance/policy/concepts/definition-structure.md#aliases)" `Microsoft.Web/connections/api.id` を使用して、コネクタ プロパティ `api.id` の値にアクセスします。 |
   | `like` | `*managedApis/{connector-name}` | `field` 値を比較するために使用する論理演算子と値 <p><p>このシナリオでは、`like` 演算子とワイルドカード (*) 文字の両方により、このルールがリージョンに関係なく機能するようにします。文字列 `*managedApis/{connector-name}` は、照合する値であり、`{connector-name}` は、ブロックするコネクタの ID です。 <p><p>たとえば、ソーシャル メディア プラットフォームまたはデータベースへの接続の作成をブロックするとします。 <p><p>- Twitter: `twitter` <br>- Instagram: `instagram` <br>- Facebook: `facebook` <br>- Pinterest: `pinterest` <br>- SQL Server または Azure SQL: `sql` <p><p>これらのコネクタ ID を見つけるには、このトピックで前述した「[コネクタの参照 ID を見つける](#connector-reference-ID)」を参照してください。 |
   | `then` | `{effect-to-apply}` | `if` 条件が満たされたときに適用する効果 <p><p>このシナリオで、`{effect-to-apply}` は、ポリシーに準拠していない要求または操作をブロックして失敗させます。 <p><p>詳細については、[Policy の定義の構造 - ポリシー ルール](../governance/policy/concepts/definition-structure.md#policy-rule)に関する記事を参照してください。 |
   | `effect` | `deny` | `effect` は、指定された接続を作成する要求をブロックします <p><p>詳細については、「[Azure Policy の効果について](../governance/policy/concepts/effects.md#deny)」の「deny」を参照してください。 |
   ||||

   たとえば、Instagram コネクタを使用した接続の作成をブロックするとします。 使用できるポリシー定義を次に示します。

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   **[POLICY RULE]\(ポリシー ルール\)** ボックスに次のように表示されます。

   ![ポリシー ルールの例が表示された [POLICY RULE]\(ポリシー ルール\) ボックスを示すスクリーンショット。](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   複数のコネクタの場合は、次のように条件を追加できます。

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. 終了したら、 **[保存]** を選択します。 ポリシー定義を保存すると、Azure Policy によって、さらにプロパティ値が生成されて、ポリシー定義に追加されます。

1. 次に、ポリシーを適用する場所にポリシー定義を割り当てるために、[ポリシー割り当てを作成](#create-policy-assignment)します。

Azure Policy 定義の詳細については、次のトピックを参照してください。

* [Policy の定義の構造](../governance/policy/concepts/definition-structure.md)
* [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)
* [Azure Logic Apps 用の Azure Policy 組み込みポリシー定義](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>接続の使用をブロックするポリシーを作成する

ロジック アプリ内で接続を作成すると、その接続は別の Azure リソースとして存在します。 ロジック アプリのみを削除した場合、接続は自動的には削除されず、削除されるまで存在し続けます。 接続が既に存在する場合や、ロジック アプリの外部で使用するために接続を作成する必要がある場合があります。 その場合も、制限された接続または未承認の接続を含むロジック アプリの保存を防止するポリシーを作成することにより、ロジック アプリ内の既存の接続を使用する機能をブロックできます。

1. [Azure portal](https://portal.azure.com) にサインインします。 portal の検索ボックスに「`policy`」と入力し、 **[ポリシー]** を選択します。

   ![Azure portal で、"ポリシー" を検索して選択する](./media/block-connections-connectors/find-select-azure-policy.png)

1. **[ポリシー]** メニューの **[作成]** で、 **[定義]**  >  **[+ ポリシー定義]** を選択します。

   ![[定義] > [+ ポリシー定義] を選択する](./media/block-connections-connectors/add-new-policy-definition.png)

1. **[ポリシー定義]** で、次の例で説明しているプロパティに基づいてポリシー定義の情報を指定します。例として Instagram を使用して続行します。

   ![ポリシー定義のプロパティ](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **定義の場所** | はい | <*Azure サブスクリプション名*> | ポリシー定義に使用する Azure サブスクリプション <p><p>1.サブスクリプションを見つけるには、省略記号 ( **...** ) ボタンを選択します。 <br>2. **[サブスクリプション]** 一覧からサブスクリプションを見つけて選択します。 <br>3.終了したら、 **[選択]** を選択します。 |
   | **名前** | はい | <*policy-definition-name*> | ポリシー定義に使用する名前 |
   | **説明** | いいえ | <*policy-definition-name*> | ポリシー定義の説明 |
   | **カテゴリ** | はい | **Logic Apps** | ポリシー定義の既存のカテゴリまたは新しいカテゴリの名前 |
   | **ポリシーの適用** | はい | **有効** | この設定は、作業内容を保存するときにポリシー定義を有効にするか無効にするかを指定します。 |
   ||||

1. **[ポリシー ルール]** では、JSON 編集ボックスにポリシー定義テンプレートがあらかじめ設定されています。 このテンプレートを、次の表で説明しているプロパティに基づき、次の構文にしたがって、ご自身の[ポリシー定義](../governance/policy/concepts/definition-structure.md)に置き換えます。

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | `mode` | `All` | ポリシーによって評価されるリソースの種類を決定するモード。 <p><p>このシナリオでは、`mode` が `All` に設定されます。これにより、ポリシーは Azure リソース グループ、サブスクリプション、およびすべてのリソースの種類に適用されます。 <p><p>詳細については、[Policy の定義の構造 - モード](../governance/policy/concepts/definition-structure.md#mode)に関する記事を参照してください。 |
   | `if` | `{condition-to-evaluate}` | どのような場合にポリシー ルールを適用するかを決定する条件 <p><p>このシナリオでは、`{condition-to-evaluate}` は、`[string(field('Microsoft.Logic/workflows/parameters'))]` からの文字列出力に文字列 `{connector-name}` が含まれているかどうかを判断します。 <p><p>詳細については、[Policy の定義の構造 - ポリシー ルール](../governance/policy/concepts/definition-structure.md#policy-rule)に関する記事を参照してください。 |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | 条件と比較する値 <p><p>このシナリオで、`value` は `[string(field('Microsoft.Logic/workflows/parameters'))]` からの文字列出力であり、`Microsoft.Logic/workflows/parameters` オブジェクト内の `$connectors` オブジェクトを文字列に変換します。 |
   | `contains` | `{connector-name}` | `value` プロパティとの比較に使用する論理演算子と値 <p><p>このシナリオで、`contains` 演算子は、`{connector-name}` の出現箇所に関係なくルールが機能するようにします。ここで、文字列 `{connector-name}` は、制限またはブロックするコネクタの ID です。 <p><p>たとえば、ソーシャル メディア プラットフォームまたはデータベースへの接続の使用をブロックするとします。 <p><p>- Twitter: `twitter` <br>- Instagram: `instagram` <br>- Facebook: `facebook` <br>- Pinterest: `pinterest` <br>- SQL Server または Azure SQL: `sql` <p><p>これらのコネクタ ID を見つけるには、このトピックで前述した「[コネクタの参照 ID を見つける](#connector-reference-ID)」を参照してください。 |
   | `then` | `{effect-to-apply}` | `if` 条件が満たされたときに適用する効果 <p><p>このシナリオで、`{effect-to-apply}` は、ポリシーに準拠していない要求または操作をブロックして失敗させます。 <p><p>詳細については、[Policy の定義の構造 - ポリシー ルール](../governance/policy/concepts/definition-structure.md#policy-rule)に関する記事を参照してください。 |
   | `effect` | `deny` | `effect` は、指定された接続を使用するロジック アプリを保存する要求を `deny` またはブロックします <p><p>詳細については、「[Azure Policy の効果について](../governance/policy/concepts/effects.md#deny)」の「deny」を参照してください。 |
   ||||

   たとえば、Instagram 接続を使用するロジック アプリの保存をブロックするとします。 使用できるポリシー定義を次に示します。

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   **[POLICY RULE]\(ポリシー ルール\)** ボックスに次のように表示されます。

   ![ポリシー定義のルール](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. 終了したら、 **[保存]** を選択します。 ポリシー定義を保存すると、Azure Policy によって、さらにプロパティ値が生成されて、ポリシー定義に追加されます。

1. 次に、ポリシーを適用する場所にポリシー定義を割り当てるために、[ポリシー割り当てを作成](#create-policy-assignment)します。

Azure Policy 定義の詳細については、次のトピックを参照してください。

* [Policy の定義の構造](../governance/policy/concepts/definition-structure.md)
* [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)
* [Azure Logic Apps 用の Azure Policy 組み込みポリシー定義](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>ポリシー割り当てを作成する

次に、ポリシー定義を、ポリシーを適用する場所 (たとえば、1 つのリソース グループ、複数のリソース グループ、Azure Active Directory (Azure AD) テナント、または Azure サブスクリプション) に割り当てる必要があります。 このタスクでは、次の手順に従ってポリシー割り当てを作成します。

1. サインアウトした場合は、[Azure portal](https://portal.azure.com) にもう一度サインインします。 portal の検索ボックスに「`policy`」と入力し、 **[ポリシー]** を選択します。

   ![Azure portal で、"ポリシー" を検索して選択する](./media/block-connections-connectors/find-select-azure-policy.png)

1. **[ポリシー]** メニューの **[作成]** で、 **[割り当て]**  >  **[ポリシーの割り当て]** を選択します。

   ![[割り当て] > [割り当て] を選択する](./media/block-connections-connectors/add-new-policy-assignment.png)

1. **[基本]** で、ポリシー割り当てに関する次の情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **スコープ** | はい | ポリシー割り当てを適用するリソース。 <p><p>1. **[スコープ]** ボックスの横にある省略記号 ( **...** ) ボタンを選択します。 <br>2. **[サブスクリプション]** の一覧から、Azure サブスクリプションを選択します。 <br>3.必要に応じて、 **[リソース グループ]** の一覧から、リソース グループを選択します。 <br>4.終了したら、 **[選択]** を選択します。 |
   | **除外** | いいえ | ポリシー割り当てから除外する Azure リソースを選択します。 <p><p>1. **[除外]** ボックスの横にある省略記号 ( **...** ) ボタンを選択します。 <br>2. **[リソース]** リストで、リソース > **[Add to Selected Scope]\(選択したスコープに追加\)** を選択します。 <br>3.終了したら、 **[保存]** を選択します。 |
   | **ポリシー定義** | はい | 割り当てて適用するポリシー定義の名前。 この例では、"Block Instagram connections" という Instagram ポリシーの例を続けます。 <p><p>1. **[ポリシー定義]** ボックスの横にある省略記号 ( **...** ) ボタンを選択します。 <br>2. **[種類]** フィルターまたは **[検索]** ボックスを使用して、ポリシー定義を検索して選択します。 <br>3.終了したら、 **[選択]** を選択します。 |
   | **割り当て名** | はい | ポリシー割り当てに使用する名前 (ポリシー定義と異なる場合) |
   | **割り当て ID** | はい | ポリシー割り当ての自動生成された ID |
   | **説明** | いいえ | ポリシー割り当ての説明 |
   | **ポリシーの適用** | はい | ポリシー割り当てを有効または無効にする設定 |
   | **割り当て担当者** | いいえ | ポリシー割り当てを作成および適用したユーザーの名前 |
   ||||

   たとえば、Instagram の例を使用して、Azure リソース グループにポリシーを割り当てるには、次のようにします。

   ![ポリシー割り当てのプロパティ](./media/block-connections-connectors/policy-assignment-basics.png)

1. 完了したら、 **[確認および作成]** を選択します。

   ポリシーを作成した後、ポリシーが有効になるまでに最大 15 分間待つ必要がある場合があります。 変更でも同様の遅延効果がある場合があります。

1. ポリシーが有効になったら、[ポリシーをテスト](#test-policy)できます。

詳細については、「[クイック スタート: 準拠していないリソースを識別するためのポリシー割り当てを作成する](../governance/policy/assign-policy-portal.md)」を参照してください。

<a name="test-policy"></a>

## <a name="test-the-policy"></a>ポリシーをテストする

ポリシーを試すには、ロジック アプリ デザイナーで、現在制限されているコネクタを使用して接続の作成を開始します。 Instagram の例を続行した場合、Instagram にサインインするときに、ロジック アプリが接続の作成に失敗したというエラーが表示されます。

![適用されたポリシーが原因の接続エラー](./media/block-connections-connectors/connection-failure-message.png)

メッセージには次の情報が含まれています。

| 説明 | コンテンツ |
|-------------|---------|
| エラーの原因 | `"Resource 'instagram' was disallowed by policy."` |
| 割り当て名 | `"Block Instagram connections"` |
| 割り当て ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| ポリシー定義 ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>次のステップ

* [Azure Policy](../governance/policy/overview.md) の詳細を確認する