---
title: Azure API Management で Azure Function App を API としてインポートする | Microsoft Docs
description: このチュートリアルでは、Azure Function App を API として Azure API Management にインポートする方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: apimpm
ms.openlocfilehash: fe6a008a6cbd2ca4e8aedeeca6d96cc00f6b29d1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046055"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Azure API Management で Azure Function App を API としてインポートする

Azure API Management は、Azure Function App の新しい API としてのインポートや既存の API への追加をサポートします。 プロセスが自動的に Azure Function App 内にホスト キーを生成して、Azure API Management 内の名前付きの値に割り当てます。

この記事では、Azure API Management で Azure Function App を API としてインポートする手順について説明します。 また、テストのプロセスについても説明します。

学習内容:

> [!div class="checklist"]
> * Azure Function App の API としてのインポート
> * Azure Function App の API への追加
> * 新しい Azure Function App のホスト キーと Azure API Management の名前付きの値の表示
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

## <a name="prerequisites"></a>前提条件

* [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイック スタートを完了します。
* サブスクリプションに Azure Functions アプリがあることを確認します。 詳細については、[Azure Function App の作成](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)に関するページを参照してください。 Azure Function App には、HTTP トリガーと *Anonymous* または *Function* に設定された承認レベルの設定を備えた関数が含まれている必要があります。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-new-api-from-azure-function-app"></a>Azure Function App を新しい API としてインポートする

以下の手順に従って、Azure Function App から新しい API を作成します。

1. お使いの **Azure API Management** サービス インスタンスで、左側のメニューから **[API]** を選択します。

2. **[Add a new API]\(新しい API の追加\)** の一覧で **[Functions App]\(Function App\)** を選択します。

    ![Function App から追加する](./media/import-function-app-as-api/add-01.png)

3. **[参照する]** をクリックして、インポートする Function を選択します。

    ![Function App から追加する](./media/import-function-app-as-api/add-02.png)

4. **[Functions App]\(Function App\)** セクションをクリック、使用可能な Function App の一覧から選択します。

    ![Function App から追加する](./media/import-function-app-as-api/add-03.png)

5. 関数のインポート元となる Function App を検索してクリックし、**[選択]** をクリックします。

    ![Function App から追加する](./media/import-function-app-as-api/add-04.png)

6. インポートする Function を選択して、**[選択]** をクリックします。

    ![Function App から追加する](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > HTTP トリガーに基づかない Function のみをインポートして、承認レベル設定を *Anonymous* または *Function* に設定できます。 現時点では、Linux 関数アプリはサポートされていません。

7. **[完全]** ビューに切り替え、**[製品]** を新しい API に割り当てます。 必要に応じて、他の事前に設定されているフィールドを編集します。

    ![Function App から追加する](./media/import-function-app-as-api/add-06.png)

8. **Create** をクリックしてください。

## <a name="append-azure-function-app-to-api"></a> Azure Function App を既存の API に追加する

以下の手順に従って、Azure Function App を既存の API に追加します。

1. お使いの **Azure API Management** サービス インスタンスで、左側のメニューから **[API]** を選択します。

2. Azure Function App のインポート先となる API を選択します。 **[...]** をクリックして、コンテキスト メニューから **[インポート]** を選択します。

    ![Function App から追加する](./media/import-function-app-as-api/append-01.png)

3. **[Function App]** タイルをクリックします。

    ![Function App から追加する](./media/import-function-app-as-api/append-02.png)

4. ポップアップ ウィンドウで、**[参照]** をクリックします。

    ![Function App から追加する](./media/import-function-app-as-api/append-03.png)

5. **[Functions App]\(Function App\)** セクションをクリック、使用可能な Function App の一覧から選択します。

    ![Function App から追加する](./media/import-function-app-as-api/add-03.png)

6. 関数のインポート元となる Function App を検索してクリックし、**[選択]** をクリックします。

    ![Function App から追加する](./media/import-function-app-as-api/add-04.png)

7. インポートする Function を選択して、**[選択]** をクリックします。

    ![Function App から追加する](./media/import-function-app-as-api/add-05.png)

8. **[インポート]** をクリックします。

    ![Function App から追加する](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a>承認

Azure Function App のインポートによって、次が自動的に生成されます。

* apim-{<*お使いの Azure API Management サービス インスタンス名*>} という名前の、Function App 内のホスト キー。
* {<*お使いの Azure Function App のインスタンス名*>}-key という名前の、Azure API Management インスタンス内の名前付きの値。作成されたホスト キーが含まれます。

2019 年 4 月 4 日より後に作成された API では、ホスト キーが HTTP 要求のヘッダーで API Management から Function App に渡されます。 以前の API では、ホスト キーが[クエリ パラメーター](../azure-functions/functions-bindings-http-webhook.md#api-key-authorization)として渡されます。 Function App に関連付けられている *Backend* エンティティの`PATCH Backend` [REST API 呼び出し](https://docs.microsoft.com/rest/api/apimanagement/backend/update#backendcredentialscontract)では、この動作が変わる可能性があります。

> [!WARNING]
> Azure Function App のホスト キーまたは Azure API Management の名前付きの値のいずれかの値を削除または変更すると、サービス間の通信が失われます。 値は自動的に同期されません。
>
> ホスト キーを交換する必要がある場合は、Azure API Management の名前付きの値も変更されることを確認します。

### <a name="access-azure-function-app-host-key"></a>Azure Function App のホスト キーにアクセスする

1. お使いの Azure Function App のインスタンスに移動します。

2. 概要から **[Function App settings]\(Function App の設定\)** を選択します。

    ![Function App から追加する](./media/import-function-app-as-api/keys-02-a.png)

3. キーは **[ホスト キー]** セクションに配置されています。

    ![Function App から追加する](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Azure API Management の名前付きの値にアクセスする

お使いの Azure API Management インスタンスに移動し、左側のメニューから **[API]** をクリックします。 Azure Function App のキーは、そこに格納されています。

![Function App から追加する](./media/import-function-app-as-api/keys-01.png)

## <a name="test-in-azure-portal"></a> Azure Portal での新しい API Management API のテスト

操作は Azure portal から直接呼び出すことができます。 Azure portal は API の操作を表示およびテストするのに便利です。  

1. 前のセクションで作成した API を選択します。

2. **[テスト]** タブを選びます。

3. 操作を選択します。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは **Ocp-Apim-Subscription-Key** です。 API Management インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 

4. **[送信]** を選択します。

    バックエンドは **200 OK** といくつかのデータで応答します。

## <a name="test-in-developer-portal"></a> 開発者ポータルから操作を呼び出す

操作を開発者ポータルから呼び出して API をテストすることもできます。 

1. 「バックエンド API のインポートと公開」で作成した API を選択します。

2. **[開発者ポータル]** を選択します。

    開発者ポータルのサイトが開きます。

3. 作成した **API** を選びます。

4. テストする操作を選択します。

5. **[Try it]\(試してみる\)** を選択します。

6. **[送信]** を選択します。
    
    操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
