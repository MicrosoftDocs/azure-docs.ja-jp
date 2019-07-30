---
title: インクルード ファイル
description: インクルード ファイル
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440210"
---
1. 次のプラットフォームのためのクライアント SDK クイック スタートをダウンロードします。
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > iOS プロジェクトを使用する場合は、[GitHub の最新リリース](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest)から "azuresdk-iOS-\*.zip" をダウンロードする必要があります。 `MicrosoftAzureMobile.framework` ファイルを解凍して、プロジェクトのルートに追加します。
    >

2. データベース接続を追加するか、または既存の接続に接続する必要があります。 まず、データ ストアを作成するか、または既存のデータ ストアを使用するかを決定します。

    - **新しいデータ ストアを作成する**:データ ストアを作成する場合は、次のクイック スタートを使用します。

        [クイック スタート:Azure SQL Database の単一データベースの概要](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **既存のデータ ソース**:既存のデータベース接続を使用する場合は、次の手順に従います。

        1. SQL Database の接続文字列の形式 - `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** サーバーの名前。これは、データベースの概要ページで見つけることができ、通常は "server_name.database.windows.net" の形式になっています。
            **{ポート}** 通常は 1433 です。
            **{your_catalogue}** データベースの名前。
            **{your_username}** データベースにアクセスするユーザー名。
            **{your_password}** データベースにアクセスするためのパスワード。

            [SQL 接続文字列の形式](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)の詳細を確認してください。

        2. **モバイル アプリ**に接続文字列を追加します。App Service では、メニューの **[構成]** オプションを使用して、アプリケーションの接続文字列を管理できます。

            接続文字列を追加するには:

            1. **[アプリケーションの設定]** タブをクリックします。

            2. **[[+] New connection string] ([+] 新しい接続文字列)** をクリックします。

            3. 接続文字列の **[名前]** 、 **[値]** 、および **[種類]** を指定する必要があります。

            4. **[名前]** を「`MS_TableConnectionString`」と入力します。

            5. [値] は、前の手順で作成した接続文字列にしてください。

            6. SQL Azure データベースに接続文字列を追加している場合は、 **[種類]** の下の **[SQLAzure]** を選択します。

3. Azure Mobile Apps には、.NET バックエンド用と Node.js バックエンド用の SDK があります。

   - **Node.JS バックエンド**
    
     Node.js クイックスタート アプリを使用する場合は、以下の手順に従ってください。

     1. Azure portal で、 **[Easy Tables]** に移動します。次の画面が表示されます。
      
        ![Node Easy Tables](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. SQL 接続文字列が **[構成]** タブに既に追加されていることを確認します。次に、 **[これにより、すべてのサイト コンテンツが上書きされることを確認しました]** のボックスをオンにし、 **[TodoItem テーブルを作成する]** をクリックします。
     
        ![Node Easy Tables の構成](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. **[Easy Tables]** で、 **[+ 追加]** をクリックします。
    
        ![Node Easy Tables の追加ボタン](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. 匿名アクセス権で `TodoItem` テーブルを作成します。
      
        ![Node Easy Tables のテーブルの追加](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET バックエンド**
    
        .NET クイックスタート アプリを使用する場合は、次の手順に従います。

        1. [azure-mobile-apps-quickstarts リポジトリ](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)から Azure Mobile Apps .NET サーバー プロジェクトをダウンロードします。

        2. Visual Studio でローカルに .NET サーバー プロジェクトをビルドします。

        3. Visual Studio でソリューション エクスプローラーを開き、`ZUMOAPPNAMEService` プロジェクトを右クリックして、 **[発行]** をクリックすると、`Publish to App Service` ウィンドウが表示されます。 Mac で作業している場合は、[こちら](https://docs.microsoft.com/azure/app-service/deploy-local-git)から、アプリをデプロイする他の方法を確認してください。
        
           ![Visual Studio の発行](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. 発行先として **[App Service]** を選択し、 **[既存のものを選択]** をクリックし、次にウィンドウ下部にある **[発行]** をクリックします。

        5. 最初に、Azure サブスクリプションを使用して Visual Studio にログインする必要があります。 `Subscription`、`Resource Group` を選択し、次にアプリの名前を選択します。 準備ができたら **[OK]** をクリックします。これにより、ローカル環境にある .NET サーバー プロジェクトが App Service バックエンドにデプロイされます。 デプロイが完了すると、ブラウザーで `http://{zumoappname}.azurewebsites.net/` にリダイレクトされます。                   