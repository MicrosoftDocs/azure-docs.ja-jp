---
title: Azure Active Directory B2C と Arkose Labs を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: リスクのある不正なユーザーを特定するために、Arkose Labs で Azure Active Directory B2C を構成するためのチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/18/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c2aceedd57bcc7cd88c4e822c7b696e36b28bd8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579792"
---
# <a name="tutorial-configure-arkose-labs-with-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C を使用して Arkose Labs を構成する

このサンプル チュートリアルでは、Azure Active Directory (AD) B2C 認証を [Arkose Labs](https://www.arkoselabs.com/) と統合する方法について説明します。 Arkose Labs は、組織がボット攻撃、アカウント乗っ取り攻撃、および不正なアカウント開設を防ぐのを支援します。  

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。

- [Arkose Labs](https://www.arkoselabs.com/book-a-demo/) アカウント。

## <a name="scenario-description"></a>シナリオの説明

Arkose Labs 統合には、次のコンポーネントが含まれています。

- **Arkose Labs** - ボットやその他の自動化された不正使用に対する不正行為および不正使用防止サービスです。

- **Azure AD B2C のサインアップ ユーザー フロー** - Arkose Labs サービスを使用するサインアップ エクスペリエンスです。 カスタム HTML と JavaScript、および API コネクタを使用して Arkose Labs サービスと統合します。

- **Azure 関数** - API コネクタ機能を使用して動作する、ユーザーがホストする API エンドポイントです。 この API は、Arkose Labs セッション トークンをサーバー側で検証する役割を担います。

次の図は、Arkose Labs を Azure AD B2C と統合する方法を示しています。

![Arkose Labs アーキテクチャの図を示す画像](media/partner-arkose-labs/arkose-labs-architecture-diagram.png)

| 手順  | 説明 |
|---|---|
|1     | ユーザーがサインアップし、アカウントを作成します。 ユーザーが [送信] を選択すると、Arkose Labs 適用チャレンジが表示されます。         |
|2     |  ユーザーがチャレンジを完了すると、Azure AD B2C によってその状態が Arkose Labs に送信され、トークンが生成されます。 |
|3     |  Arkose Labs によってトークンが生成され、それが Azure AD B2C に送り返されます。   |
|4     |  Azure AD B2C によって中間 Web API が呼び出され、サインアップ フォームが渡されます。      |
|5     |  中間 Web API によって、トークン検証のためにサインアップ フォームが Arkose Lab に送信されます。    |
|6     | Arkose Lab によってプロセスが処理され、検証結果が中間 Web API に送り返されます。|
|7     | 中間 Web API によって、チャレンジの成功または失敗の結果が Azure AD B2C に送信されます。 |
|8     | チャレンジが正常に完了した場合、サインアップ フォームが Azure AD B2C に送信され、Azure AD B2C による認証が完了します。|

## <a name="onboard-with-arkose-labs"></a>Arkose Labs を使用してオンボードする

1. [Arkose](https://www.arkoselabs.com/book-a-demo/) に連絡し、アカウントを作成します。

2. アカウントが作成されたら、 https://dashboard.arkoselabs.com/login に移動します。  

3. ダッシュボード内でサイトの設定に移動して、公開キーと秘密キーを検索します。 この情報は、後で Azure AD B2C を構成するために必要になります。 公開および秘密キーの値は、[サンプル コード](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)では `ARKOSE_PUBLIC_KEY` および `ARKOSE_PRIVATE_KEY` として参照されています。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

### <a name="part-1--create-a-arkosesessiontoken-custom-attribute"></a>パート 1 – ArkoseSesessionToken カスタム属性を作成する

カスタム属性を作成するには、こちらの手順に従います。  

1. **Azure portal** >  **[Azure AD B2C]** に移動します。

2. **[ユーザー属性]** を選択します。

3. **[追加]** を選択します。

4. 属性名に「**ArkoseSesessionToken**」と入力します。

5. **[作成]** を選択します。

[カスタム属性](./user-flow-custom-attributes.md?pivots=b2c-user-flow)の詳細を確認してください。

### <a name="part-2---create-a-user-flow"></a>パート 2 - ユーザー フローを作成する

ユーザー フローは、**サインアップ** と **サインイン**、または **サインアップ** のみに使用できます。 Arkose Labs のユーザー フローは、サインアップ中にのみ表示されます。

1. ユーザー フローを作成するには、[手順](./tutorial-create-user-flows.md)を参照してください。 既存のユーザー フローを使用する場合は、それが **推奨 (次世代のプレビュー)** バージョンの種類である必要があります。

2. ユーザー フローの設定で、 **[ユーザー属性]** に移動し、 **[ArkoseSessionToken]** 要求を選択します。

![カスタム属性を選択する方法を示す図](media/partner-arkose-labs/select-custom-attribute.png)

### <a name="part-3---configure-custom-html-javascript-and-page-layouts"></a>パート 3 - カスタム HTML、JavaScript、ページ レイアウトを構成する

指定された [HTML スクリプト](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html)に移動します。 このファイルには、次の 3 つの処理を実行する、JavaScriptの `<script>` タグを持つHTML テンプレートが含まれています。

1. Arkose Labs スクリプトを読み込みます。これにより、Arkose Labs ウィジェットがレンダリングされ、クライアント側の Arkose Labs の検証が行われます。

2. ユーザーに表示される UI から、`ArkoseSessionToken` カスタム属性に対応する `extension_ArkoseSessionToken` 入力要素とラベルを非表示にします。

3. ユーザーが Arkose Labs チャレンジを完了すると、Arkose Labs によってユーザーの応答が検証され、トークンが生成されます。 カスタム JavaScript のコールバック `arkoseCallback` によって、`extension_ArkoseSessionToken` の値が生成されたトークン値に設定されます。 この値は、次のセクションで説明するように API エンドポイントに送信されます。

    Arkose Labs のクライアント側の検証については、[こちらの記事](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/214176229/Standard+Setup)を参照してください。

ユーザー フローにカスタム HTML と JavaScript を使用するには、記載されている手順に従ってください。

1. [selfAsserted.html](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose/blob/main/Assets/selfAsserted.html) ファイルを変更して、`<ARKOSE_PUBLIC_KEY>` がクライアント側の検証用に生成した値と一致するようにし、お使いのアカウントに Arkose Labs スクリプトを読み込むために使用されるようにします。

2. クロス オリジン リソース共有 (CORS) が有効になっている Web エンドポイントで HTML ページをホストします。 [Azure BLOB ストレージ アカウントを作成](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)し、[CORS を構成](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)します。

  >[!NOTE]
  >独自のカスタム HTML がある場合は、`<script>` 要素をコピーしてご自分の HTML ページに貼り付けます。

3. こちらの手順に従ってページ レイアウトを構成します。

   a. Azure portal から、 **[Azure AD B2C]** に移動します。

   b. **[ユーザー フロー]** に移動し、ご自身のユーザー フローを選択します。

   c. **[Page layouts]\(ページ レイアウト\)** を選択します。

   d. **[Local account sign up page layout]\(ローカル アカウントのサインアップ ページ レイアウト\)** を選択します。

   e. **[Use custom page content]\(カスタム ページ コンテンツを使用する\)** を **[はい]** に切り替えます。

   f. カスタム HTML が存在する URI を **[Use custom page content]\(カスタム ページ コンテンツを使用する\)** に貼り付けます。

   g. ソーシャル ID プロバイダーを使用している場合は、 **[Social account sign-up page]\(ソーシャル アカウントのサインアップ ページ\)** レイアウトについて **手順 e** と **f** を繰り返します。

   ![ページ レイアウトを示す画像](media/partner-arkose-labs/page-layouts.png)

4. ユーザー フローから **[プロパティ]** に移動し、 **[Enable JavaScript]\(JavaScript を有効にする\)** を選択してページ レイアウト (プレビュー) を適用します。 詳しくは、[こちらの記事](./javascript-and-page-layout.md?pivots=b2c-user-flow)を参照してください。

### <a name="part-4---create-and-deploy-your-api"></a>パート 4 - API を作成してデプロイする

Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)をインストールします。

>[!Note]
>このセクションで説明する手順では、Visual Studio Code を使用して Azure 関数をデプロイすることを前提としています。 Azure portal、ターミナルまたはコマンド プロンプト、あるいはその他のコード エディターを使用してデプロイすることもできます。

#### <a name="run-the-api-locally"></a>API をローカルで実行する

1. Visual Studio Code の左側のナビゲーション バーで、Azure 拡張機能に移動します。 ローカルの Azure 関数を表す **[ローカル プロジェクト]** フォルダーを選択します。

2. **F5** を押すか、 **[デバッグ]**  >  **[デバッグの開始]** メニューを使用してデバッガーを起動し、Azure Functions ホストに接続します。 このコマンドでは、Azure 関数によって作成された単一のデバッグ構成が自動的に使用されます。

3. Azure Functions 拡張機能によって、ローカル開発用にいくつかのファイルが自動的に生成され、依存関係がインストールされます。また、まだ存在しない場合は、Functions Core ツールがインストールされます。 これらのツールは、デバッグ エクスペリエンスに役立ちます。

4. Functions Core ツールからの出力が Visual Studio Code の **[ターミナル]** パネルに表示されます。 ホストが起動したら、出力に表示されるローカル URL を **Alt を押しながらクリック** してブラウザーを開き、関数を実行します。 Azure Functions エクスプローラーで、関数を右クリックして、ローカルにホストされている関数の URL を表示します。

テスト中にローカル インスタンスを再デプロイするには、手順 1 から 4 を繰り返します。

#### <a name="add-environment-variables"></a>環境変数を追加する

このサンプルでは、[HTTP 基本認証](https://tools.ietf.org/html/rfc7617)を使用して Web API エンドポイントを保護します。

ユーザー名とパスワードは、リポジトリの一部としてではなく、環境変数として格納されます。 詳細については、[local.settings.js](../azure-functions/functions-run-local.md?tabs=macos%2ccsharp%2cbash#local-settings-file) ファイルを参照してください。

1. ルート フォルダー内に local.settings.js ファイルを作成します

2. 下のコードをコピーしてファイルに貼り付けます

```
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "BASIC_AUTH_USERNAME": "<USERNAME>",
    "BASIC_AUTH_PASSWORD": "<PASSWORD>",
    "ARKOSE_PRIVATE_KEY": "<ARKOSE_PRIVATE_KEY>",
    "B2C_EXTENSIONS_APP_ID": "<B2C_EXTENSIONS_APP_ID>"
  }
}
```
**BASIC_AUTH_USERNAME** と **BASIC_AUTH_PASSWORD** の値は、Azure 関数に対する API 呼び出しを認証するために使用される資格情報になります。 任意の値を選択します。

`<ARKOSE_PRIVATE_KEY>` は、Arkose Labs サービスで生成したサーバー側のシークレットです。 これは、[Arkose Labs サーバー側の検証 API](https://arkoselabs.atlassian.net/wiki/spaces/DG/pages/266214758/Server-Side+Instructions) を呼び出して、フロントエンドによって生成された `ArkoseSessionToken` の値を検証するために使用されます。

`<B2C_EXTENSIONS_APP_ID>` は、ディレクトリにカスタム属性を格納するために Azure AD B2C によって使用されるアプリのアプリケーション ID です。 このアプリケーション ID を見つけるには、 **[概要]** ペインから [アプリの登録] に移動し、「b2c-extensions-app」を検索して、アプリケーション (クライアント) ID をコピーします。 `-` 文字を削除します。

![アプリ ID による検索を示す画像](media/partner-arkose-labs/search-app-id.png)

#### <a name="deploy-the-application-to-the-web"></a>アプリケーションを Web にデプロイする

1. [こちら](/azure/javascript/tutorial-vscode-serverless-node-04)のガイドで説明されている手順に従って、Azure 関数をクラウドにデプロイします。 Azure 関数のエンドポイント Web URL をコピーします。

2. デプロイしたら、 **[Upload settings]\(設定のアップロード\)** オプションを選択します。 これにより、環境変数が App Service の[アプリケーション設定](../azure-functions/functions-develop-vs-code.md?tabs=csharp#application-settings-in-azure)にアップロードされます。 これらのアプリケーション設定は、[Azure portal を使用して管理](../azure-functions/functions-how-to-use-azure-function-app-settings.md)または構成することもできます。

Azure Functions の Visual Studio Code での開発の詳細については、[こちらの記事](../azure-functions/functions-develop-vs-code.md?tabs=csharp#republish-project-files)を参照してください。

#### <a name="configure-and-enable-the-api-connector"></a>API コネクタを構成して有効にする

[API コネクタを作成](./add-api-connector.md)し、ユーザー フローに対して有効にします。 API コネクタの構成は次のようになります。

![API コネクタを構成する方法を示す図](media/partner-arkose-labs/configure-api-connector.png)

- **エンドポイント URL** - Azure 関数をデプロイしたときにコピーした関数の URL です。

- **ユーザー名とパスワード** - 以前に環境変数として定義したユーザー名とパスワードです。

API コネクタを有効にするには、ユーザー フローの **API コネクタ** の設定で、 **[Before creating the user]\(ユーザーを作成する前\)** の手順で呼び出される API コネクタを選択します。 これにより、ユーザーがサインアップ フローで **[作成]** を選択したときに、その API が呼び出されます。 このAPI によって、Arkose ウィジェットのコールバック `arkoseCallback` によって設定された `ArkoseSessionToken` 値のサーバー側の検証が実行されます。

![API コネクタの有効化を示す図](media/partner-arkose-labs/enable-api-connector.png)

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある **[ユーザー フロー]** を選択します。

2. 以前に作成したユーザー フローを選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. [アプリケーション] : 登録済みのアプリを選択します (サンプルは JWT)

   b. [応答 URL] : リダイレクト URL を選択します

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. サインアウト

6. サインイン フローを実行します  

7. **[続行]** を選択すると、Arkose Labs パズルが表示されます。

## <a name="additional-resources"></a>その他のリソース

- Azure AD B2C サインアップ ユーザー フローの[サンプル コード](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose)

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)