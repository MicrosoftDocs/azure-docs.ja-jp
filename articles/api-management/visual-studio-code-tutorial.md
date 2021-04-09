---
title: チュートリアル - API のインポートと管理 - Azure API Management と Visual Studio Code | Microsoft Docs
description: このチュートリアルでは、Visual Studio Code 用の Azure API Management 拡張機能を使用して、API のインポート、テスト、管理を行う方法について説明します。
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0dea8e43d5f09b84c5795bc257cf3331ad919fcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649548"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>チュートリアル:Visual Studio Code 用の API Management 拡張機能を使用して API をインポートおよび管理する

このチュートリアルでは、Visual Studio Code 用の API Management 拡張機能を使用して、API Management の一般的な操作を行う方法について説明します。 使い慣れた Visual Studio Code 環境を使用して、API のインポート、更新、テスト、管理を行います。

学習内容は次のとおりです。

> [!div class="checklist"]
> * API Management に API をインポートする
> * API を編集する
> * API Management のポリシーを適用する
> * API のテスト


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API Management 拡張機能の API":::

その他の API Management 機能の概要については、[Azure portal](import-and-publish.md) を使用して API Management のチュートリアルを参照してください。

## <a name="prerequisites"></a>[前提条件]
- [Azure API Management の用語](api-management-terminology.md)について理解していること
- [Visual Studio Code](https://code.visualstudio.com/) と最新の [Visual Studio Code 用 Azure API Management 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview) が確実にインストール済みであること
- [API Management インスタンスの作成](vscode-create-service-instance.md)

## <a name="import-an-api"></a>API をインポートする

以下の例では、JSON 形式の OpenAPI 仕様を API Management にインポートします。 この例で使用されるバックエンド API は Microsoft から提供されており、Azure (`https://conferenceapi.azurewebsites.net?format=json`) でホストされています。

1. Visual Studio Code のアクティビティ バーから Azure アイコンを選択します。
1. 作成した API Management インスタンスをエクスプローラー ペインで展開します。
1. **[API]** を右クリックし、 **[Import from OpenAPI Link]\(OpenAPI リンクからインポート\)** を選択します。 
1. 確認を求められたら、次の値を入力します。
    1. JSON 形式のコンテンツの **OpenAPI リンク**。 この例では「 *https://conferenceapi.azurewebsites.net?format=json* 」とします。
    この URL が、サンプル API を実装するサービスになります。 要求は、API Management によってこのアドレスに転送されます。
    1. API Management インスタンスにおける一意の **API 名** (*demo-conference-api* など)。 この名前には、英文字、数字、ハイフンのみを含めることができます。 最初と最後の文字は、英数字にする必要があります。 API を呼び出す際のパスに、この名前が使用されます。

API は、正常にインポートされるとエクスプローラー ペインに表示され、利用できる API 操作が **[Operations]\(操作\)** ノードに表示されます。

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="インポートされた API (エクスプローラー ペイン)":::

## <a name="edit-the-api"></a>API を編集する

API は、Visual Studio Code で編集することができます。 たとえば、API の Resource Manager JSON 記述をエディター ウィンドウで編集し、API へのアクセスに使用される **http** プロトコルを削除します。 その後、 **[ファイル]**  >  **[保存]** を選択します。

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="JSON 記述を編集する":::

OpenAPI 形式を編集するには、エクスプローラー ペインで API 名を右クリックし、 **[Edit OpenAPI]\(OpenAPI の編集\)** を選択します。 変更を行ってから、 **[ファイル]**  >  **[保存]** を選択します。

## <a name="apply-policies-to-the-api"></a>API にポリシーを適用する 

API Management には、API 向けに構成できる[ポリシー](api-management-policies.md)が用意されています。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 ポリシーは、グローバルにして API Management インスタンスのすべての API に適用することも、スコープを特定の API または特定の API 操作に限定することもできます。

このセクションでは、API 応答を変換するいくつかの一般的なアウトバウンド ポリシーを API に適用する方法を紹介します。 この例のポリシーは、応答ヘッダーを変更し、応答本文に出現する元のバックエンド URL を非表示にするものです。

1. エクスプローラー ペインで、インポートした *demo-conference-api* の **[ポリシー]** を選択します。 エディター ウィンドウでポリシー ファイルが開きます。 このファイルによって構成されるポリシーの対象は、この API のすべての操作です。 

1. ファイルの `<outbound>` 要素を次の内容で更新します。
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * 1 つ目の `set-header` ポリシーは、デモンストレーション用のカスタム応答ヘッダーを追加するものです。
    * 2 つ目の `set-header` ポリシーは、**X-Powered-By** ヘッダーが存在する場合に、そのヘッダーを削除するものです。 API バックエンドで使用されているアプリケーション フレームワークがこのヘッダーから明らかになる可能性があることから、このヘッダーは発行元によって削除されるのが一般的です。
    * `redirect-content-urls` ポリシーは、応答本文内のリンクを、API Management ゲートウェイを経由して同等のリンクをポイントするように書き換える (マスクする) ものです。
    
1. ファイルを保存します。 確認を求められた場合は、 **[アップロード]** を選択してファイルをクラウドにアップロードしてください。

## <a name="test-the-api"></a>API のテスト

### <a name="get-the-subscription-key"></a>サブスクリプション キーを取得する

インポートした API および適用されているポリシーをテストするためには、API Management インスタンスのサブスクリプション キーが必要です。

1. エクスプローラー ペインで、API Management インスタンスの名前を右クリックします。
1. **[Copy Subscription Key]\(サブスクリプション キーのコピー\)** を選択します。

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="サブスクリプション キーのコピー":::

### <a name="test-an-api-operation"></a>API 操作をテストする

1. インポートした *demo-conference-api* の **[Operations]\(操作\)** ノードをエクスプローラー ペインで展開します。
1. *GetSpeakers* などの操作を選択します。
1. エディター ウィンドウで、**Ocp-Apim-Subscription-Key** の横にある `{{SubscriptionKey}}` を、コピーしたサブスクリプション キーに置き換えます。
1. **[要求の送信]** をクリックします。 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Visual Studio Code から API 要求を送信する":::

要求が成功すると、バックエンドから **200 OK** およびいくつかのデータが応答として返されます。

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API テスト操作":::

この応答について、次の点に注目してください。
* 応答に **Custom** ヘッダーが追加されています。
* 応答に **X-Powered-By** ヘッダーがありません。
* API バックエンドへの URL が API Management ゲートウェイ (このケースでは `https://apim-hello-world.azure-api.net/demo-conference-api`) にリダイレクトされています。

### <a name="trace-the-api-operation"></a>API 操作をトレースする

API 操作のデバッグに役立つ詳しいトレース情報については、**Ocp-APIM-Trace-Location** の横に表示されるリンクを選択してください。 

要求を行った後に問題が発生したらその箇所を特定できるよう、この場所にある JSON ファイルに、インバウンド、バックエンド、アウトバウンドのトレース情報が格納されます。

> [!TIP]
> API 操作のテスト時は、オプションの[ポリシーのデバッグ](api-management-debug-policies.md)が API Management 拡張機能によって許可されます (Developer サービス レベルで利用可能)。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

API Management インスタンスが必要なくなった場合にこれを削除するには、右クリックして **[ポータルで開く]** を選択し、[API Management サービスとそのリソース グループを削除します](get-started-create-service-instance.md#clean-up-resources)。

または、 **[Delete API Management]\(API Management の削除\)** を選択して、API Management インスタンスのみを削除することもできます (この操作では、そのリソース グループは削除されません)。

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="API Management インスタンスを VS Code から削除する":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Visual Studio Code 用 API Management 拡張機能の、API のインポートと管理に使用できるいくつかの機能を紹介しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * API Management に API をインポートする
> * API を編集する
> * API Management のポリシーを適用する
> * API のテスト

API Management 拡張機能には、API を操作するための機能が他にも用意されています。 たとえば、[デバッグのポリシー](api-management-debug-policies.md) (Developer サービス レベルで利用可能) が利用できるほか、[名前付きの値](api-management-howto-properties.md)を作成して管理することができます。