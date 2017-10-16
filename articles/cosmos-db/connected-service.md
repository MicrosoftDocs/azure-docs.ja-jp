---
title: "Azure Cosmos DB のための Visual Studio 接続済みサービス"
description: "Visual Studio 接続済みサービスを通して、開発者は、簡単に Azure Cosmos DB アカウントに接続し、リソースを管理できます"
services: cosmos-db
documentationcenter: 
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: de0c83e4c99894f98de18eb089ce11cdf5c70f2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: Visual Studio 接続済みサービス (プレビュー段階)

Visual Studio 接続済みサービスを使用すると、開発者は、簡単に Azure Cosmos DB アカウントに接続し、リソースを管理できます。

さらに、接続済みサービスのデータ エクスプローラーを使用すれば、ストアド プロシージャ、UDF、トリガーを作成し、サーバー側のビジネス ロジックを実行することもできます。 データ エクスプローラーでは、API で使用可能な、組み込みのプログラムによるデータ アクセスがすべて公開されており、データに簡単にアクセスできます。

## <a name="prerequisites"></a>前提条件

以下のものがそろっていることを確認してください。

* アクティブな Azure アカウント。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 
* Azure Cosmos DB アカウント。 まだ実行していない場合は、[Cosmos DB の Azure アカウントを作成する](create-documentdb-dotnet.md)手順を実行して、Azure ポータルで作成するか、または [Connected Service ツール Azure Cosmos DB アカウントを作成する](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool) を参照してください。 
* 開発にローカル環境を使用する場合は、[Azure Cosmos DB エミュレーター](local-emulator.md) を使用できます。 この環境では、Azure Cosmos DB サービスをエミュレートします。
* [Visual Studio](http://www.visualstudio.com/)。
* 最新の Azure Cosmos DB 接続済みサービスのビットです。 次のスクリーン ショットは、Visual Studio Marketplace から Azure Cosmos DB 接続済みサービスをダウンロードする方法を示しています。 コンピューターで **Visual Studio** を開きます。 **[ツール]** メニューの **[拡張機能と更新]** を選択し、**[オンライン]**、 / **[Visual Studio Marketplace]** を選択します。 「**cosmosdb**」と入力してビットを検索します。

    Azure Cosmos DB 接続済みサービスは、[Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709) からもインストールできます。

    ![Connected Service download bits.png のスクリーン ショット](./media/connected-service/connected-service-downloadbits.png) 

## <a id="SetupVS"></a>Visual Studio ソリューションをセットアップする
1. コンピューターで **Visual Studio** を開きます。
2. **[ファイル]** メニューで、**[新規]**、**[プロジェクト]** の順に選択します。
3. **[新しいプロジェクト]** ダイアログで、**[Visual C#]** / **[コンソール アプリ (.NET Framework)]** または **[WPF アプリ (.NET Framework)]** の順に選択し、プロジェクトの名前を指定して、**[OK]** をクリックします。

    ![[新しいプロジェクト] ウィンドウのスクリーン ショット](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>接続済みサービスを追加し、アカウントを追加する
1. ソリューション エクスプローラーで、プロジェクト ノードを右クリックし、**[追加]** / **[接続済みサービス]** をクリックします。 または、**[プロジェクト]**をクリックして、**[接続済みサービスの追加]** を選択します。

    ![[接続済みサービスの追加] ウィンドウのスクリーン ショット](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. [接続済みサービス] ページで、**[接続済みサービス]** / **[Azure Cosmos DB]** をクリックして、**[Azure Cosmos DB]** ページを開きます。

    ![Azure Cosmos DB ウィンドウのスクリーン ショット](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. 初めての場合は、下矢印をクリックしてサインインするか、またはアカウントを追加します。 サインインすると、すべての Azure Cosmos DB のアカウントが空白の領域に表示されます。 プロジェクトに追加する Azure Cosmos DB アカウントを 1 つ選択します。

    ![サインインと表示されている DB アカウント一覧ウィンドウのスクリーン ショット](./media/connected-service/connected-service-add-db-account.png)
4. Azure Cosmos DB アカウントを追加すると、Azure Cosmos DB アカウントの接続済みサービス フォルダーがプロジェクトに追加されます。 複数の Azure Cosmos DB アカウントを追加するには、手順 1 ～ 3 を繰り返します。

    ![接続済みサービス フォルダー ウィンドウのスクリーン ショット](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Azure Cosmos DB 接続済みサービスを追加したら、次のいずれかの方法で Azure Cosmos DB にアクセスできるようプロジェクトを変更します。

    * Azure Cosmos DB クライアントに必要な nuget パッケージがインストールされています。 これは、パッケージの構成ファイルから確認できます。 

        ![新しい Azure Cosmos DB パッケージ構成](./media/connected-service/connected-service-packages-config.png)   
    
    * Azure Cosmos DB の接続 uri とキーがプロジェクト構成ファイルに追加されます。ここでは App.config です。 

        ![新しい Azure Cosmos DB アプリ構成](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Azure Cosmos DB エクスプローラーを開く
1. プロジェクト ノードを右クリックして **[Open Cosmos DB Explorer]**\(Cosmos DB エクスプローラーを開く\) を選択します。

    ![[データ エクスプローラーを開く] ウィンドウのスクリーン ショット](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. **[Choose a Cosmos DB Account]**\(Cosmos DB アカウントの選択\) ページでドロップダウン リストをクリックし、Azure Cosmos DB アカウントを 1 つ選択します。

    ![追加された接続済みサービス アカウント ウィンドウのスクリーン ショット](./media/connected-service/connected-service-open-explorer.png)
3. **[開く]** をクリックすると、データ エクスプローラー ウィンドウが表示されます。

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>接続済みサービス ツールで Azure Cosmos DB アカウントを作成する
1. [接続済みサービス] ページのウィンドウの左下にある **[Create a New Cosmos DB Account]**\(Cosmos DB アカウントの新規作成\) をクリックして、**[Cosmos DB アカウントの作成]** ページを開きます。

    ![[Azure Cosmos DB アカウントの作成] のエントリ ポイント ウィンドウのスクリーン ショット](./media/connected-service/connected-service-click-new-db-account.png)
2. **[Cosmos DB アカウントの作成]** ページで、この Azure Cosmos DB アカウントに必要な構成を指定します。

    次のスクリーンショットの情報を参考にしながら **[Cosmos DB アカウントの作成]** ページのフィールドに入力します。 
 
    ![新しい Azure Cosmos DB ページ](./media/connected-service/connected-service-create-new-account.png)        
3. **[作成]** をクリックしてアカウントを作成します。

## <a name="use-data-explorer"></a>データ エクスプローラーの使用

データ エクスプローラーを開いたら、次の操作を行うことができます。
* データベースを作成および削除する
* コレクションを作成および削除する
* ドキュメントを作成、削除、およびフィルター処理する
* ストアド プロシージャを作成および削除する
* トリガーおよびユーザー定義関数を作成および削除して、サーバー側のビジネス ロジックを実行する 

![新しい Azure Cosmos DB ページ](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>デモ

Visual Studio の接続済みサービスの使用方法を説明した次の動画をご覧ください: [Visual Studio で Azure Cosmos DB 接続済みサービスを使用する ](https://go.microsoft.com/fwlink/?linkid=858711)

## <a name="next-steps"></a>次のステップ
このドキュメントでは、次の項目について学びました。

> [!div class="checklist"]
> * Azure Cosmos DB アカウントを作成する
> * 接続済みサービスを追加し、アカウントを追加する
> * Azure Cosmos DB エクスプローラーを開く
> * データ エクスプローラーの使用

これで、Azure Cosmos DB アカウントを使用して、接続済みサービス を起動し、実行できました。次のチュートリアルのいずれかに進んで、ソリューションの開発を始めてください。

* [NET での DocumentDB API を使用した開発](tutorial-develop-documentdb-dotnet.md)
* [Azure Cosmos DB: DocumentDB API の使用に関するチュートリアル](documentdb-get-started.md)
* Azure Cosmos DB のスケールとパフォーマンスをテストする場合には、 「[Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)」を参照してください。
* [Azure Cosmos DB アカウントを監視する](monitor-accounts.md)方法を学習します。

