---
title: 請求書 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer API を使用した請求書分析に関連する概念 (使用法と制限) について説明します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 46cf34bd40832488985008a645f1da25eb87b9d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467393"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Form Recognizer の事前構築済み請求書モデル

Azure Form Recognizer を使用すると、事前構築済みの請求書モデルを使用して、販売請求書から情報を分析して抽出できます。 Invoice API を使用すると、お客様は、さまざまな形式の請求書を受け取り、構造化されたデータを返すことで、請求書の処理を自動化することができます。 強力な[光学式文字認識 (OCR)](../computer-vision/concept-recognizing-text.md) 機能と、請求書を解釈するディープ ラーニング モデルを組み合わせて、英語の請求書から重要な情報を抽出します。 顧客、仕入先、請求書 ID、請求書の期限、合計、請求金額、課税額、出荷先、請求先、品目などのテキスト、テーブル、情報が抽出されます。 Form Recognizer v2.1 プレビューでは、事前構築済みの Invoice API が一般公開されています。

## <a name="what-does-the-invoice-service-do"></a>請求書サービスの機能

Invoice API を呼び出すと、請求書から主要なフィールドと品目が抽出され、構造化された JSON 応答に整理されて返されます。 請求書は、携帯電話でキャプチャされた画像、スキャンされたドキュメント、デジタル PDF など、形式や品質が異なっていてもかまいません。 Invoice API により、このようなすべての請求書から、構造化された出力が抽出されます。 

![Contoso の請求書の例](./media/invoice-example-new.jpg)

## <a name="try-it-out"></a>試してみる

Form Recognizer の請求書サービスを試してみるには、オンラインのサンプル UI ツールにアクセスしてください。

