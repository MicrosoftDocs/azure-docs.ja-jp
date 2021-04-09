---
title: Azure 関数アプリを API として API Management にインポートする
titleSuffix: Azure API Management
description: この記事では、Azure Function App を API として Azure API Management にインポートする方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: f66395b1e0f45f1e80cd0ac93bf8c9ae8674a0f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732964"
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
* サブスクリプションに Azure Functions アプリがあることを確認します。 詳細については、[Azure Function App の作成](../azure-functions/functions-get-started.md)に関するページを参照してください。 Azure Function App には、HTTP トリガーと *Anonymous* または *Function* に設定された承認レベルの設定を備えた関数が含まれている必要があります。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a>Azure Function App を新しい API としてインポートする

以下の手順に従って、Azure Function App から新しい API を作成します。

1. Azure portal で API Management サービスに移動し、メニューから **[API]** を選択します。

2. **[Add a new API]\(新しい API の追加\)** の一覧で **[Functions App]\(Function App\)** を選択します。

    ![[Function App] タイルを示すスクリーンショット。](./media/import-function-app-as-api/add-01.png)

3. **[参照する]** をクリックして、インポートする Function を選択します。

    ![[参照する] ボタンが強調表示されているスクリーンショット。](./media/import-function-app-as-api/add-02.png)

4. **[Functions App]\(Function App\)** セクションをクリック、使用可能な Function App の一覧から選択します。

    ![[Function App] セクションが強調表示されているスクリーンショット。](./media/import-function-app-as-api/add-03.png)

5. 関数のインポート元となる Function App を検索してクリックし、 **[選択]** をクリックします。

    ![関数のインポート元となる Function App と [選択] ボタンが強調表示されているスクリーンショット。](./media/import-function-app-as-api/add-04.png)

6. インポートする Function を選択して、 **[選択]** をクリックします。

    ![インポートする Function と [選択] ボタンが強調表示されているスクリーンショット。](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > HTTP トリガーに基づかない Function のみをインポートして、承認レベル設定を *Anonymous* または *Function* に設定できます。

7. **[完全]** ビューに切り替え、 **[製品]** を新しい API に割り当てます。 必要に応じて、作成時に他のフィールドを指定することも、後で **[設定]** タブに移動して構成することもできます。設定については、「[最初の API のインポートと発行](import-and-publish.md#import-and-publish-a-backend-api)」のチュートリアルで説明されています。
8. **Create** をクリックしてください。

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Azure Function App を既存の API に追加する

以下の手順に従って、Azure Function App を既存の API に追加します。

1. お使いの **Azure API Management** サービス インスタンスで、左側のメニューから **[API]** を選択します。

2. Azure Function App のインポート先となる API を選択します。 **[...]** をクリックして、コンテキスト メニューから **[インポート]** を選択します。

    ![[インポート] メニュー オプションが強調表示されているスクリーンショット。](./media/import-function-app-as-api/append-01.png)

3. **[Function App]** タイルをクリックします。

    ![[Function App] タイルが強調表示されているスクリーンショット。](./media/import-function-app-as-api/append-02.png)

4. ポップアップ ウィンドウで、 **[参照]** をクリックします。

    ![[参照] ボタンを示すスクリーンショット。](./media/import-function-app-as-api/append-03.png)

5. **[Functions App]\(Function App\)** セクションをクリック、使用可能な Function App の一覧から選択します。

    ![Function App の一覧が強調表示されているスクリーンショット。](./media/import-function-app-as-api/add-03.png)

6. 関数のインポート元となる Function App を検索してクリックし、 **[選択]** をクリックします。

    ![関数のインポート元となる Function App が強調表示されているスクリーンショット。](./media/import-function-app-as-api/add-04.png)

7. インポートする Function を選択して、 **[選択]** をクリックします。

    ![インポートする関数が強調表示されているスクリーンショット。](./media/import-function-app-as-api/add-05.png)

8. **[インポート]** をクリックします。

    ![Function App から追加する](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a>承認

Azure Function App のインポートによって、次が自動的に生成されます。

* apim-{<*お使いの Azure API Management サービス インスタンス名*>} という名前の、Function App 内のホスト キー。
* {<*お使いの Azure Function App のインスタンス名*>}-key という名前の、Azure API Management インスタンス内の名前付きの値。作成されたホスト キーが含まれます。

2019 年 4 月 4 日より後に作成された API では、ホスト キーが HTTP 要求のヘッダーで API Management から Function App に渡されます。 以前の API では、ホスト キーが[クエリ パラメーター](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization)として渡されます。 Function App に関連付けられている *Backend* エンティティの `PATCH Backend` [REST API 呼び出し](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract)では、この動作が変わる可能性があります。

> [!WARNING]
> Azure Function App のホスト キーまたは Azure API Management の名前付きの値のいずれかの値を削除または変更すると、サービス間の通信が失われます。 値は自動的に同期されません。
>
> ホスト キーを交換する必要がある場合は、Azure API Management の名前付きの値も変更されることを確認します。

### <a name="access-azure-function-app-host-key"></a>Azure Function App のホスト キーにアクセスする

1. お使いの Azure Function App のインスタンスに移動します。

2. 概要から **[Function App settings]\(Function App の設定\)** を選択します。

    ![[Function App settings]\(Function App の設定\) オプションが強調表示されているスクリーンショット。](./media/import-function-app-as-api/keys-02-a.png)

3. キーは **[ホスト キー]** セクションに配置されています。

    ![[ホスト キー] セクションが強調表示されているスクリーンショット。](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Azure API Management の名前付きの値にアクセスする

お使いの Azure API Management インスタンスに移動し、左側のメニューから **[API]** をクリックします。 Azure Function App のキーは、そこに格納されています。

![Function App から追加する](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Azure portal での新しい API のテスト

操作は Azure portal から直接呼び出すことができます。 Azure portal は API の操作を表示およびテストするのに便利です。  

1. 前のセクションで作成した API を選択します。

2. **[テスト]** タブを選びます。

3. 操作を選択します。

    ページに、クエリ パラメーターのフィールドとヘッダーのフィールドが表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは **Ocp-Apim-Subscription-Key** です。 API Management インスタンスを作成した場合は、既に管理者になっているので、キーが自動的に入力されます。 

4. **[送信]** を選択します。

    バックエンドは **200 OK** といくつかのデータで応答します。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
