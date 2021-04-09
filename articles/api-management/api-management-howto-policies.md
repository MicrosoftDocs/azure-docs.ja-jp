---
title: Azure API Management のポリシー | Microsoft Docs
description: API Management のポリシーを作成、編集、構成する方法について説明します。 コード例を参照し、使用可能なその他のリソースを確認してください。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358118"
---
# <a name="policies-in-azure-api-management"></a>Azure API Management のポリシー

Azure API Management (APIM) のポリシーは、発行者がその構成を通じて API の動作を変更できる、システムの強力な機能の 1 つです。 ポリシーは、API の要求または応答に対して順に実行される一連のステートメントのコレクションです。 代表的なステートメントとしては、XML 形式から JSON 形式への変換や、(開発者からの呼び出しの回数を制限する) 呼び出しレート制限が挙げられます。 他にも多数のポリシーが標準で提供されています。

ポリシーは、API コンシューマーとマネージド API の間に配置されたゲートウェイ内で適用されます。 ゲートウェイは、すべての要求を受け取り、通常はそれらの要求をそのまま基底の API に転送します。 ただし、ポリシーを使用すると、受信要求と送信応答の両方に変更を適用できます。

ポリシーの式は、ポリシーで特に指定されていない限り、任意の API Management ポリシーで属性値またはテキスト値として使用できます。 [制御フロー][Control flow] ポリシーや[変数の設定][Set variable]ポリシーなど、一部のポリシーはポリシーの式に基づいています。 詳細については、「[詳細なポリシー][Advanced policies]」と「[ポリシーの式][Policy expressions]」をご覧ください。

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>ポリシー構成について

ポリシー定義は、一連の受信ステートメントと送信ステートメントが記述された単純な XML ドキュメントです。 XML は、定義ウィンドウで直接編集できます。 ウィンドウの右側にはステートメントの一覧が表示され、現在のスコープに適用できるステートメントが有効になり強調表示されます。

有効なステートメントをクリックすると、定義ビュー内のカーソル位置に適切な XML が追加されます。 

> [!NOTE]
> 追加するポリシーが有効になっていない場合、そのポリシー用の正しいスコープが選択されていることを確認してください。 各ポリシー ステートメントは、特定のスコープおよびポリシー セクション内で使用するように設計されています。 ポリシーのポリシー セクションとスコープを確認するには、[ポリシー リファレンス][Policy Reference]に関するページでそのポリシーの **使用例** を参照してください。
> 
> 

構成は `inbound`、`backend`、`outbound`、および `on-error` に分かれます。 要求および応答に対して、指定された一連のポリシー ステートメントが順に実行されます。

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

要求の処理中にエラーが発生した場合、`inbound`、`backend`、または `outbound` セクションの残りの手順はスキップされ、実行は `on-error` セクションのステートメントにジャンプします。 `on-error` セクションにポリシー ステートメントを配置することで、`context.LastError` プロパティを使用してエラーを確認し、`set-body` ポリシーを使用してエラーの検査とカスタマイズを行い、エラーが発生した場合の動作を構成できます。 組み込み手順用と、ポリシー ステートメントの処理中に発生する可能性があるエラー用のエラー コードがあります。 詳細については、 [API Management のポリシーにおけるエラー処理](./api-management-error-handling-policies.md)に関するページを参照してください。

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>ポリシーの構成方法

ポリシーの構成方法については、[ポリシーの設定と編集](set-edit-policies.md)に関する記事をご覧ください。

## <a name="policy-reference"></a>ポリシー リファレンス

ポリシー ステートメントとその設定の一覧については、[ポリシー リファレンス](./api-management-policies.md)に関する記事をご覧ください。

## <a name="policy-samples"></a>ポリシーのサンプル

コード サンプルの詳細については、[ポリシーのサンプル](./policy-reference.md)に関する記事をご覧ください。

## <a name="examples"></a>例

### <a name="apply-policies-specified-at-different-scopes"></a>さまざまなスコープで指定されたポリシーを適用する

グローバル レベルのポリシーと API 向けに構成されたポリシーがある場合、API が使用されるたびに両方のポリシーが適用されます。 API Management では、基本要素を介してポリシー ステートメントの組み合わせの順序を指定できます。 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

上のポリシー定義の例では、`cross-domain` ステートメントが上位のポリシーよりも前に実行され、その後に `find-and-replace` ポリシーが続いています。 

### <a name="restrict-incoming-requests"></a>受信要求を制限する

新しいステートメントを追加して受信要求を指定された IP アドレスに制限するには、`inbound` XML 要素内にカーソルを置き、**[Restrict caller IPs]\(呼び出し元 IP の制限\)** ステートメントをクリックします。

![制限ポリシー][policies-restrict]

これで、ステートメントを構成する方法を示す XML スニペットが `inbound` 要素に追加されます。

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

受信要求を制限して、IP アドレス 1.2.3.4 からの要求のみを受け付けるには、次のように XML を変更します。

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>次のステップ

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
+ [ポリシーのサンプル](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
