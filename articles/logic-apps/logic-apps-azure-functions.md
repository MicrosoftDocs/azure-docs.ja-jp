---
title: 関数を追加して Azure Logic Apps から呼び出す
description: Azure Logic Apps の自動化されたタスクおよびワークフローから Azure で作成した関数内のカスタム コードを呼び出して実行する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: 7df9f7d072af7c5f6523fd1be0432ce51954fa10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791881"
---
# <a name="call-functions-from-azure-logic-apps"></a>Azure Logic Apps から関数を呼び出す

自分のロジック アプリで特定のジョブを実行するためのコードを実行する場合は、[Azure Functions](../azure-functions/functions-overview.md) を使用して独自の関数を作成することができます。 このサービスを利用して Node.js、C#、および F# 関数を作成できるので、完全なアプリやコードを実行するためのインフラストラクチャを構築する必要はありません。 また、[関数内からロジック アプリを呼び出す](#call-logic-app)こともできます。 Azure Functions は、クラウド内でサーバーレス コンピューティングを実現します。次のようなタスクを実行するために便利です。

* Node.js または C# で、関数を使用してロジック アプリの動作を拡張します。
* ロジック アプリのワークフローで計算を実行します。
* ロジック アプリのフィールドで高度な書式設定や計算を行います。

Azure Functions を使用せずにコード スニペットを実行するには、[インライン コードを追加して実行](../logic-apps/logic-apps-add-run-inline-code.md)する方法を確認してください。

