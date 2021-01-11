---
title: Azure API Management ポリシーでの名前付きの値の使用方法
description: Azure API Management ポリシーでの名前付きの値の使用方法を説明します。 名前付きの値には、リテラル文字列とポリシー式を含めることができます。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 3f317276ae92e6121d519553b7883677dab89705
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852193"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Azure API Management ポリシーでの名前付きの値の使用方法

API Management のポリシーは、Azure Portal がその構成を通じて API の動作を変更できる、システムの強力な機能の 1 つです。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 ポリシー ステートメントは、リテラル テキストの値、ポリシーの式、名前付きの値を使用して構築できます。

それぞれの API Management サービスインスタンスには、サービスインスタンスに対してグローバルなキーと値のペアのコレクションがあり、これは名前付きの値と呼ばれます。 コレクション内の項目の数に制限はありません。 名前付きの値を利用し、すべての API の構成とポリシーを対象に、定数文字列値を管理できます。 各名前付きの値は、次の属性を持つことができます。

| 属性      | Type            | Description                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | ポリシー内の名前付きの値を参照するために使用されます。 1 から 256 文字の文字列。 文字、数字、ドット、ダッシュのみを使用できます。 |
| `Value`        | string          | 実際の値。 空にしたり、空白のみで構成したりすることはできません。 最大文字数は 4,096 文字です。                                        |
| `Secret`       | boolean         | 値がシークレットかどうかと暗号化する必要があるかどうかを決定します。                                                               |
| `Tags`         | 文字列の配列 | 名前付きの値の一覧をフィルター処理するために使用されます。 最大 32 のタグ。                                                                                    |

![名前付きの値](./media/api-management-howto-properties/named-values.png)

名前付きの値には、リテラル文字列と[ポリシー式](./api-management-policy-expressions.md)を含めることができます。 たとえば、`Expression` の値は、現在の日時を含む文字列を返すポリシー式です。 名前付きの値 `Credential` はシークレットとしてマークされているので、既定では、その値は表示されません。

| 名前       | 値                      | Secret | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| 値      | 42                         | False  | vital-numbers |
| 資格情報 | ••••••••••••••••••••••     | True   | security      |
| 式 | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> API Management サービス内に格納された名前付きの値ではなく、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) サービスに格納された値を使用できます。こちらが[その例](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)です。

## <a name="to-add-and-edit-a-named-value"></a>名前付きの値を追加および編集するには

![名前付きの値を追加する](./media/api-management-howto-properties/add-property.png)

1. **[API Management]** で **[API]** を選びます。
2. **[名前付きの値]** を選択します。
3. **[+ 追加]** を押します。

    [名前] と [値] は必須値です。 値がシークレットの場合、 _[これはシークレットです]_ チェックボックスをオンにします。 名前付きの値の整理に役立つ任意のタグを 1 つまたは複数入力し、[保存] をクリックします。

4. **Create** をクリックしてください。

名前付きの値が作成されたら、それをクリックして編集できます。 名前付きの値の名前を変更すると、その名前付きの値を参照するすべてのポリシーが、その新しい名前を使用するように自動的に更新されます。

## <a name="to-delete-a-named-value"></a>名前付きの値を削除するには

名前付きの値を削除するには、削除する名前付きの値の横にある **[削除]** をクリックします。

> [!IMPORTANT]
> 名前付きの値がいずれかのポリシーで参照されている場合、その名前付きの値を使用しているすべてのポリシーからその値を削除するまで削除は完了しません。

## <a name="to-search-and-filter-named-values"></a>名前付きの値を検索し、フィルター処理するには

**[名前付きの値]** タブには、名前付きの値の管理に役立つ検索とフィルター処理の機能があります。 名前付きの値の一覧を名前でフィルター処理するには、 **[検索プロパティ]** テキストボックスに検索語句を入力します。 すべての名前付きの値を表示するには、 **[検索プロパティ]** テキストボックスを消去し、Enter を押します。

タグで一覧をフィルター処理するには、 **[タグでフィルター]** テキストボックスに 1 つまたは複数のタグを入力します。 すべての名前付きの値を表示するには、 **[タグでフィルター]** テキストボックスを消去し、Enter を押します。

## <a name="to-use-a-named-value"></a>名前付きの値を使用するには

ポリシーで名前付きの値を使用するには、`{{ContosoHeader}}` のように、二重の括弧の中にその名前を置きます。次に例を示します。

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

この例では、`ContosoHeader` は `set-header` ポリシーのヘッダー名として使用されており、`ContosoHeaderValue` はそのヘッダーの値として使用されています。 このポリシーが API Management ゲートウェイの要求または応答で評価されるとき、`{{ContosoHeader}}` と `{{ContosoHeaderValue}}` はそれぞれの値で置換されます。

名前付きの値は前の例のように完全な属性または要素値として使用できますが、次の例に示すように、リテラル テキスト表現に挿入するか、その一部と組み合わせることもできます: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

名前付きの値にはポリシー式を含めることもできます。 次の例では、`ExpressionProperty` が使用されます。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

このポリシーが評価されるとき、`{{ExpressionProperty}}` はその値 `@(DateTime.Now.ToString())` で置換されます。 値がポリシー式であるため、式が評価され、ポリシーがその実行に進みます。

名前付きの値が範囲内にあるポリシーを持つ操作を呼び出すことで、開発者ポータルでこれを試すことができます。 次の例では、前の 2 つのサンプル `set-header` ポリシーと名前付きの値で操作が呼び出されています。 ポリシーと名前付きの値を利用して構成された 2 つのカスタム ヘッダーが応答に含まれることに注意してください。

![[開発者ポータル]][api-management-send-results]

[API Inspector トレース](api-management-howto-api-inspector.md)を見て、前の 2 つのサンプル ポリシーと名前付きの値が含まれる呼び出しを探すと、ポリシー式が含まれる名前付きの値のポリシー式評価に加え、名前付きの値が挿入された 2 つの `set-header` ポリシーを確認できます。

![API Inspector トレース][api-management-api-inspector-trace]

名前付きの値にはポリシー式を含めることができますが、他の名前付きの値を含めることはできません。 名前付きの値参照を含むテキストが `Text: {{MyProperty}}` などの値に使用されている場合、その参照は解決されず、置き換えられません。

## <a name="next-steps"></a>次のステップ

-   ポリシーの使用に関する説明
    -   [API Management のポリシー](api-management-howto-policies.md)
    -   [Policy reference (ポリシー リファレンス)](./api-management-policies.md)
    -   [ポリシー式](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
