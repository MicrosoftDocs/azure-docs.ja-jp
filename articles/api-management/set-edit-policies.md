---
title: Azure API Management ポリシーの設定または編集方法 | Microsoft Docs
description: Azure API Management ポリシーを設定または編集する方法について説明します。 これらのポリシーは、一連の受信および送信ステートメントが記述された XML ドキュメントです。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: a737fada3e019029967bc752aaa0dedc354fa880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078325"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Azure API Management ポリシーの設定または編集方法

ポリシー定義は、一連の受信ステートメントと送信ステートメントが記述された XML ドキュメントです。 XML は、定義ウィンドウで直接編集できます。 ポリシー ウィンドウの右側に表示される一覧から定義済みのポリシーを選ぶこともできます。 現在のスコープに適用可能なステートメントは有効になり、強調表示されます。 有効なステートメントをクリックすると、定義ビュー内のカーソル位置に適切な XML が追加されます。 

ポリシーについて詳しくは、「[Azure API Management のポリシー](api-management-howto-policies.md)」をご覧ください。

## <a name="set-or-edit-a-policy"></a>ポリシーの設定または編集

ポリシーを設定または編集するには、次の手順に従います。

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。
2. APIM インスタンスを参照します。
3. **[API]** タブをクリックします。

    ![ポリシーを編集する](./media/set-edit-policies/code-editor.png)

4. 前にインポートした API の 1 つを選びます。
5. **[デザイン]** タブを選択します。
6. ポリシーを適用する操作を選びます。 すべての操作にポリシーを適用する場合は、**[すべての操作]** を選択します。
7. **[受信処理]** または **[送信処理]** セクションで、 **</>** (コード エディター) アイコンを選択します。
8. 目的のポリシー コードを適切なブロックのいずれかに貼り付けます。

    ```xml
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>スコープの構成

ポリシーは、グローバルに構成することも、成果物、API、または操作をスコープとして構成することもできます。 ポリシーの構成を開始するには、ポリシーを適用するスコープを最初に選択する必要があります。

ポリシー スコープは、次の順序で評価されます。

1. グローバル スコープ
2. 成果物スコープ
3. API スコープ
4. 操作スコープ

ポリシーに含まれるステートメントは、`base` 要素が存在する場合は、その配置に従って評価されます。 グローバル ポリシーには親ポリシーがないため、`<base>` 要素を使用しても効果はありません。

現在のスコープに含まれるポリシーをポリシー エディターに表示するには、 **[選択したスコープの有効なポリシーを再計算する]** をクリックします。

### <a name="global-scope"></a>グローバル スコープ

グローバル スコープが APIM インスタンス内の **[すべての API]** に対して構成されます。

1. [Azure Portal](https://portal.azure.com/) にサインインし、APIM インスタンスに移動します。
2. **[すべての API]** をクリックします。

    ![グローバル スコープ](./media/api-management-howto-policies/global-scope.png)

3. 三角形のアイコンをクリックします。
4. **[コード エディター]** を選択します。
5. ポリシーを追加または編集します。
6. **[保存]** をクリックします。 

    API Management ゲートウェイに変更が直ちに反映されます。

### <a name="product-scope"></a>成果物スコープ

成果物スコープは、選択した成果物に対して構成されます。

1. **[成果物]** をクリックします。

    ![成果物スコープ](./media/api-management-howto-policies/product-scope.png)

2. ポリシーを適用する成果物を選びます。
3. **[ポリシー]** をクリックします。
4. ポリシーを追加または編集します。
5. **[保存]** をクリックします。 

### <a name="api-scope"></a>API スコープ

API スコープは、選択した API の **[すべての操作]** に対して構成されます。

1. ポリシーを適用する **API** を選びます。

    ![API スコープ](./media/api-management-howto-policies/api-scope.png)

2. **[すべての操作]** を選択します
3. 三角形のアイコンをクリックします。
4. **[コード エディター]** を選択します。
5. ポリシーを追加または編集します。
6. **[保存]** をクリックします。 

### <a name="operation-scope"></a>操作スコープ 

操作スコープは、選択した操作に対して構成されます。

1. **API** を選びます。
2. ポリシーを適用する操作を選びます。

    ![操作スコープ](./media/api-management-howto-policies/operation-scope.png)

3. 三角形のアイコンをクリックします。
4. **[コード エディター]** を選択します。
5. ポリシーを追加または編集します。
6. **[保存]** をクリックします。 

## <a name="next-steps"></a>次の手順

次の関連トピックをご覧ください。

+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
+ [ポリシーのサンプル](./policy-reference.md)