> [!div class="nextstepaction"]
> [事前構築済みモデルを試す](https://fott-preview.azurewebsites.net/)

Form Recognizer の請求書サービスを試すには、Azure サブスクリプション ([無料で作成](https://azure.microsoft.com/free/cognitive-services)) および [Form Recognizer リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)のエンドポイントとキーが必要です。 

:::image type="content" source="media/analyze-invoice-new.png" alt-text="分析済みの請求書の例" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>入力の要件

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>請求書分析操作

[請求書分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)操作に請求書の画像または PDF を入力として渡すと、目的の値が抽出されます。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用される結果 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>請求書分析結果取得操作

2 番目の手順では、[請求書分析結果取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83)操作を呼び出します。 この操作には、請求書分析操作によって作成された結果 ID を入力として渡します。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 **succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求数 (RPS) を超えないようにするために、間隔は 3 - 5 秒あけてください。

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | notStarted: 分析操作は開始されていません。<br /><br />running: 分析操作が進行中です。<br /><br />failed: 分析操作は失敗しました。<br /><br />succeeded: 分析操作は成功しました。|

**status** フィールドの値が **succeeded** の場合、JSON の応答には請求書の解釈の結果、抽出されたテーブル、および必要に応じてテキスト認識の結果 (要求した場合) が含まれます。 請求書解釈の結果は名前付きフィールドの値の辞書として編成され、各値には、抽出されたテキスト、正規化された値、境界ボックス、信頼度、対応する単語要素が含まれます。 また、各品目には、金額、説明、単価、数量などが含まれている場合に抽出された品目も含まれます。テキスト認識の結果は行と単語の階層として編成され、テキスト、境界ボックス、信頼情報が含まれます。

### <a name="sample-json-output"></a>サンプル JSON 出力

請求書分析結果取得操作への応答は、抽出されたすべての情報が含まれる請求書の構造化表現になります。 [サンプルの請求書ファイル](media/sample-invoice.jpg)と、その構造化された出力である[サンプルの請求書出力](media/invoice-example-new.jpg)を参照してください。

JSON 出力には次の 3 つの部分があります。 
* `"readResults"` ノードには、認識されたすべてのテキストと選択マークが格納されます。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 
* `"pageResults"` ノードには、境界ボックスで抽出されたテーブルとセル、信頼度、および "readResults" 内の行と単語への参照が格納されます。
* `"documentResults"` ノードには、モデルによって検出された請求書固有の値と品目が格納されます。 ここで、請求書 ID、出荷先、請求先、顧客、合計、品目など、請求書のすべてのフィールドを確認できます。

## <a name="example-output"></a>出力例

請求書サービスにより、テキスト、テーブル、および 26 個の請求書フィールドが抽出されます。 次に示すのは、JSON の出力応答で請求書から抽出されるフィールドです (以下の出力では、こちらの[サンプル請求書](media/sample-invoice.jpg)が使用されています)。

|名前| Type | 説明 | Text | 値 (標準化された出力) |
|:-----|:----|:----|:----| :----|
| CustomerName | string | 請求対象の顧客 | Microsoft Corp |  |
| CustomerId | string | 顧客の参照 ID | CID-12345 |  |
| PurchaseOrder | string | 注文書の参照番号 | PO-3333 | | 
| InvoiceId | string | この特定の請求書の ID (多くの場合、"請求書番号") | INV-100 | | 
| InvoiceDate | date | 請求書の発行日 | 2019 年 11 月 15 日 | 2019-11-15 | 
| DueDate | date | この請求書の支払期日 | 12/15/2019 | 2019-12-15 |
| VendorName | string | この請求書を作成したベンダー | CONTOSO LTD. | |
| VendorAddress | string | ベンダーの住所 | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | string | VendorAddress に関連付けられている名前 | Contoso Headquarters | |
| CustomerAddress | string | 顧客の住所 | 123 Other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | string | CustomerAddress に関連付けられている名前 | Microsoft Corp | |
| BillingAddress | string | 顧客の明示的な請求先住所 | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | string | BillingAddress に関連付けられている名前 | Microsoft サービス | |
| ShippingAddress | string | 顧客の明示的な送付先住所 | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | string | ShippingAddress に関連付けられている名前 | Microsoft Delivery | |
| SubTotal | 数値 | この請求書で識別された小計フィールド | $100.00 | 100 | 
| TotalTax | 数値 | この請求書で識別された合計税額フィールド | $10.00 | 10 |
| InvoiceTotal | 数値 | この請求書に関連付けられている合計新規料金 | $110.00 | 110 |
| AmountDue |  数値 | ベンダーに対する合計金額 | $610.00 | 610 |
| ServiceAddress | string | 顧客の明示的なサービス住所または物件所在地 | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | string | ServiceAddress に関連付けられている名前 | Microsoft サービス | |
| RemittanceAddress | string | 顧客の明示的な送金住所または支払住所 | 123 Remit St New York, NY, 10001 |  |
| RemittanceAddressRecipient | string | RemittanceAddress に関連付けられている名前 | Contoso Billing |  |
| ServiceStartDate | date | サービス期間の最初の日 (たとえば、公共料金サービス期間) | 2019 年 10 月 14 日 | 2019-10-14 |
| ServiceEndDate | date | サービス期間の最後の日 (たとえば、公共料金サービス期間) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | 数値 | 明示的な以前の未払い残高 | $500.00 | 500 |

次に示すのは、JSON の出力応答で請求書から抽出される品目です (以下の出力では、こちらの[サンプル請求書](./media/sample-invoice.jpg)が使用されています)  

|名前| Type | 説明 | テキスト (品目 #1) | 値 (標準化された出力) |
|:-----|:----|:----|:----| :----|
| アイテム | string | 品目の完全な文字列テキスト行 | 3/4/2021 A123 Consulting Services 2 hours $30.00 10% $60.00 | |
| Amount | 数値 | 品目の金額 | $60.00 | 100 |
| 説明 | string | 請求書の明細項目の説明テキスト | コンサルティング サービス | コンサルティング サービス |
| 数量 | 数値 | この請求書明細項目の数量 | 2 | 2 |
| UnitPrice | 数値 | この項目の 1 つの単位の正味価格または総価格 (請求書の総請求書の設定によって異なります) | $30.00 | 30 |
| ProductCode | string| 特定の品目に関連付けられている製品コード、製品番号、または SKU | A123 | |
| ユニット | string| 品目の単位 (kg、lb など) | 時間 | |
| Date | 日付| 各品目に対応する日付。 多くの場合、これは品目が発送された日付です | 3/4/2021| 2021-03-04 |
| 税 | 数値 | 各品目に関連付けられている税。 使用可能な値には、課税額、税金 (%)、税金 (Y/N) などがあります | 10% | |


## <a name="next-steps"></a>次のステップ

- [Form Recognizer サンプル UI](https://fott-preview.azurewebsites.net/) で、独自の請求書とサンプルを試してみてください。
- 選択した開発言語で Form Recognizer を使用して請求書処理アプリの作成を始めるには、[Form Recognizer のクイックスタート](quickstarts/client-library.md)を完了します。

## <a name="see-also"></a>関連項目

* [Form Recognizer とは](./overview.md)
* [REST API リファレンス ドキュメント](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
