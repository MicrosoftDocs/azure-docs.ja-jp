---
title: Dropbox に接続する - Azure Logic Apps
description: Dropbox REST API と Azure Logic Apps を使用して、ファイルをアップロードおよび管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 4e0689454ec074348fcbc775373a48d6825cfac4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050981"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Dropbox 内のファイルをアップロードおよび管理する

Dropbox コネクタと Azure Logic Apps を使用すると、Dropbox アカウントでファイルをアップロードおよび管理する自動化されたワークフローを作成できます。 

この記事では、ロジック アプリから Dropbox に接続して、Dropbox の**ファイルの作成時**トリガーと、Dropbox の**パスを使用したファイル コンテンツの取得**アクションを追加する方法を示しています。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 無料でサインアップできる [Dropbox アカウント](https://www.dropbox.com/)。 ロジック アプリと Drobox アカウント間の接続を作成するには、アカウントの資格情報が必要です。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 この例では、空のロジック アプリが必要となります。

## <a name="add-trigger"></a>トリガーの追加

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 検索ボックスで、 **[すべて]** を選択します。 検索ボックスに、フィルターとして「dropbox」と入力します。
トリガーの一覧から、**ファイルの作成時**

   ![Dropbox トリガーを選択](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. お使いの Dropbox アカウントの資格情報でサインインし、Dropbox データにアクセスすることについての承認を Azure Logic Apps に与えます。

1. トリガーに必要な情報を入力します。 

   この例では、ファイルの作成を追跡するフォルダーを選択します。 フォルダーを参照するには、 **[フォルダー]** ボックスの横のフォルダー アイコンを選択します。

## <a name="add-action"></a>アクションを追加する

新しいファイルからコンテンツを取得するアクションを追加します。

1. トリガーで、 **[次のステップ]** を選択します。 

1. 検索ボックスで、 **[すべて]** を選択します。 検索ボックスに、フィルターとして「dropbox」と入力します。
アクションの一覧から、次のアクションを選択します。**パスを使用してファイルの内容を取得する**

1. Azure Logic Apps の Dropbox へのアクセスをまだ承認していない場合、ここでアクセスを承認します。

1. 使用するファイル パスを参照するには、 **[ファイル パス]** ボックスの横の省略記号 ( **...** ) ボタンを選択します。 

   **[ファイル パス]** ボックス内をクリックし、動的コンテンツ リストから、 **[ファイル パス]** を選択することもできます。この値は、前のセクションで追加したトリガーからの出力として取得できます。

1. 完了したら、ロジック アプリを保存します。

1. ロジック アプリをトリガーするには、Dropbox に新しいファイルを作成します。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Open API (以前の Swagger) ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/dropbox/)を参照してください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
