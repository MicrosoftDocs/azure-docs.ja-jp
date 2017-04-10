---
title: "Azure API Management ポリシーのプロパティの利用方法"
description: "Azure API Management ポリシーのプロパティの利用方法に関する説明"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 6f39b00f-cf6e-4cef-9bf2-1f89202c0bc0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 146aa7706b5ff2a613dd94c75f3ad447ace82834


---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Azure API Management ポリシーのプロパティの利用方法
API Management のポリシーは、発行者がその構成を通じて API の動作を変更できる、システムの強力な機能の 1 つです。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 ポリシー ステートメントは、リテラル テキストの値、ポリシーの式、プロパティを使用して構築できます。 

それぞれの API Management サービス インスタンスには、サービス インスタンスにグローバルなキー/値ペアのプロパティ コレクションがあります。 これらのプロパティを利用し、すべての API の構成とポリシーを対象に、定数文字列値を管理できます。 各プロパティには以下の属性があります。

| Attribute | 型 | Description |
| --- | --- | --- |
| Name |文字列 |プロパティの名前。 文字、数字、ピリオド、ダッシュ、下線のみを使用できます。 |
| 値 |文字列 |プロパティの値。 空にすることはできません。スペースのみで構成することはできません。 |
| シークレット |boolean |値がシークレットかどうかと暗号化する必要があるかどうかを決定します。 |
| タグ |文字列の配列 |任意のタグ。指定されている場合、プロパティの一覧のフィルター処理に利用できます。 |

プロパティは発行者ポータルの **[プロパティ]** タブで構成できます。 次の例では、次の 3 つのプロパティが構成されています。

![プロパティ][api-management-properties]

プロパティ値にはリテラル文字列と[ポリシー式](https://msdn.microsoft.com/library/azure/dn910913.aspx)を含めることができます。 次の表は、前の 3 つのサンプル プロパティとその属性をまとめたものです。 `ExpressionProperty` の値は、現在の日時を含む文字列を返すポリシー式です。 プロパティ `ContosoHeaderValue` はシークレットとして設定されています。そのため、その値は表示されていません。

| 名前 | 値 | シークレット | タグ |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-use-a-property"></a>プロパティを使用するには
ポリシーでプロパティを使用するには、`{{ContosoHeader}}` のように、二重の括弧の中にプロパティ名を置きます。次に例を示します。

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

この例では、`ContosoHeader` は `set-header` ポリシーのヘッダー名として使用されており、`ContosoHeaderValue` はそのヘッダーの値として使用されています。 このポリシーが API Management ゲートウェイの要求または応答で評価されるとき、`{{ContosoHeader}}` と `{{ContosoHeaderValue}}` はそれぞれのプロパティ値で置換されます。

プロパティは前の例のように完全な属性または要素値として使用できますが、例 `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

プロパティにはポリシー式を含めることもできます。 次の例では、`ExpressionProperty` が使用されます。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

このポリシーが評価されるとき、`{{ExpressionProperty}}` はその値 `@(DateTime.Now.ToString())` で置換されます。 値がポリシー式であるため、式が評価され、ポリシーがその実行に進みます。

プロパティが範囲内にあるポリシーを持つ操作を呼び出すことで、開発者ポータルでこれを試すことができます。 次の例では、前の 2 つのサンプル `set-header` ポリシーとプロパティで操作が呼び出されています。 ポリシーとプロパティを利用して構成された 2 つのカスタム ヘッダーが応答に含まれることに注意してください。

![[開発者ポータル]][api-management-send-results]

[API Inspector トレース](api-management-howto-api-inspector.md)を見て、前の 2 つのサンプル ポリシーとプロパティが含まれる呼び出しを探すと、ポリシー式が含まれるプロパティのポリシールール評価に加え、プロパティが挿入された 2 つの `set-header` ポリシーを確認できます。

![API Inspector トレース][api-management-api-inspector-trace]

プロパティ値にはポリシー式を含めることができますが、プロパティ値に他のプロパティを含めることはできないことに注意してください。 `Property value text {{MyProperty}}`のように、プロパティ参照を含むテキストが使用されている場合、そのプロパティ参照は置換されず、プロパティ値の一部として追加されます。

## <a name="to-create-a-property"></a>プロパティを作成するには
プロパティを作成するには、**[プロパティ]** タブで **[プロパティの追加]** をクリックします。

![プロパティの追加][api-management-properties-add-property-menu]

**[名前]** と **[値]** は必須値です。 このプロパティの値がシークレットの場合、 **[これはシークレットです]** チェック ボックスをオンします。 プロパティの整理に役立つ任意のタグを 1 つまたは複数入力し、 **[保存]**をクリックします。

![プロパティの追加][api-management-properties-add-property]

新しいプロパティが保存されると、 **[検索プロパティ]** テキストボックスに新しいプロパティの名前が入力され、新しいプロパティが表示されます。 すべてのプロパティを表示するには、 **[検索プロパティ]** テキストボックスを消去し、Enter を押します。

![[プロパティ]][api-management-properties-property-saved]

REST API を利用し、プロパティを作成する方法については、「 [Create a property using the REST API (REST API を利用してプロパティを作成する)](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put)」を参照してください。

## <a name="to-edit-a-property"></a>プロパティを編集するには
プロパティを編集するには、変数するプロパティの横にある **[編集]** をクリックします。

![プロパティの編集][api-management-properties-edit]

変更があれば行い、 **[保存]**をクリックします。 プロパティ名を変更すると、そのプロパティを参照するポリシーも、その新しい名前を使用するように自動的に更新されます。

![プロパティの編集][api-management-properties-edit-property]

REST API を利用し、プロパティを編集する方法については、「 [Edit a property using the REST API (REST API を利用してプロパティを編集する)](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch)」を参照してください。

## <a name="to-delete-a-property"></a>プロパティを削除するには
プロパティを削除するには、削除するプロパティの横にある **[削除]** をクリックします。

![プロパティの削除][api-management-properties-delete]

**[はい、削除します (Yes, delete it)]** をクリックして確定します。

![削除の確定][api-management-delete-confirm]

> [!IMPORTANT]
> プロパティがポリシーに参照される場合、プロパティを使用するすべてのポリシーからプロパティを削除するまで削除は完了しません。
> 
> 

REST API を利用し、プロパティを削除する方法については、「 [Delete a property using the REST API (REST API を利用してプロパティを作成する)](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete)」を参照してください。

## <a name="to-search-and-filter-properties"></a>プロパティを検索し、フィルター処理するには
**[プロパティ]** タブには、プロパティの管理に役立つ検索とフィルター処理の機能があります。 プロパティ名でプロパティの一覧をフィルター処理するには、 **[検索プロパティ]** テキストボックスに検索語句を入力します。 すべてのプロパティを表示するには、 **[検索プロパティ]** テキストボックスを消去し、Enter を押します。

![Search][api-management-properties-search]

タグ値でプロパティの一覧をフィルター処理するには、 **[タグでフィルター]** テキストボックスに 1 つまたは複数のタグを入力します。 すべてのプロパティを表示するには、 **[タグでフィルター]** テキストボックスを消去し、Enter を押します。

![フィルター][api-management-properties-filter]

## <a name="next-steps"></a>次のステップ
* ポリシーの使用に関する説明
  * [API Management のポリシー](api-management-howto-policies.md)
  * [Policy reference (ポリシー リファレンス)](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [ポリシー式](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>ビデオの概要を見る
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Use-Properties-in-Policies/player]
> 
> 

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png




<!--HONumber=Dec16_HO2-->


