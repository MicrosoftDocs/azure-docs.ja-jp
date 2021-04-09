---
title: 改ページ位置の自動修正で項目やレコードを増やす
description: Azure Logic Apps で、コネクタ アクションの既定のページ サイズの上限を超過するように改ページ位置の自動修正を設定する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87090265"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Azure Logic Apps で改ページ位置の自動修正を使用し、データ、項目、レコードを増やす

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) でコネクタ アクションを使用してデータ、項目、レコードを受け取るとき、得られた結果セットが大きすぎて、全部の結果を同時に返せないことがあります。 アクションによっては、結果の数がコネクタの既定のページ サイズを超えることがあります。 この場合、アクションは、結果の最初のページのみを返します。 たとえば、SQL Server コネクタの **行の取得** アクションの既定のページ サイズは 2048 ですが、その他の設定によって変わることがあります。

一部のアクションでは、改ページ位置の自動修正の上限までロジック アプリで受け取る結果を増やせますが、アクションが完了したとき、それらの結果を 1 つのメッセージとして返すよう、*改ページ位置の自動修正* 設定をオンにできます。 改ページ位置の自動修正を使用するとき、アクションで返す結果の目標数である *しきい値* を指定する必要があります。 このアクションでは、指定したしきい値に到達するまで結果が取得されます。 項目の合計数が指定のしきい値より少ないとき、このアクションではすべての結果が受け取られます。

改ページ位置の自動修正設定をオンにすると、コネクタのページ サイズに基づいて結果のページが受け取られます。 この動作は、指定したしきい値よりも多く結果を受け取る可能性があることを意味します。 たとえば、次の改ページ位置の自動修正設定をサポートする、SQL Server の **行の取得** アクションの使用時です。

* このアクションの既定のページ サイズはページあたり 2,048 件のレコードです。
* 10,000 件のレコードがあるとき、5,000 件を最小として指定するとします。
* 改ページ位置の自動修正により、レコードのページが取得されます。そのため、少なくとも指定の最小レコードを得るために、このアクションで 5,000 件ではなく、6,144 件のレコード (3 ページ x 2048 件) が返されます。

特定のアクションで既定のページ サイズを超過できるコネクタには次のものがあります。

* [Azure Blob Storage](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Microsoft Teams](/connectors/teams/)
* [Oracle データベース](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 改ページ位置の自動修正をオンにするロジック アプリとアクション。 ロジック アプリがない場合は、[クイック スタート:初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="turn-on-pagination"></a>改ページ位置の自動修正をオンにする

Logic App Designer の、あるアクションで改ページ位置の自動修正がサポートされるかどうかを判断するには、アクションの設定で **改ページ位置の自動修正** 設定を確認します。 この例では、SQL Server の **行の取得** アクションで改ページ位置の自動修正をオンにする方法を示します。

1. アクションの右上隅で、省略記号 ( **...** ) ボタンを選択し、 **[設定]** を選択します。

   ![アクションの設定を開く](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   そのアクションで改ページ位置の自動修正がサポートされる場合、そのアクションに **改ページ位置の自動修正** 設定が表示されます。

1. **[改ページ位置の自動修正]** 設定を **[オフ]** から **[オン]** に変更します。 **[しきい値]** プロパティに、そのアクションで返す結果の目標数として整数値を指定します。

   ![返される結果の最小数を指定します。](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 準備ができたら、 **[完了]** を選択します。

## <a name="workflow-definition---pagination"></a>ワークフロー定義 - 改ページ位置の自動修正

この機能がサポートされるアクションに対して改ページ位置の自動修正をオンにすると、ロジック アプリにワークフロー定義に、そのアクションの `"runtimeConfiguration"` プロパティの `"minimumItemCount"` プロパティと共に、`"paginationPolicy"` プロパティが含まれます。次の例をご覧ください。

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>サポートを受ける

ご質問がある場合は、[Azure Logic Apps に関する Microsoft Q&A 質問ページ](/answers/topics/azure-logic-apps.html)を参照してください。