> [!NOTE]
> Logic Apps と Azure Functions の統合では、現在、スロットを有効にして機能させることはできません。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 関数アプリ。自分が作成する関数と共に Azure Functions 内に作成される関数用のコンテナーです。 関数アプリを持っていない場合は、[まず関数アプリを作成します](../azure-functions/functions-get-started.md)。 次に、Azure portal でロジック アプリの外部で関数を作成するか、ロジック アプリ デザイナーで[ロジック アプリの内部から](#create-function-designer)関数を作成することができます。

* ロジック アプリを操作するときは、既存であるか新規であるかに関係なく、同じ要件が関数アプリと関数に適用されます。

  * 関数アプリとロジック アプリでは、同じ Azure サブスクリプションを使用する必要があります。

  * 新しい関数アプリでは、ランタイム スタックとして .NET または JavaScript を使用する必要があります。 既存の関数アプリに新しい関数を追加する場合は、C# または JavaScript のいずれかを選択できます。

  * 自分の関数では、**HTTP トリガー** テンプレートを使用します。

    HTTP トリガー テンプレートは、ロジック アプリから `application/json` 型のコンテンツを受け入れることができます。 ロジック アプリに関数を追加すると、このテンプレートからお使いの Azure サブスクリプション内に作成されたカスタム関数がロジック アプリ デザイナーに表示されます。

  * [OpenAPI 定義](../azure-functions/functions-openapi-definition.md) (以前は [Swagger ファイル](https://swagger.io/)として知られていました) を定義済みでない限り、自分の関数でカスタム ルートが使用されることはありません。

  * 自分の関数に対して OpenAPI 定義を定義している場合、関数パラメーターを操作するときに、ロジック アプリ デザイナーによって豊富なエクスペリエンスが提供されます。 OpenAPI 定義を含む関数をロジック アプリが見つけてアクセスできるようにするには、[以下の手順に従って関数アプリを設定](#function-swagger)します。

* ロジック アプリの最初の手順として[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を含む、関数の追加先となるロジック アプリ

  関数を実行するアクションを追加する前に、自分のロジック アプリをトリガーで開始する必要があります。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>OpenAPI の記述がある関数を検索する

ロジック アプリ デザイナーで関数アプリを操作するときに豊富なエクスペリエンスを利用するには、自分の関数に対して、以前は [Swagger ファイル](https://swagger.io/)として知られていた [OpenAPI 定義を生成](../azure-functions/functions-openapi-definition.md)します。 お使いのロジック アプリで Swagger の記述を含む関数を検索して使用できるように、関数アプリを設定するために、次の手順を実行します。

1. 自分の関数アプリがアクティブに実行されていることを確認します。

1. 自分の関数アプリで、次の手順に従って[クロスオリジン リソース共有 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) を設定して、すべてのオリジンが許可されるようにします。

   1. **関数アプリ** の一覧から、自分の関数アプリを選択します。 右側のウィンドウで、 **[プラットフォーム機能]**  >  **[CORS]** を選択します。

      ![関数アプリ、[プラットフォーム機能]、[CORS] の順に選択する](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. **[CORS]** でアスタリスク ( **`*`** ) ワイルドカード文字を追加しますが、リスト内の他のすべてのオリジンは削除して、 **[保存]** を選択します。

      !["CORS* にワイルドカード文字 "*" を設定します。](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>HTTP 要求の内部でプロパティ値にアクセスする

Webhook 関数は、入力として HTTP 要求を受け取り、それらの要求を他の関数に渡すことができます。 たとえば、Logic Apps には [DateTime 値を変換する関数](../logic-apps/workflow-definition-language-functions-reference.md)がありますが、この基本的なサンプル JavaScript 関数は、関数に渡された要求オブジェクト内のプロパティにアクセスしてそのプロパティ値を操作する方法を示しています。 オブジェクト内のプロパティにアクセスするために、この例では[ドット (.) 演算子](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)を使用しています。

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

この関数の内部で何が起きるかを、次に示します。

1. 関数は `data` 変数を作成し、`request` オブジェクト内の `body` オブジェクトをその変数に割り当てます。 この関数はドット (.) 演算子を使用して、`request` オブジェクト内の `body` オブジェクトを参照します。

   ```javascript
   var data = request.body;
   ```

1. これで、この関数は `data` 変数を介して `date` プロパティにアクセスし、`ToDateString()` 関数を呼び出すことによってそのプロパティ値を DateTime 型から DateString 型に変換することができます。 関数は、関数の応答の `body` プロパティを介して、結果も返します。

   ```javascript
   body: data.date.ToDateString();
   ```

これで Azure に関数を作成したので、[ロジック アプリに関数を追加する](#add-function-logic-app)手順に従ってください。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>ロジック アプリの内部で関数を作成する

ロジック アプリ デザイナーに組み込みの Azure Functions アクションを使用して、ロジック アプリのワークフローから関数を直接作成できますが、この方法を使用できるのは、JavaScript で記述された関数の場合のみです。 その他の言語の場合は、Azure portal の Azure Functions 機能を使用して関数を作成できます。 詳細については、「[Azure Portal で初めての関数を作成する](../azure-functions/functions-get-started.md)」を参照してください。

ただし、Azure で関数を作成する前に、関数のコンテナーである関数アプリをあらかじめ用意しておく必要があります。 関数アプリを持っていない場合は、まず関数アプリを作成します。 「[Azure Portal で初めての関数を作成する](../azure-functions/functions-get-started.md)」を参照してください。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. 関数を作成して追加するには、自身のシナリオに適用されるステップに従います。

   * ロジック アプリのワークフローの最後のステップで、 **[新しいステップ]** を選択します。

   * ロジック アプリのワークフローの既存のステップ間で、矢印の上にマウスを移動して、プラス (+) 記号を選択し、 **[アクションの追加]** を選択します。

1. 検索ボックスで、フィルターとして「azure functions」と入力します。 アクションの一覧から、次のような **[Azure 関数を選択する]** アクションを選択します。

   ![Azure portal で関数を見つける。](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 関数アプリの一覧で、関数アプリを選択します。 アクションの一覧が開いたら、次のアクションを選択します。 **[新しい関数を作成する]** 。

   ![関数アプリを選択する](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 関数定義エディターで、関数を定義します。

   1. **[関数名]** ボックスで関数の名前を指定します。

   1. **[コード]** ボックスで、自分のコードを関数テンプレートに追加します。関数の実行が終了した後でロジック アプリに返される応答とペイロードも含めます。 完了したら **[作成]** を選択します。

   次に例を示します。

   ![関数を定義する](./media/logic-apps-azure-functions/add-code-function-definition.png)

   テンプレートのコードでは、 *`context` オブジェクト* は、以降のステップでロジック アプリが **Request Body** フィールド経由で送信するメッセージを参照します。 関数の内部から `context` オブジェクトのプロパティにアクセスするには、次の構文を使用します。

   `context.body.<property-name>`

   たとえば、`context` オブジェクト内の `content` プロパティを参照するには、次の構文を使用します。

   `context.body.content`

   また、テンプレート コードには、`input` 変数が含まれます。この変数は、お使いの関数が該当の値に対する操作を実行できるように、`data` パラメーターからの値を格納します。 また、JavaScript 関数の中で、`data` 変数は `context.body` のショートカットです。

   > [!NOTE]
   > この場合の `body` プロパティは `context` オブジェクトに適用され、アクションの出力からの **Body** トークンと同じではありません。お使いの関数にも渡すことができます。

1. **[要求本文]** ボックスで、関数の入力を指定します。書式は JavaScript Object Notation (JSON) オブジェクトにする必要があります。

   この入力は、ロジック アプリが関数に送信する *コンテキスト オブジェクト* またはメッセージです。 **[要求本文]** フィールドをクリックすると、動的コンテンツの一覧が表示され、前のステップからの出力のトークンを選択できます。 この例では、コンテキスト ペイロードが、メール トリガーからの **From** トークンの値を保持する `content` という名前のプロパティを含んでいることを示しています。

   ![[要求本文] の例 - コンテキスト オブジェクトのペイロード](./media/logic-apps-azure-functions/function-request-body-example.png)

   ここで、コンテキスト オブジェクトは文字列としてキャストされないため、オブジェクトのコンテンツは JSON ペイロードに直接追加されます。 ただし、コンテキスト オブジェクトが文字列、JSON オブジェクト、または JSON 配列を渡す JSON トークンでない場合は、エラーが発生します。 そのため、この例で代わりに **Received Time** トークンを使用した場合は、二重引用符を追加することで、コンテキスト オブジェクトを文字列としてキャストできます。

   ![オブジェクトを文字列としてキャストする](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 使用するメソッド、要求ヘッダー、クエリ パラメーター、認証などのその他の詳細を指定するには、 **[新しいパラメーターの追加]** の一覧を開き、目的のオプションを選択します。 認証については、選択した関数によってオプションが異なります。 「[関数の認証を有効にする](#enable-authentication-functions)」を参照してください。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>ロジック アプリに既存の関数を追加する

ロジック アプリから既存の関数を呼び出すには、ロジック アプリ デザイナーで他のアクションと同様に関数を追加します。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. 関数を追加するステップで、 **[新しいステップ]** を選択します。

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「azure functions」と入力します。 アクションの一覧から、 **[Azure 関数を選択する]** アクションを選択します。

   ![Azure 内で関数を見つける。](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 関数アプリの一覧で、関数アプリを選択します。 関数の一覧が表示されたら、関数を選択します。

   ![関数アプリと関数を選択する](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   API 定義 (Swagger 記述) を持ち、[ロジック アプリが探してアクセスできるように設定された](#function-swagger)関数の場合は、 **[Swagger アクション]** を選択することができます。

   ![関数アプリ、[Swagger アクション]、および自分の関数を選択する](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. **[要求本文]** ボックスで、関数の入力を指定します。書式は JavaScript Object Notation (JSON) オブジェクトにする必要があります。

   この入力は、ロジック アプリが関数に送信する *コンテキスト オブジェクト* またはメッセージです。 **[要求本文]** フィールドをクリックすると、動的コンテンツの一覧が表示され、前のステップからの出力のトークンを選択できます。 この例では、コンテキスト ペイロードが、メール トリガーからの **From** トークンの値を保持する `content` という名前のプロパティを含んでいることを示しています。

   ![[要求本文] の例 - コンテキスト オブジェクトのペイロード](./media/logic-apps-azure-functions/function-request-body-example.png)

   ここで、コンテキスト オブジェクトは文字列としてキャストされないため、オブジェクトのコンテンツは JSON ペイロードに直接追加されます。 ただし、コンテキスト オブジェクトが文字列、JSON オブジェクト、または JSON 配列を渡す JSON トークンでない場合は、エラーが発生します。 そのため、この例で代わりに **Received Time** トークンを使用した場合は、二重引用符を追加することで、コンテキスト オブジェクトを文字列としてキャストできます。

   ![オブジェクトを文字列としてキャストする](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 使用するメソッド、要求ヘッダー、クエリ パラメーター、認証などのその他の詳細を指定するには、 **[新しいパラメーターの追加]** の一覧を開き、目的のオプションを選択します。 認証については、選択した関数によってオプションが異なります。 [関数の認証の有効化](#enable-authentication-functions)に関するセクションを参照してください。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>関数からロジック アプリを呼び出す

関数の内部からロジック アプリをトリガーする場合、ロジック アプリは、呼び出し可能なエンドポイントを提供するトリガーから開始する必要があります。 たとえば、**HTTP**、**Request**、**Azure Queues**、または **Event Grid** トリガーでロジック アプリを開始できます。 次に、関数の内部から、そのトリガーの URL に HTTP POST 要求を送信し、ロジック アプリで処理するペイロードを含めます。 詳細については、「[ロジック アプリを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」をご覧ください。

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>関数の認証を有効にする

サインインしたり、資格情報やシークレットを指定したりする必要なく Azure Active Directory (Azure AD) によって保護される他のリソースへのアクセスを容易に認証するために、ロジック アプリでは、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) (以前はマネージド サービス ID (MSI) と呼ばれていました) を使用することができます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。 [Azure AD 認証用のマネージド ID がサポートされているサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)の詳細をご覧ください。

システム割り当て ID または手動で作成したユーザー割り当て ID を使用するようにロジック アプリを設定した場合、ロジック アプリ内の関数では、認証にもその同じ ID を使用できます。 ロジック アプリ内の関数の認証サポートについては、「[送信呼び出しに認証を追加する](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)」を参照してください。

自分の関数でマネージド ID を設定し、使用するには、次の手順を行います。

1. ロジック アプリでマネージド ID を有効にし、ターゲット リソースに対するその ID のアクセスを設定します。 「[Azure Logic Apps でマネージド ID を使用して認証し、リソースにアクセスする](../logic-apps/create-managed-service-identity.md)」を参照してください。

1. 次の手順で、関数と関数アプリの認証を有効にします。

   * [関数で匿名認証を設定する](#set-authentication-function-app)
   * [関数アプリで Azure AD 認証を設定する](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>関数で匿名認証を設定する

関数内でロジック アプリのマネージド ID を使用するには、関数の認証レベルを匿名に設定する必要があります。 設定していないと、ロジック アプリにより "BadRequest" エラーがスローされます。

1. [Azure portal](https://portal.azure.com) で、お使いの関数アプリを探して選択します。 以下の手順ではサンプル関数アプリとして "FabrikamFunctionApp" が使用されます。

1. 関数アプリ ウィンドウで **[プラットフォーム機能]** を選択します。 **[開発ツール]** で **[高度なツール (Kudu)]** を選択します。

   ![Kudu の高度なツールを開く](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Kudu Web サイトのタイトル バーで、 **[デバッグ コンソール]** メニューから、 **[CMD]** を選択します。

   ![デバッグ コンソール メニューから、[CMD] オプションを選択します。](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. 次のページが表示されたら、フォルダーの一覧から **[site]**  >  **[wwwroot]**  >  *<自分の関数>* の順に選択します。 以下の手順ではサンプル関数として "FabrikamAzureFunction" が使用されます。

   ![[site]、[wwwroot]、<自分の関数> の順に選択する](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. 編集のために `function.json` ファイルを開きます。

   !["function.json" ファイルの編集をクリックする](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. `bindings` オブジェクトで、`authLevel` プロパティが存在するかどうかを確認します。 プロパティが存在する場合、プロパティの値を `anonymous` に設定します。 存在しない場合、そのプロパティを追加し、値を設定します。

   !["authLevel" プロパティを追加し、"anonymous" に設定する](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. 完了したら、設定を保存し、次のセクションに進みます。

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>関数アプリで Azure AD 認証を設定する

このタスクを開始する前に、後で使用するために、次の値を見つけてメモしておきます。

* システム割り当て ID に対して生成され、ロジック アプリを表すオブジェクト ID

  * このオブジェクト ID を生成するには、[ロジック アプリのシステム割り当て ID を有効にします](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)。

  * それ以外の方法では、ロジック アプリ デザイナーでロジック アプリを開くことでこのオブジェクト ID が見つかります。 ロジック アプリのメニューの **[設定]** で、 **[ID]**  >  **[システム割り当て済み]** の順に選択します。

* Azure Active Directory (Azure AD) のテナントのディレクトリ ID

  テナントのディレクトリ ID を取得するには、[`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) Powershell コマンドを実行します。 あるいは、Azure portal で次の手順を行います。

  1. [Azure portal](https://portal.azure.com) で、お使いの関数アプリを探して選択します。

  1. Azure AD テナントを検索して選択します。 この手順ではサンプル テナントとして "Fabrikam" を使用します。

  1. テナントのメニューの **[管理]** の下で **[プロパティ]** を選択します。

  1. たとえば、テナントのディレクトリ ID をコピーし、後で使用するためにその ID を保存します。

     ![Azure AD テナントのディレクトリ ID を見つけてコピーする](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* アクセスするターゲット リソースのリソース ID

  * これらのリソース ID を見つける方法については、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

  > [!IMPORTANT]
  > このリソース ID は、必要な末尾のスラッシュも含めて、Azure AD で求められる値と正確に一致させる必要があります。

  このリソース ID は、後で、[システム割り当て ID を使用するように関数アクションを設定する](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)ときに、 **[対象]** プロパティで使用するものと同じ値でもあります。

これで、関数アプリで Azure AD 認証を設定する準備ができました。

1. [Azure portal](https://portal.azure.com) で、お使いの関数アプリを探して選択します。

1. 関数アプリ ウィンドウで **[プラットフォーム機能]** を選択します。 **[ネットワーク]** で **[認証/承認]** を選択します。

   ![認証と承認の設定を表示する](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. **[App Service 認証]** 設定を **[オン]** に変更します。 **[要求が認証されていない場合のアクション]** 一覧から、 **[Azure Active Directory でのログイン]** を選択します。 **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。

   ![Azure AD による認証をオンにする](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. **[Azure Active Directory の設定]** ウィンドウで、次の手順を行います。

   1. **[管理モード]** を **[詳細]** に設定します。

   1. **[クライアント ID]** プロパティに、ロジック アプリのシステム割り当て ID のオブジェクト ID を入力します。

   1. **[発行者の URL]** プロパティに `https://sts.windows.net/` URL を入力し、Azure AD テナントのディレクトリ ID を追加します。

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. **[許可されるトークン対象ユーザー]** プロパティに、アクセスするターゲット リソースのリソース ID を入力します。

      このリソース ID は、後で、[システム割り当て ID を使用するように関数アクションを設定する](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)とき、 **[対象]** プロパティで使用するものと同じ値です。

   この時点で、バージョンは次の例のようになります。

   ![Azure Active Directory 認証設定](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. 終了したら、 **[OK]** を選択します。

1. ロジック アプリ デザイナーに戻り、[こちら](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)の手順に従って、管理対象 ID でアクセスを認証します。

## <a name="next-steps"></a>次のステップ

* [Logic Apps のコネクタ](../connectors/apis-list.md)について確認します。