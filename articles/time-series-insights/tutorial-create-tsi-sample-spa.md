---
title: チュートリアル:Azure Time Series Insights シングルページ Web アプリの作成 | Microsoft Docs
description: TSI 環境のデータを照会してレンダリングするシングルページ Web アプリケーションを作成する方法について説明します。
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: fe8b6113646589e30ff839c8bd47968138d98b03
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521435"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>チュートリアル: Azure Time Series Insights シングルページ Web アプリの作成

このチュートリアルでは、[Time Series Insights (TSI) サンプル アプリケーション](https://insights.timeseries.azure.com/clientsample)をモデルとして、TSI データにアクセスする独自のシングルページ Web アプリケーション (SPA) を作成するプロセスについて説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの設計
> * Azure Active Directory (AD) にアプリケーションを登録する方法
> * Web アプリケーションをビルド、発行、テストする方法 

## <a name="prerequisites"></a>前提条件

お持ちでない場合は、[無料の Azure サブスクリプション](https://azure.microsoft.com/free/)にサインアップしてください。 

また、まだインストールしていない場合は、Visual Studio もインストールする必要があります。 このチュートリアルでは、[無料の Community バージョンまたは無料試用版をダウンロード/インストール](https://www.visualstudio.com/downloads/)できます。

## <a name="application-design-overview"></a>アプリケーションの設計の概要

前述のとおり、TSI サンプル アプリケーションが、このチュートリアルで使用される設計とコードのベースになります。 コード内では、TSI JavaScript クライアント ライブラリが使用されます。 TSI クライアント ライブラリによって、2 つの主な API カテゴリの抽象化が提供されます。

- **TSI Query API を呼び出すためのラッパー メソッド**: JSON ベースの式を使用して TSI データを照会できるようにする REST API。 メソッドは、ライブラリの `TsiClient.server` 名前空間の下に整理されています。
- **いくつかの種類のグラフ コントロールを作成してデータを設定するためのメソッド**: TSI データを Web ページで視覚化するために使用されるメソッド。 メソッドは、ライブラリの `TsiClient.ux` 名前空間の下に整理されています。

このチュートリアルでは、サンプル アプリケーションの TSI 環境からのデータも使用します。 TSI サンプル アプリケーションの構造と TSI クライアント ライブラリの使用の詳細については、チュートリアル「[Azure Time Series Insights JavaScript クライアント ライブラリを調べる](tutorial-explore-js-client-lib.md)」を参照してください。

## <a name="register-the-application-with-azure-ad"></a>Azure AD へのアプリケーションの登録 

ビルドする前に、アプリケーションを Azure AD に登録する必要があります。 登録によってアプリケーションの ID 構成が行われ、そのアプリケーションでシングル サインオンの OAuth サポートを利用できるようになります。 OAuth を利用するには、SPA で "暗黙的な" 承認付与が使用される必要があります。この承認付与は、アプリケーション マニフェストで更新します。 アプリケーション マニフェストは、アプリケーションの ID 構成の JSON 表現です。 

1. Azure サブスクリプション アカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。  
1. 左側のウィンドウの **[Azure Active Directory]** リソース、**[アプリの登録]**、**+ [新しいアプリケーションの登録]** の順に選択します。  
   
   ![Azure portal - Azure AD アプリケーション登録](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. **[作成]** ページで、必須のパラメーターを入力します。
   
   パラメーター|説明
   ---|---
   **Name** | わかりやすい登録名を入力します。  
   **アプリケーションの種類** | SPA Web アプリケーションをビルドするため、[Web アプリ/API] のままにします。
   **サインオン URL** | アプリケーションのホーム/サインイン ページの URL を入力します。 アプリケーションは (後で) Azure App Service でホストされるため、"https:\//azurewebsites.net" ドメイン内の URL を使用する必要があります。 この例では、名前は登録名に基づきます。

   完了したら、**[作成]** をクリックして新しいアプリケーション登録を作成します。

   ![Azure portal - Azure AD アプリケーション登録 - 作成](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. リソース アプリケーションによって、他のアプリケーションで使用される REST API が提供されます。これらのリソース アプリケーションも Azure AD に登録されます。 API では、"スコープ" を公開することで、クライアント アプリケーションによるきめ細かい/安全なアクセスが実現します。 お客様のアプリケーションは "Azure Time Series Insights" API を呼び出します。そのため、実行時に要求/付与されるアクセス許可に関して、API とスコープを指定する必要があります。 **[設定]**、**[必要なアクセス許可]**、**+ [追加]** の順に選択します。

   ![Azure portal - Azure AD のアクセス許可の追加](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. **[API アクセスの追加]** ページで、TSI API を指定するために **1. の [API を選択します]** をクリックします。 **[API を選択します]** ページで検索フィールドに「azure time」と入力します。 次に、結果リストの "Azure Time Series Insights" API を選択して、**[選択]** をクリックします。 

   ![Azure portal - Azure AD のアクセス許可の追加 - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. 今度は API のスコープを指定します。 もう一度 **[API アクセスの追加]** ページで、**2. の [アクセス許可を選択します]** をクリックします。 **[アクセスの有効化]** ページで、[Access Azure Time Series Insights service]\(Azure Time Series Insights サービスへのアクセス\) スコープを選択します。 **[選択]** をクリックすると **[API アクセスの追加]** ページに戻るので、そこで **[完了]** をクリックします。

   ![Azure portal - Azure AD のアクセス許可の追加 - スコープ](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. **[必要なアクセス許可]** ページに戻ったら、"Azure Time Series Insights" API が表示されたことがわかります。 さらに、すべてのユーザーに関して、アプリケーションが API とスコープにアクセスするためのアクセス許可を事前承認する必要があります。 上部にある **[アクセス許可の付与]** ボタンをクリックして、**[はい]** を選択します。

   ![Azure portal - Azure AD の必要なアクセス許可 - 承認](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. 前述のとおり、アプリケーション マニフェストを更新することも必要です。 階層リンクのアプリケーション名をクリックして、**[登録済みのアプリ]** ページに戻ります。 **[マニフェスト]** を選択して `oauth2AllowImplicitFlow` プロパティを `true` に変更し、**[保存]** をクリックします。

   ![Azure portal - Azure AD でのマニフェストの更新](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. 最後に、階層リンクをクリックして再度 **[登録済みのアプリ]** ページに戻り、アプリケーションの**ホーム ページ** URL と**アプリケーション ID** のプロパティをコピーします。 これらのプロパティは後の手順で使用します。

   ![Azure portal - Azure AD のプロパティ](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>Web アプリケーションのビルドと発行

1. アプリケーションのプロジェクト ファイルを格納するディレクトリを作成します。 次の各 URL を表示し、ページの右上にある [Raw] リンクを右クリックして、プロジェクト ディレクトリに "名前を付けて保存" します。

   > [!NOTE]
   > ブラウザーによっては、ファイルを保存する前にファイル拡張子を (HTML または CSS に) 修正する必要がある場合があります。

   - **index.html**: ページの HTML と JavaScript https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** CSS スタイル シート: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. Web アプリケーション用のプロジェクトを作成するために、Visual Studio を起動してサインインします。 **[ファイル]** メニューで、**[Web** **サイトを開く]** オプションを選択します。 **[Web サイトを開く]** ダイアログで、HTML ファイルと CSS ファイルを格納した作業ディレクトリを選択し、**[開く]** をクリックします。

   ![VS - [ファイル] の [Web サイトを開く]](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. Visual Studio の **[表示]** メニューから**ソリューション エクスプローラー**を開きます。 Web サイト プロジェクト (地球アイコン) が含まれた新しいソリューションが表示されます。ここには HTML ファイルと CSS ファイルが含まれています。

   ![VS - ソリューション エクスプローラーの新しいソリューション](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. アプリケーションを発行する前に、**index.html** で JavaScript コードの一部を更新する必要があります。 

   a. 最初に、`<head>` 要素にある、JavaScript ファイルとスタイル シート ファイルの参照のパスを変更します。 Visual Studio ソリューションの **index.html** ファイルを開いて、JavaScript コードの次の行を見つけます。 "PROD RESOURCE LINKS" の下の 3 行をコメント解除して、"DEV RESOURCE LINKS" の下の 3 行をコメントアウトします。
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      変更したコードは次の例のようになります。

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. 次に、新しい Azure AD アプリケーション登録を使用するために、アクセス トークン ロジックを変更します。 「[Azure AD へのアプリケーションの登録](#register-the-application-with-azure-ad)」セクションの手順 9. でコピーしたアプリケーション ID とホーム ページ URL が使用されるよう、次の `clientID` 変数と `postLogoutRedirectUri` 変数をそれぞれ変更します。

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      変更したコードは次の例のようになります。

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. 編集が完了したら **index.html** を保存します。

1. 次に、Web アプリケーションを Azure アプリ サービスとして Azure サブスクリプションに発行します。  

   > [!NOTE]
   > 次のダイアログ ボックスにあるフィールドのいくつかには、お客様の Azure サブスクリプションからデータが入力されます。 そのため、続行できるようになる前に、各ダイアログの読み込みの完了に数秒かかる場合があります。  

   a. **ソリューション エクスプローラー**の Web サイト プロジェクト ノードを右クリックして、**[Web アプリの発行]** を選択します。  

      ![VS - ソリューション エクスプローラーの [Web アプリの発行]](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. **[Microsoft Azure App Service]** を選択して発行先を作成します。  

      ![VS - 発行プロファイル](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. アプリケーションの発行に使用したいサブスクリプションを選択して、[新規作成] をクリックします。 

      ![VS - 発行プロファイル - アプリ サービス](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. **[App Service の作成]** ダイアログのフィールドがすべて読み込まれるまで数秒待ってから、次のフィールドを変更します。
   
      フィールド | 説明
      ---|---
      **アプリ名** | 「[Azure AD へのアプリケーションの登録](#register-the-application-with-azure-ad)」の手順 3. で使用した Azure AD アプリケーション登録名に変更します。 
      **リソース グループ** | **[新規作成]** ボタンを使用して、**[アプリ名]** フィールドに一致するよう変更します。
      **App Service プラン** | **[新規作成]** ボタンを使用して、**[アプリ名]** フィールドに一致するよう変更します。

      完了したら、**[作成]** をクリックします。 Azure リソースが作成される間、左下隅にある **[エクスポート]** ボタンが数秒間、[配置しています] に変わります。 リソースが作成されると、前のダイアログに戻ります。 

      ![VS - 発行プロファイル - 新しいアプリ サービスの追加](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. **[発行]** ダイアログに戻ったら、**[発行方法]** を [Web 配置] に設定してください。 さらに、`http` ではなく `https` を使用して、Azure AD アプリケーション登録に一致するよう **[宛先 URL]** を変更します。 次に、[発行] をクリックして Web アプリケーションを配置します。  

      ![VS - Web アプリの発行 - アプリ サービスの発行](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. Visual Studio の **[出力]** ウィンドウに成功した発行のログが表示されます。 配置の完了後、さらに Visual Studio によってブラウザー タブで Web アプリケーションが開かれ、サインインを求められます。 サインインが成功すると、データが入力されたすべての TSI コントロールが表示されます。  

      [![VS - Web アプリの発行 - 発行のログ出力](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![TSI SPA アプリ - ログイン](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>トラブルシューティング  

エラー コード/状態 | 説明
---------------------| -----------
"*AADSTS50011: アプリケーションの応答アドレスが登録されていません。*" | Azure AD 登録に "応答 URL" プロパティがありません。 Azure AD アプリケーション登録の **[設定]** / **[応答 URL]** ページに移動します。 「[Azure AD へのアプリケーションの登録](#register-the-application-with-azure-ad)」の手順 3. で指定された**サインオン** URL が存在することを確認します。 
"*AADSTS50011: The reply url specified in the request does not match the reply urls configured for the application: '\<Application ID GUID>'. (要求で指定されている応答 URL が、アプリケーションに関して構成されている応答 URL と一致しません: "<アプリケーション ID GUID>")*" | 「[Web アプリケーションのビルドと発行](#build-and-publish-the-web-application)」の手順 4.b で指定された `postLogoutRedirectUri` が、Azure AD アプリケーション登録の**設定** / **応答 URL** のプロパティで指定されている値に一致する必要があります。 さらに、`https` を使用するよう **[宛先 URL]** も変更してください  (「[Web アプリケーションのビルドと発行](#build-and-publish-the-web-application)」の手順 5.e を参照)。
Web アプリケーションによって読み込みが行われるものの、背景が白く、スタイルが適用されていないテキストのみのサインイン ページが表示される。 | 「[Web アプリケーションのビルドと発行](#build-and-publish-the-web-application)」の手順 4. で説明されているパスが正しいことを確認します。 Web アプリケーションが .css ファイルを検出できない場合、スタイルがページに正しく適用されません。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成された Azure サービスがいくつか実行されています。 このチュートリアル シリーズを完了する予定がない場合、不要なコストが発生しないようにすべてのリソースを削除することをお勧めします。 

Azure Portal の左側のメニューで、次のように操作します。

1. **[リソース グループ]** アイコンをクリックし、TSI 環境に作成したリソース グループを選択します。 ページの上部で **[リソース グループの削除]** をクリックし、リソース グループの名前を入力してから **[削除]** をクリックします。 
1. **[リソース グループ]** アイコンをクリックし、デバイス シミュレーション ソリューション アクセラレータによって作成されたリソース グループを選択します。 ページの上部で **[リソース グループの削除]** をクリックし、リソース グループの名前を入力してから **[削除]** をクリックします。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * アプリケーションの設計
> * Azure Active Directory (AD) にアプリケーションを登録する方法
> * Web アプリケーションをビルド、発行、テストする方法 

このチュートリアルでは、アクセス トークンを取得するために、サインインしたユーザーの ID を使用して Azure AD との統合が行われます。 サービス/デーモン アプリケーションの ID を使用して TSI API にアクセスする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Time Series Insights API の認証と認可](time-series-insights-authentication-and-authorization.md)
