---
title: Azure API Management で API を変換および保護する | Microsoft Docs
description: クォータとスロットル (レート制限) ポリシーを使用して、API を保護する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 68c516ee7ca2d76339760ce0ad95590686250603
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521939"
---
# <a name="transform-and-protect-your-api"></a>API を変換および保護する

このチュートリアルでは、API を変換して、プライベート バックエンド情報を公開しないようにする方法を示します。 たとえば、バックエンドで実行されているテクノロジ スタックに関する情報を非表示にすることができます。 API の HTTP 応答の本文に表示される元の URL を非表示にして、代わりに APIM ゲートウェイにリダイレクトすることもできます。

このチュートリアルでは、Azure API Management でレート制限を構成することによって、いかに簡単にバックエンド API の保護を追加できるかを示します。 たとえば、API の呼び出し回数を制限して、開発者が過剰に使用しないようにすることができます。 詳しくは、「[API Management のポリシー](api-management-policies.md)」をご覧ください

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> -   応答ヘッダーを削除するように API を変換する
> -   API 応答の本文内の元の URL を APIM ゲートウェイの URL に置換する
> -   レート制限ポリシー (調整) を追加して API を保護する
> -   変換をテストする

![ポリシー](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>前提条件

-   [Azure API Management の用語](api-management-terminology.md)について学習します。
-   [Azure API Management のポリシーの概念](api-management-howto-policies.md)を理解します。
-   次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
-   また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>応答ヘッダーを削除するように API を変換する

このセクションでは、ユーザーに対して表示したくない HTTP ヘッダーを非表示にする方法を示します。 この例では、HTTP 応答で次のヘッダーが削除されます。

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>元の応答をテストする

元の応答を表示するには:

1. APIM サービス インスタンスで **[API]** (**[API 管理]** の下) を選択します。
2. API の一覧で **[Demo Conference API]\(デモ会議 API\)** をクリックします。
3. 画面の上部にある **[テスト]** タブをクリックします。
4. **[GetSpeakers]** 操作を選択します。
5. 画面の下部にある **[送信]** をクリックします。

元の応答は次のようになります。

![ポリシー](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>変換ポリシーを設定する

![送信ポリシーを設定する](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. **[Demo Conference API]\(デモ会議 API)** を選択します。
2. 画面の上部の **[デザイン]** タブを選択します。
3. **[すべての操作]** を選択します。
4. **[送信処理]** セクションで、**[</>]** アイコンをクリックします。
5. **&lt;outbound&gt;** 要素内にカーソルを配置します。
6. 右側のウィンドウの **[変換ポリシー]** で、**[+ HTTP ヘッダーの設定]** を 2 回クリックします (2 つのポリシー スニペットを挿入するため)。

   ![ポリシー](./media/transform-api/transform-api.png)

7. **\<outbound>** コードを次のように変更します。

       <set-header name="X-Powered-By" exists-action="delete" />
       <set-header name="X-AspNet-Version" exists-action="delete" />

   ![ポリシー](./media/transform-api/set-policy.png)

8. **[保存]** ボタンをクリックします。

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>API 応答の本文内の元の URL を APIM ゲートウェイの URL に置換する

このセクションでは、API の HTTP 応答の本文に表示される元の URL を非表示にして、代わりに APIM ゲートウェイにリダイレクトする方法を示します。

### <a name="test-the-original-response"></a>元の応答をテストする

元の応答を表示するには:

1. **[Demo Conference API]\(デモ会議 API)** を選択します。
2. 画面の上部にある **[テスト]** タブをクリックします。
3. **[GetSpeakers]** 操作を選択します。
4. 画面の下部にある **[送信]** をクリックします。

    元の応答が次のように表示されます。

    ![ポリシー](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>変換ポリシーを設定する

1.  **[Demo Conference API]\(デモ会議 API\)** を選択します。
2.  **[すべての操作]** を選択します。
3.  画面の上部の **[デザイン]** タブを選択します。
4.  **[送信処理]** セクションで、**[</>]** アイコンをクリックします。
5.  **&lt;outbound&gt;** 要素内にカーソルを配置します。
6.  右側のウィンドウの **[変換ポリシー]** で、**[+ Find and replace string in body]\(+ 本文内の文字列の検索および置換\)** をクリックします。
7.  **find-and-replace** コード (**\<outbound\>** 要素内) を変更して、APIM ゲートウェイと一致するように URL を置換します。 例: 

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>レート制限ポリシー (調整) を追加して API を保護する

このセクションでは、レート制限を構成してバックエンド API の保護を強化する方法を示します。 たとえば、API の呼び出し回数を制限して、開発者が過剰に使用しないようにすることができます。 この例では、各サブスクリプション ID に対して呼び出しの上限が 15 秒ごとに 3 回に設定されます。15 秒後、開発者は、API の呼び出しを再試行できます。

![受信ポリシーを設定する](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1.  **[Demo Conference API]\(デモ会議 API)** を選択します。
2.  **[すべての操作]** を選択します。
3.  画面の上部の **[デザイン]** タブを選択します。
4.  **[受信処理]** セクションで、**[</>]** アイコンをクリックします。
5.  **&lt;inbound&gt;** 要素内にカーソルを配置します。
6.  右側のウィンドウの **[アクセス制限ポリシー]** で、**[+ Limit call rate per key]\(+ キーごとの呼び出しレートの制限\)** をクリックします。
7.  **rate-limit-by-key** コード (**\<inbound\>** 要素内) を次のコードに変更します。

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>変換をテストする

この時点でコード エディターのコードを確認すると、ポリシーは次のようになります。

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
            <find-and-replace from="://conferenceapi.azurewebsites.net:443" to="://apiphany.azure-api.net/conference"/>
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

このセクションの残りの部分では、この記事で設定したポリシー変換をテストします。

### <a name="test-the-stripped-response-headers"></a>削除した応答ヘッダーをテストする

1. **[Demo Conference API]\(デモ会議 API)** を選択します。
2. **[テスト]** タブを選びます。
3. **[GetSpeakers]** 操作をクリックします。
4. **[送信]** をクリックします。

    ヘッダーが削除されたことがわかります。

    ![ポリシー](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>置換された URL をテストする

1. **[Demo Conference API]\(デモ会議 API)** を選択します。
2. **[テスト]** タブを選びます。
3. **[GetSpeakers]** 操作をクリックします。
4. **[送信]** をクリックします。

    URL が置換されたことがわかります。

    ![ポリシー](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>レート制限 (調整) をテストする

1. **[Demo Conference API]\(デモ会議 API)** を選択します。
2. **[テスト]** タブを選びます。
3. **[GetSpeakers]** 操作をクリックします。
4. 行の **[送信]** を 3 回クリックします。

    要求を 3 回送信すると、"**429 要求が多すぎます**" 応答が返されます。

5. 15 秒ほど待ち、**[送信]** をもう一度クリックします。 今度は "**200 OK**" 応答が返されます。

    ![Throttling](./media/transform-api/test-throttling.png)

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

## <a name="next-steps"></a>次の手順

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
