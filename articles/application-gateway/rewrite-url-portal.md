---
title: Azure Application Gateway を使用して URL とクエリ文字列を書き換える - Azure portal
description: Azure portal を使用して、URL とクエリ文字列を書き換えるように Azure Application Gateway を構成する方法について説明します
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 7/16/2020
ms.author: surmb
ms.openlocfilehash: ec58c6f97efdbcb91071bcea98bbbc614833246d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215775"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal-preview"></a>Azure Application Gateway を使用して URL を書き換える - Azure portal (プレビュー)

この記事では、Azure portal を使用して、URL を書き換えるように [Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md) インスタンスを構成する方法を説明します。

>[!NOTE]
> URL 書き換え機能はプレビュー段階であり、Application Gateway の Standard_v2 および WAF_v2 SKU でのみ使用できます。 運用環境での使用はお勧めしません。 プレビューの詳細については、[使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには Application Gateway v2 SKU インスタンスが必要です。 URL の書き換えは v1 SKU ではサポートされていません。 v2 SKU を持っていない場合は、始める前に [Application Gateway v2 SKU](tutorial-autoscale-ps.md) インスタンスを作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="configure-url-rewrite"></a>URL 書き換えを構成する

次の例では、要求 URL に */article* が含まれている場合は常に、URL パスと URL クエリ文字列が書き換えられます

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. **[すべてのリソース]** を選択して、アプリケーション ゲートウェイを選択します。

2. 左側のウィンドウで **[Rewrites]\(書き換え\)** を選択します。

3. **[Rewrite set]\(書き換えセット\)** を選択します。

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="書き換えセットを追加する":::

4. 書き換えセットの名前を指定し、ルーティング規則に関連付けます。

    a. **[名前]** ボックスに、書き換えセットの名前を入力します。
    
    b. **[関連付けられているルーティング規則]** の一覧に表示されている規則の 1 つ以上を選択します。 これは、ルーティング規則によって書き換え構成をソース リスナーに関連付けるために使用されます。 選択できるのは、他の書き換えセットに関連付けられていないルーティング規則のみです。 既に他の書き換えセットに関連付けられている規則は灰色で表示されます。
    
    c. **[次へ]** を選択します。
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="ルールに関連付ける":::

5. 書き換えルールを作成します。

    a. **[書き換え規則の追加]** を選択します。
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="[書き換え規則の追加] が強調表示されたスクリーンショット。":::
    
    b. 書き換え規則の名前を **[書き換えルール名]** ボックスに入力します。 **[規則のシーケンス]** ボックスに値を入力します。

6. この例では、パスに */article* が含まれている場合にのみ、URL パスと URL クエリ文字列を書き換えます。 これを行うには、URL パスに */article* が含まれているかどうかを評価する条件を追加します

    a. **[条件の追加]** を選択し、 **[If]** 命令がを含まれるボックスを選択して展開します。
    
    b. この例では、URL パス内の */article* パターンを確認する必要があるため、 **[チェックする変数の型]** の一覧で **[サーバー変数]** を選択します。
    
    c. **[サーバー変数]** 一覧で、uri_path を選択します
    
    d. **[大文字と小文字を区別する]** で、 **[いいえ]** を選択します。
    
    e. **[演算子]** ボックスの一覧で、 **[等しい (=)]** を選択します。
    
    f. 正規表現パターンを入力します。 この例では、パターン `.*article/(.*)/(.*)` を使用します
    
      () は、後で URL パスを書き換えるための式を作成する際に使用される部分文字列を取得するために使用されます。 詳細については、[このページ](rewrite-http-headers-url.md#capturing)を参照してください。

    g. **[OK]** を選択します。

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Condition":::

 

7. URL と URL パスを書き換えるアクションを追加します

   a. **[書き換えの種類]** の一覧で、 **[URL]** を選択します。

   b. **[アクションの種類]** ボックスの一覧で、 **[設定]** を選択します。

   c. **[コンポーネント]** で、 **[URL パスと URL クエリ文字列の両方]** を選択します

   d. **[URL パスの値]** に、パスの新しい値を入力します。 この例では、 **/article.aspx** を使用します 

   e. **[URL クエリ文字列の値]** に、URL クエリ文字列の新しい値を入力します。 この例では、**id={var_uri_path_1}&title={var_uri_path_2}** を使用します
    
    `{var_uri_path_1}` と `{var_uri_path_1}` は、この式 `.*article/(.*)/(.*)` で条件を評価しているときに取得された部分文字列を取り込むために使用されます
    
   f. **[OK]** を選択します。

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="操作":::

8. **[作成]** をクリックして、書き換えセットを作成します。

9. 新しい書き換えセットが、書き換えセットの一覧に表示されていることを確認します

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="書き換え規則の追加":::

## <a name="verify-url-rewrite-through-access-logs"></a>アクセス ログを使用して URL の書き換えを確認する

アクセス ログの以下のフィールドを確認し、想定どおりに URL の書き換えが行われたかどうか確かめます。

* **originalRequestUriWithArgs**:このフィールドには元の要求 URL が含まれています
* **requestUri**:このフィールドには、Application Gateway での書き換え操作後の URL が含まれています

アクセス ログのすべてのフィールドの詳細については、[こちら](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)を参照してください。

##  <a name="next-steps"></a>次のステップ

一般的なユース ケースにおける書き換えの設定方法の詳細については、[一般的な書き換えのシナリオ](rewrite-http-headers.md)に関する記事を参照してください。
