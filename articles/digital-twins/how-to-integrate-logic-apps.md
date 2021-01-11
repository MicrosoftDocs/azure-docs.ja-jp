---
title: Logic Apps との統合
titleSuffix: Azure Digital Twins
description: カスタム コネクタを使用して Logic Apps を Azure Digital Twins に接続する方法について説明します
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 2fc2db54217756ba0f4f7d643b1bc12ad2668209
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88848664"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>カスタム コネクタを使用して Logic Apps と統合する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、アプリとサービス全体のワークフローを自動化するのに役立つクラウド サービスです。 Logic Apps を Azure Digital Twins API に接続すると、Azure Digital Twins とそのデータに関するそのような自動化されたフローを作成できます。

現在、Azure Digital Twins に、Logic Apps 用の認定された (構築済みの) コネクタはありません。 代わりに、Azure Digital Twins で Logic Apps を使用するための現在のプロセスでは、Logic Apps で動作するように変更された[カスタム Azure Digital Twins Swagger ファイル](https://github.com/Azure-Samples/digital-twins-custom-swaggers/blob/main/LogicApps/preview/2020-05-31-preview/digitaltwins.json)を使用して、[**カスタム Logic Apps コネクタ**](../logic-apps/custom-connector-overview.md)を作成します。

この記事では、[Azure portal](https://portal.azure.com) を使用して、Azure Digital Twins インスタンスに Logic Apps を接続するために使用できる**カスタム コネクタを作成**します。 その後、シナリオの例にこの接続を使用する**ロジック アプリを作成**します。ここでは、タイマーによってトリガーされるイベントにより、Azure Digital Twins インスタンスのツインが自動的に更新されます。 

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に **[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成**してください。

このアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを設定する

この記事で Azure Digital Twins インスタンスを Logic Apps に接続するには、**Azure Digital Twins インスタンス**を先に設定しておく必要があります。 

ここで新しいインスタンスを設定する必要がある場合、最も簡単な方法は、自動デプロイ スクリプト サンプルを実行することです。 ["*AutoRest を使用して インスタンスと認証を設定する (スクリプト化)* ](how-to-set-up-instance-scripted.md)" 方法に関する記事の手順に従って、新しいインスタンスと必要な Azure AD アプリの登録を設定します。 説明には、各手順が正常に完了し、新しいインスタンスを使用する準備ができていることを確認するための手順も含まれています。

このチュートリアルでは、インスタンスを設定したときの以下の値が必要になります。 これらの値を再度収集する必要がある場合は、以下のリンクを使用して、[Azure portal](https://portal.azure.com) でそれらを見つけるためのセットアップの記事の対応するセクションを参照してください。
* Azure Digital Twins インスタンスの "**_ホスト名_**" ([ポータルで見つける](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD アプリ登録の "**_アプリケーション (クライアント) ID_**" ([ポータルで見つける](how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD アプリ登録の "**_ディレクトリ (テナント) ID_**" ([ポータルで見つける](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>アプリの登録のクライアント シークレットを取得する

Azure AD アプリの登録に対する "**_クライアント シークレット_**" も作成する必要があります。 これを行うには、Azure portal の [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ページに移動します (このリンクを使用しても、ポータルの検索バーで検索してもかまいません)。 一覧でお使いの登録を選択して、詳細を表示します。 

登録のメニューから *[証明書とシークレット]* を選択して、 *[+ 新しいクライアント シークレット]* を選択します。

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Azure AD アプリの登録のポータル表示。リソース メニューの [証明書とシークレット] とページの [新しいクライアント シークレット] が強調して示されている":::

[説明] と [有効期限] に適切な値を入力して、 *[追加]* クリックします。
*[証明書とシークレット]* ページのクライアント シークレットの一覧にシークレットが追加されます。 後で使用するので値を記録しておきます ([コピー] アイコンを使用してクリップボードにコピーすることもできます)。

### <a name="add-a-digital-twin"></a>デジタル ツインを追加する

この記事では、Logic Apps を使用して、Azure Digital Twins インスタンス内のツインを更新します。 続行するには、インスタンスにツインを少なくとも 1 つ追加する必要があります。 

[DigitalTwins API](how-to-use-apis-sdks.md)、[NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用してツインを追加できます。 これらの方法を使用してツインを作成する方法の詳細な手順については、"[ *「デジタル ツインを管理する」方法*](how-to-manage-twin.md)を参照してください。

作成したインスタンス内のツインの "**_ツイン ID_**" が必要になります。

## <a name="create-custom-logic-apps-connector"></a>カスタム Logic Apps コネクタを作成する

このステップでは、Azure Digital Twins API 用の[カスタム Logic Apps コネクタ](../logic-apps/custom-connector-overview.md)を作成します。 これを行った後は、次のセクションでロジック アプリを作成するときに、Azure Digital Twins を接続できるようになります。

Azure portal の [[Logic Apps カスタム コネクタ]](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) ページに移動します (このリンクを使用しても、ポータルの検索バーで検索してもかまいません)。 *[+ 追加]* をクリックします。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure portal の [Logic Apps カスタム コネクタ] ページ。[追加] ボタンが強調して示されている":::

次の *[Logic Apps カスタム コネクタの作成]* ページで、お使いのサブスクリプション、リソース グループ、新しいコネクタの名前とデプロイの場所を選択します。 *[確認および作成]* をクリックします。 これにより表示される *[確認および作成]* タブによって、下部にある *[作成]* をクリックしてリソースを作成できます。

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure portal の [Logic Apps カスタム コネクタの作成] ページの [確認および作成] タブ。[作成] ボタンが強調して示されている":::

コネクタのデプロイ ページが表示されます。 デプロイが完了したら、 *[リソースに移動]* ボタンをクリックして、ポータルでコネクタの詳細を表示します。

### <a name="configure-connector-for-azure-digital-twins"></a>Azure Digital Twins 用にコネクタを構成する

次に、作成したコネクタを Azure Digital Twins に接続するように構成します。

まず、Logic Apps で動作するように変更されたカスタム Azure Digital Twins Swagger をダウンロードします。 [こちらのリンク](https://github.com/Azure-Samples/digital-twins-custom-swaggers/blob/main/LogicApps/preview/2020-05-31-preview/digitaltwins.json)から *digitaltwins.json* をダウンロードします。

次に、Azure portal のコネクタの [概要] ページで、 *[編集]* クリックします。

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="前のステップで作成したコネクタの [概要] ページ。[編集] ボタンが強調して示されている":::

次の *[Logic Apps カスタム コネクタの編集]* ページで、次の情報を構成します。
* **カスタム コネクタ**
    - API エンドポイント: REST (既定値のまま)
    - インポート モード: OpenAPI ファイル (既定値のまま)
    - ファイル: これは、前にダウンロードしたカスタム Swagger ファイルです。 *[インポート]* をクリックし、コンピューター上のファイルを見つけて、 *[開く]* をクリックします。
* **一般情報**
    - アイコン、アイコンの背景色、説明: 任意の値を入力します。
    - スキーム: HTTPS (既定値のまま)
    - [Host]\(ホスト\):Azure Digital Twins インスタンスの "*ホスト名*"。
    - ベース URL: / (既定値のまま)

次に、ウィンドウの下部にある *[セキュリティ]* ボタンをクリックして、次の構成ステップに進みます。

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="[Logic Apps カスタム コネクタの編集] ページの下部のスクリーンショット。セキュリティに進むためのボタンが強調して示されている":::

[セキュリティ] ステップで、 *[編集]* クリックして次の情報を構成します。
* **[認証の種類]** : OAuth 2.0
* **OAuth 2.0**:
    - ID プロバイダー: Azure Active Directory
    - クライアント ID: お使いの Azure AD アプリ登録の "*アプリケーション (クライアント) ID*"
    - クライアント シークレット:Azure AD アプリ登録の "[*前提条件*](#prerequisites)" で作成した "*クライアント シークレット*"
    - ログイン URL: https://login.windows.net (既定のまま)
    - テナント ID: Azure AD アプリ登録の "*ディレクトリ (テナント) ID*"
    - リソース URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - スコープ:Directory.AccessAsUser.All
    - リダイレクト URL: (現時点では既定のまま)

[リダイレクト URL] フィールドに *[カスタム コネクタを保存してリダイレクト URL を生成します]* と表示されていることにご注意ください。 ペインの上部にある *[コネクタの更新]* をクリックしてこれを行い、コネクタの設定を確認します。

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="[Logic Apps カスタム コネクタの編集] ページの上部のスクリーンショット。[コネクタの更新] ボタンの周囲が強調して示されている":::

<!-- Success message? didn't see one -->

[リダイレクト URL] フィールドに戻り、生成された値をコピーします。 次の手順で使用します。

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="[Logic Apps カスタム コネクタの編集] ページの [リダイレクト URL] フィールドの値が、https://logic-apis-westus2.consent.azure-apim.net/redirect になっている。値をコピーするボタンが強調して示されている。":::

コネクタを作成するために必要な情報は、これですべてです ([セキュリティ] に続けて [定義] ステップを行う必要はありません)。 *[Logic Apps カスタム コネクタの編集]* ペインを閉じてかまいません。

>[!NOTE]
>最初に *[編集]* をクリックしたコネクタの [概要] ページに戻ります。 *[編集]* を再びクリックすると、構成選択入力プロセス全体が再び始まるのでご注意ください。 前回の設定値は残っていないので、変更した値で更新した構成を保存したい場合は、既定値で上書きされないように、他の値もすべて再入力する必要があります。

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Azure AD アプリでコネクタのアクセス許可を付与する

次に、最後のステップでコピーしたカスタム コネクタの "*リダイレクト URL*" の値を使用して、Azure AD アプリの登録でコネクタのアクセス許可を付与します。

Azure portal の [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ページに移動し、一覧から登録を選択します。 

登録のメニューの *[認証]* で、URI を追加します。

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure portal でのアプリ登録の認証ページ。メニューの [認証] と、そのページの [URI の追加] ボタンが強調して示されている。"::: 

カスタム コネクタの "*リダイレクト URL*" を新しいフィールドに入力し、 *[保存]* アイコンをクリックします。

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure portal でのアプリ登録の認証ページ。新しいリダイレクト URL と、そのページの [保存] ボタンが強調して示されている。":::

これで、Azure Digital Twins API にアクセスできるカスタム コネクタの設定が完了しました。 

## <a name="create-logic-app"></a>ロジック アプリを作成する

次に、新しいコネクタを使用して Azure Digital Twins の更新を自動化するロジック アプリを作成します。

Azure portal で [[Logic Apps]](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Logic%2Fworkflows) ページに移動します (このリンクを使用しても、ポータルの検索バーで検索してもかまいません)。 *[ロジック アプリの作成]* をクリックします。

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure portal の [Logic Apps] ページ。[ロジック アプリの作成] ボタンの周囲が強調して示されている":::

次の *[ロジック アプリ]* ページで、お使いのサブスクリプション、リソース グループ、および新しいロジック アプリの名前とデプロイの場所を選択します。 *[確認および作成]* をクリックします。 これにより表示される *[確認および作成]* タブによって、下部にある *[作成]* をクリックしてリソースを作成できます。

ロジック アプリのデプロイ ページが表示されます。 デプロイが完了したら、 *[リソースに移動]* ボタンをクリックして *[Logic Apps デザイナー]* に進み、そこでワークフローのロジックを入力します。

### <a name="design-workflow"></a>ワークフローを設計する

*[Logic Apps デザイナー]* で、 *[一般的なトリガーで開始する]* の _**[繰り返し]**_ を選択します。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure portal の [Logic Apps デザイナー] ページ。[繰り返し] 一般トリガーが強調して示されている":::

次の *[Logic Apps デザイナー]* ページで、 **[繰り返し]** 頻度を *[秒]* に変更して、イベントが 3 秒ごとにトリガーされるようにします。 これにより、後で非常に長い時間待つことなく、簡単に結果を確認できるようになります。

*[+ 新しいステップ]* をクリックします。

これにより、 *[アクションの選択]* ボックスが開きます。 *[カスタム]* タブに切り替えます。上部のボックスに、前に作成したカスタム コネクタが表示されます。

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure portal の Logic Apps デザイナーでのフローの作成。[アクションの選択] ボックスの [カスタム] タブが選択されている。前に作成したユーザーのカスタム コネクタがボックスに表示され、その周囲が強調して示されている。":::

それを選択して、そのコネクタに含まれる API の一覧を表示します。 検索バーを使用するか、一覧をスクロールして、**DigitalTwins_Add** を選択します。 (この記事ではこの API が使用されていますが、他の API を Logic Apps の接続用に選択してもかまいません)。

コネクタに接続するために、Azure の資格情報でサインインするように求められる場合があります。 *[要求されているアクセス許可]* ダイアログが表示される場合は、画面の指示に従ってアプリに同意して受け入れます。

新しい *DigitalTwinsAdd* ボックスのフィールドを次のように設定します。
* id: ロジック アプリで更新するインスタンス内のデジタル ツインの "*ツイン ID*" を入力します。
* Item - 1: このフィールドには、選択した API 要求で必要な本文を入力します。 *DigitalTwinsUpdate* の場合、この本文は JSON Patch コードの形式です。 ツインを更新するための JSON Patch の構成の詳細については、「[デジタル ツインを更新する](how-to-manage-twin.md#update-a-digital-twin)」セクションを参照してください ("*方法: 「デジタル ツインを管理する」方法*を参照してください。
* api-version: 現在のパブリック プレビューでは、この値は *2020-05-31-preview* です。

Logic Apps デザイナーで *[保存]* をクリックします。

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="ロジック アプリ コネクタで完成したアプリの表示。DigitalTwinsAdd ボックスに、JSON Patch 本文のサンプルなど、上で説明した値が入力されている。ウィンドウの [保存] ボタンが強調して示されている。":::

## <a name="query-twin-to-see-the-update"></a>ツインのクエリを実行して更新を確認する

ロジック アプリを作成したので、Logic Apps デザイナーで定義したツイン更新イベントが、3 秒ごとに繰り返し発生するようになります。 つまり、3 秒後には、ツインに対してクエリを実行し、新しくパッチが適用された値が反映されていることを確認できるはずです。

任意の方法 ([カスタム クライアント アプリ](tutorial-command-line-app.md)、[Azure Digital Twins Explorer サンプル アプリ](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)、[SDK と API](how-to-use-apis-sdks.md)、[CLI](how-to-use-cli.md) など) を選択して、ツインのクエリを実行できます。 

Azure Digital Twins インスタンスのクエリの詳細については、[*ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、指定したパッチを使用して、Azure Digital Twins インスタンス内のツインを定期的に更新するロジック アプリを作成しました。 カスタム コネクタで他の API を選択して、インスタンスに対するさまざまなアクションのロジック アプリを作成することができます。

使用可能な API 操作とその詳細については、"[*Azure Digital Twins の API および SDK を使用する方法*](how-to-use-apis-sdks.md)" に関するページで参照してください。
