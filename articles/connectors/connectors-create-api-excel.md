---
title: Excel Online のデータ、ワークシート、およびテーブルを管理する
description: Azure Logic Apps を使用して、Excel Online for Business または Excel Online for OneDrive のワークシートおよびテーブルのデータを管理します。
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400725"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Azure Logic Apps を使用して Excel Online データを管理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Excel Online for Business](/connectors/excelonlinebusiness/) コネクタまたは [Excel Online for OneDrive](/connectors/excelonline/) コネクタを使用して、Excel Online for Business または OneDrive のデータに基づいて自動化されたタスクおよびワークフローを作成できます。 このコネクタは、データの操作とスプレッドシートの管理を行う際に役立つアクションを提供します。たとえば、次のアクションがあります。

* 新しいワークシートとテーブルを作成します。
* ワークシート、テーブル、および行の取得と管理を行います。
* 行とキー列を追加します。

これらのアクションからの出力を他のサービスのアクションで使用できます。 たとえば、ワークシートを毎週作成するアクションを使用する場合、Office 365 Outlook コネクタを使用して確認電子メールを送信する別のアクションを使用できます。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

> [!NOTE]
> [Excel Online for Business](/connectors/excelonlinebusiness/) コネクタと [Excel Online for OneDrive](/connectors/excelonline/) コネクタは、Azure Logic Apps と連携し、[PowerApps 用の Excel コネクタ](/connectors/excel/)とは異なります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 職場アカウントまたは個人の Microsoft アカウント用の[職場または学校アカウント](https://www.office.com/)

  Excel データは、OneDrive などのストレージ フォルダーに、コンマ区切り値 (CSV) ファイルとして存在できます。 
  同じ CSV ファイルを、[フラット ファイル コネクタ](../logic-apps/logic-apps-enterprise-integration-flatfile.md)で使用することもできます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Excel Online データにアクセスするためのロジック アプリ。 このコネクタではアクションのみが提供されるため、ロジック アプリを開始するには、**繰り返し** トリガーなどのトリガーを別に選択してください。

## <a name="add-excel-action"></a>Excel アクションを追加する

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. トリガーで、 **[新しいステップ]** を選択します。

1. 検索ボックスに、フィルターとして「excel」と入力します。 アクションの一覧で、目的のアクションを選択します。

   > [!NOTE]
   > ロジック アプリ デザイナーでは、100 以上の列を含むテーブルを読み込むことはできません。 可能であれば、デザイナーでテーブルを読み込めるように、選択したテーブルの列数を減らします。

1. メッセージが表示されたら、職場または学校のアカウントにサインインします。

   Excel Online への接続を作成してデータにアクセスしてよいという承認が、この資格情報によってロジック アプリに与えられます。

1. 選択したアクションで必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、コネクタのリファレンス ページを参照してください。

* [Excel Online for Business](/connectors/excelonlinebusiness/)
* [Excel Online for OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
