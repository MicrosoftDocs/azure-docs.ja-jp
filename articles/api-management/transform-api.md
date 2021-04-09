---
title: チュートリアル - Azure API Management で API を変換および保護する | Microsoft Docs
description: このチュートリアルでは、API Management で変換とスロットル (レート制限) ポリシーを使用して API を保護する方法について説明します。
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010233"
---
# <a name="tutorial-transform-and-protect-your-api"></a>チュートリアル:API を変換および保護する

このチュートリアルでは、API を変換して、プライベート バックエンド情報を公開しないようにする方法を示します。 たとえば、バックエンドで実行されているテクノロジ スタックに関する情報を非表示にすることができます。 API の HTTP 応答の本文に表示される元の URL を非表示にして、代わりに APIM ゲートウェイにリダイレクトすることもできます。

このチュートリアルでは、Azure API Management でレート制限を構成することによって、いかに簡単にバックエンド API の保護を追加できるかを示します。 たとえば、API が開発者によって過剰に使用されないよう、API 呼び出しレートを制限したい場合があります。 詳細については、「[API Management のポリシー](api-management-policies.md)」を参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> -   応答ヘッダーを削除するように API を変換する
> -   API 応答の本文内の元の URL を APIM ゲートウェイの URL に置換する
> -   レート制限ポリシー (スロットリング) を追加して API を保護する
> -   変換をテストする

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="ポータルでのポリシー":::

## <a name="prerequisites"></a>前提条件

-   [Azure API Management の用語](api-management-terminology.md)について学習します。
-   [Azure API Management のポリシーの概念](api-management-howto-policies.md)を理解します。
-   次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
-   また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>応答ヘッダーを削除するように API を変換する

このセクションでは、ユーザーに対して表示したくない HTTP ヘッダーを非表示にする方法を示します。 この例では、HTTP 応答から次のヘッダーを削除する方法を紹介しています。

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>元の応答をテストする

元の応答を表示するには:

1. API Management サービス インスタンスで、 **[API]** を選択します。
1. API の一覧で **[Demo Conference API]\(デモ会議 API\)** を選択します。
1. 画面の上部にある **[テスト]** タブを選択します。
1. **[GetSpeakers]** 操作を選択し、 **[送信]** を選択します。

元の応答は、次のようになります。

:::image type="content" source="media/transform-api/original-response.png" alt-text="元の API 応答":::

応答に、**X-AspNet-Version** と **X-Powered-By** というヘッダーが含まれていることがわかります。

### <a name="set-the-transformation-policy"></a>変換ポリシーを設定する

1. **[Demo Conference API]\(デモ会議 API\)**  >  **[Design]\(デザイン\)**  >  **[すべての操作]** を選択します。
4. **[送信処理]** セクションで、コード エディター ( **</>** ) アイコンを選択します。

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="送信ポリシーに移動する" border="false":::

1. **&lt;outbound&gt;** 要素内にカーソルを置き、右上隅にある **[Show snippets]\(スニペットの表示\)** を選択します。
1. 右側のウィンドウの **[変換ポリシー]** で、 **[+ HTTP ヘッダーの設定]** を 2 回クリックします (2 つのポリシー スニペットを挿入するため)。

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="HTTP ヘッダーの設定ポリシー":::

1. **\<outbound>** コードを次のように変更します。

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="HTTP ヘッダーを設定する":::

1. **[保存]** を選択します。

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>API 応答の本文内の元の URL を APIM ゲートウェイの URL に置換する

このセクションでは、API の HTTP 応答の本文に表示される元の URL を非表示にして、代わりに APIM ゲートウェイにリダイレクトする方法を示します。

### <a name="test-the-original-response"></a>元の応答をテストする

元の応答を表示するには:

1. **[Demo Conference API]\(デモ会議 API)**  >  **[テスト]** を選択します。
1. **[GetSpeakers]** 操作を選択し、 **[送信]** を選択します。

    応答に、元のバックエンド URL が含まれていることがわかります。

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="応答に含まれる元の URL":::


### <a name="set-the-transformation-policy"></a>変換ポリシーを設定する

1.  **[Demo Conference API]\(デモ会議 API\)**  >  **[すべての操作]**  >  **[Design]\(デザイン\)** を選択します。
1.  **[送信処理]** セクションで、コード エディター ( **</>** ) アイコンを選択します。
1.  **&lt;outbound&gt;** 要素内にカーソルを置き、右上隅にある **[Show snippets]\(スニペットの表示\)** を選択します。
1.  右側のウィンドウの **[変換ポリシー]** で、 **[Mask URLs in content]\(コンテンツ内の URL をマスク\)** を選択します。 
1.  **[保存]** を選択します。

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>レート制限ポリシー (調整) を追加して API を保護する

このセクションでは、レート制限を構成してバックエンド API の保護を強化する方法を示します。 たとえば、API が開発者によって過剰に使用されないよう、API 呼び出しレートを制限したい場合があります。 この例では、各サブスクリプション ID に対して呼び出しの上限が 15 秒ごとに 3 回に設定されます。 15 秒後、開発者は、API の呼び出しを再試行できます。

1.  **[Demo Conference API]\(デモ会議 API\)**  >  **[すべての操作]**  >  **[Design]\(デザイン\)** を選択します。
1.  **[受信処理]** セクションで、コード エディター ( **</>** ) アイコンを選択します。
1.  **&lt;inbound&gt;** 要素内にカーソルを置き、右上隅にある **[Show snippets]\(スニペットの表示\)** を選択します。

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="受信ポリシーを設定する" border="false":::

1.  右側のウィンドウの **[アクセス制限ポリシー]** で、 **[+ Limit call rate per key]\(+ キーごとの呼び出しレートの制限\)** を選択します。
1.  **rate-limit-by-key** コード ( **\<inbound\>** 要素内) を次のコードに変更します。

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>変換をテストする

この時点でコード エディターのコードを確認すると、ポリシーは次のようになります。

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

このセクションの残りの部分では、この記事で設定したポリシー変換をテストします。

### <a name="test-the-stripped-response-headers"></a>削除した応答ヘッダーをテストする

1. **[Demo Conference API]\(デモ会議 API)**  >  **[テスト]** を選択します。
1. **[GetSpeakers]** 操作を選択し、 **[送信]** を選択します。

    ヘッダーが削除されたことがわかります。

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="削除された応答ヘッダー":::

### <a name="test-the-replaced-url"></a>置換された URL をテストする

1. **[Demo Conference API]\(デモ会議 API)**  >  **[テスト]** を選択します。
1. **[GetSpeakers]** 操作を選択し、 **[送信]** を選択します。

    URL が置換されたことがわかります。

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="置き換えられた URL":::

### <a name="test-the-rate-limit-throttling"></a>レート制限 (調整) をテストする

1. **[Demo Conference API]\(デモ会議 API)**  >  **[テスト]** を選択します。
1. **[GetSpeakers]** 操作を選択します。 **[送信]** を 3 回続けて選択します。

    要求を 3 回送信すると、"**429 要求が多すぎます**" 応答が返されます。

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="要求が多すぎます":::

1. 15 秒ほど待ち、 **[送信]** をもう一度選択します。 今度は "**200 OK**" 応答が返されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> -   応答ヘッダーを削除するように API を変換する
> -   API 応答の本文内の元の URL を APIM ゲートウェイの URL に置換する
> -   レート制限ポリシー (調整) を追加して API を保護する
> -   変換をテストする

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [API の監視](api-management-howto-use-azure-monitor.md)
