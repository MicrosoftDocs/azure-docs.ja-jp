---
title: Microsoft OneDrive 内のファイルにアクセスして管理する
description: Azure Logic Apps で自動化されたワークフローを作成することによって OneDrive 内のファイルをアップロードおよび管理する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 9fe4988b5499943f4b11ec5d640209ceb68e84ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87040221"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して OneDrive コネクタ内のファイルにアクセスして管理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [OneDrive コネクタ](/connectors/onedriveconnector/)を使用すると、ファイルのアップロード、取得、削除など、ファイルを管理するための自動化されたタスクとワークフローを作成できます。 OneDrive を使用して、次のタスクを実行できます。

* OneDrive にファイルを保存してワークフローを構築するか、OneDrive 内の既存ファイルを更新できます。 
* OneDrive 内でファイルが作成または更新されたときに、トリガーを使用してワークフローを開始できます。
* ファイルの作成、ファイルの削除などのアクションを使用できます。 たとえば、添付ファイル付きの新しい Office 365 電子メールを受信したときに (トリガー)、OneDrive に新しいファイルを作成します (アクション)。

この記事では、ロジック アプリ内で OneDrive コネクタを使用する方法を説明し、トリガーとアクションの一覧を示します。

Logic Apps の詳細については、「[Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="connect-to-onedrive"></a>OneDrive に接続する

ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "*接続*" を作成します。 接続により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、OneDrive に接続するには、最初に OneDrive "*接続*" が必要です。 接続を作成するには、接続対象のサービスへのアクセスに通常使用する資格情報を入力します。 そのため、OneDrive の場合は、OneDrive アカウントの資格情報を入力して接続を作成します。

### <a name="create-the-connection"></a>接続の作成

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>トリガーを使用する

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーは、指定された間隔と頻度でサービスを "ポーリング" します。 トリガーの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。

1. ロジック アプリ デザイナーで、「`onedrive`」と入力して、トリガーの一覧を取得します。  

   ![[Microsoft のマネージド API を表示] というタイトルのダイアログ ボックスには、"onedrive" と表示されているボックスがあります。 以下は、4 つのトリガーの一覧です。 1 つ目は [OneDrive - ファイルが作成されたとき] です。 2 つ目の [OneDrive - ファイルが変更されたとき] が選択されています。](./media/connectors-create-api-onedrive/onedrive-1.png)

2. **[When a file is modified (ファイルの変更時)]** を選択します。 接続が既に存在する場合は、[ピッカーの表示] ボタンを選択してフォルダーを選択します。

   ![[ファイルが変更されたとき] というタイトルのダイアログ ボックスには、関連付けられている参照ボタンを持つ [フォルダー] というタイトルのボックスがあります。](./media/connectors-create-api-onedrive/sample-folder.png)

   サインインを求められたら、サインインの詳細を入力して接続を作成します。 その手順については、この記事の「[接続の作成](connectors-create-api-onedrive.md#create-the-connection)」を参照してください。

   この例では、選択したフォルダー内のファイルが更新されたときに、ロジック アプリが実行されます。 このトリガーの結果を確認するには、自身に電子メールを送信する別のアクションを追加してください。 たとえば、Office 365 Outlook の "*電子メールを送信する*" アクションを追加します。これにより、ファイルが更新されると電子メールが送信されます。

3. **[編集]** を選択し、 **[頻度]** と **[間隔]** の値を設定します。 たとえば、トリガーを使用して 15 分ごとにポーリングを実行するには、 **[頻度]** を **[分]** に設定し、 **[間隔]** を **15** に設定します。 

   ![[ファイルが変更されたとき] というタイトルのダイアログ ボックスには、次のラベル付きの 5 つのボックスが表示されます。[フォルダー]、[頻度]、[間隔]、[タイムゾーン]、および [開始時刻] です。 [頻度] フィールドと [タイム ゾーン] フィールドには、ドロップダウン リストがあります。](./media/connectors-create-api-onedrive/trigger-properties.png)

4. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

## <a name="use-an-action"></a>アクションを使用する

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。

1. プラス記号を選択します。 いくつかの選択肢が表示されます。 **[アクションの追加]** 、 **[条件の追加]** 、 **[その他]** です。

   ![[+ 新しいステップ]、[アクションの追加]、[条件の追加]、および [... その他] という 4 つのボタンを示すスクリーンショット。](./media/connectors-create-api-onedrive/add-action.png)

2. **[アクションの追加]** を選択します。

3. 検索ボックスに「`onedrive`」と入力して、使用可能なすべてのアクションの一覧を取得します。

   ![[Microsoft のマネージド API を表示] というタイトルのダイアログ ボックスには、"onedrive" と表示されているボックスがあります。 以下は、8 個のアクションの一覧です。 1 つ目は [OneDrive - ファイルの作成] で、これが選択されています。](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. この例では、 **[OneDrive - Create file (OneDrive - ファイルを作成する)]** を選択します。 接続が既に存在する場合は、 **[フォルダー パス]** で、ファイルを配置する場所を選択し、 **[ファイル名]** に入力して、**目的のファイルのコンテンツ** を選択します。  

   ![[ファイルの作成] というタイトルのダイアログ ボックスには、[フォルダーのパス]、[ファイル名]、および [フォルダーの内容] というラベルの付いた 3 つのボックスが表示されます。 [フォルダーのパス] ボックスの横には、ディレクトリの参照ボタンがあります。](./media/connectors-create-api-onedrive/sample-action.png)

   接続情報の入力を求められたら、このトピックの説明に従って詳細を入力して[接続を作成します](#create-the-connection)。

   この例では、OneDrive フォルダーに新しいファイルを作成します。 別のトリガーからの出力を使用して、OneDrive ファイルを作成できます。 たとえば、Office 365 Outlook の "*When a new email arrives (新しい電子メールが届いたとき)* " トリガーを追加します。 次に、OneDrive の "*Create file (ファイルを作成する)* " アクションを追加します。このアクションは、ForEach 内の Attachments フィールドと Content-Type フィールドを使用して OneDrive に新しいファイルを作成します。

   ![[For each] というタイトルのダイアログ ボックスには、[以前の手順から出力を選択] というラベルの付いたボックスがあり、それには "アタッチメント" と表示されています。 [For each] ボックスの残りに対応する [ファイルの作成] ダイアログ ボックスがあり、[フォルダーのパス]、[ファイル名]、および [ファイルのコンテンツ] というラベルが付いたボックスがあります。 ](./media/connectors-create-api-onedrive/foreach-action.png)

5. ツール バーの左上隅にある **[保存]** を選択して変更を保存します。 ロジック アプリが保存され、場合によっては、自動的に有効になります。

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/onedriveconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps のコネクタ](apis-list.md)