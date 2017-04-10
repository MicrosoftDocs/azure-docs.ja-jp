---
title: "Azure Data Catalog に Data Lake Store のデータを登録する | Microsoft Docs"
description: "Azure Data Catalog に Data Lake Store のデータを登録する"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 59f072c7a8272fc04e1d662c0ab17e7ee4500fa6
ms.openlocfilehash: b589742e23ba9cca919a845e4d262ccfea4c9aca


---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Azure Data Catalog に Data Lake Store のデータを登録する
この記事では、Azure Data Lake Store と Azure Data Catalog を統合し、データを Data Catalog と統合することで組織内で検出できるようにする方法について説明します。 データのカタログ化の詳細については、「 [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md)」を参照してください。 Data Catalog を使用できるシナリオを理解するには、「 [Azure Data Catalog の一般的なシナリオ](../data-catalog/data-catalog-common-scenarios.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure サブスクリプションを有効にする** 。 [手順](data-lake-store-get-started-portal.md)を参照してください。
* **Azure Data Lake Store アカウント**。 「 [Azure ポータルで Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。 このチュートリアルでは、 **datacatalogstore**という Data Lake Store アカウントを作成します。

    アカウントを作成したら、サンプル データ セットをアップロードします。 このチュートリアルでは、 **Azure Data Lake Git リポジトリ** の [AmbulanceData](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/)フォルダーにあるすべての .csv ファイルをアップロードします。 [Azure Storage Explorer](http://storageexplorer.com/)などのさまざまなクライアントを使用して、BLOB コンテナーにデータをアップロードすることができます。
* **Azure Data Catalog**。 組織で Azure Data Catalog が既に作成されている必要があります。 組織ごとに使用できるカタログは&1; つのみです。

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Data Catalog のソースとして Data Lake Store を登録する

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. `https://azure.microsoft.com/services/data-catalog`にアクセスし、 **[はじめに]**をクリックします。
2. Azure Data Catalog ポータルにログインし、 **[データの発行]**をクリックします。

    ![データ ソースの登録](./media/data-lake-store-with-data-catalog/register-data-source.png "データ ソースの登録")
3. 次のページで、 **[アプリケーションの起動]**をクリックします。 これで、コンピューター上にアプリケーション マニフェスト ファイルがダウンロードされます。 アプリケーションを起動するには、このマニフェスト ファイルをダブルクリックします。
4. [ようこそ] ページで、 **[サインイン]**をクリックし、資格情報を入力します。

    ![[ようこそ] 画面](./media/data-lake-store-with-data-catalog/welcome.screen.png "[ようこそ] 画面")
5. [データ ソースの選択] ページで、**[Azure Data Lake]** を選択してから **[次へ]** をクリックします。

    ![データ ソースの選択](./media/data-lake-store-with-data-catalog/select-source.png "データ ソースの選択")
6. 次のページで、Data Catalog に登録する Data Lake Store アカウントの名前を指定します。 その他のオプションは既定値のままにし、 **[接続]**をクリックします。

    ![データ ソースへの接続](./media/data-lake-store-with-data-catalog/connect-to-source.png "データ ソースへの接続")
7. 次のページは以下のセグメントに分けることができます。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サーバー階層]** ボックスには、Data Lake Store アカウントのフォルダー構造が示されます。 **$Root** は Data Lake Store アカウントのルートを表し、**AmbulanceData** は Data Lake Store アカウントのルートに作成されているフォルダーを表します。

    b. **[使用可能なオブジェクト]** ボックスには、**AmbulanceData** フォルダーにあるファイルとフォルダーがリストされます。

    c. **[登録されるオブジェクト]** ボックスには、Azure Data Catalog に登録するファイルとフォルダーがリストされます。

    ![データ構造の表示](./media/data-lake-store-with-data-catalog/view-data-structure.png "データ構造の表示")
8. このチュートリアルでは、ディレクトリ内のすべてのファイルを登録する必要があります。 そのため、![オブジェクトの移動ボタン](./media/data-lake-store-with-data-catalog/move-objects.png "オブジェクトの移動")をクリックして、すべてのファイルを **[登録するオブジェクト]** ボックスに移動します。

    データは組織全体のデータ カタログに登録されるため、後でデータをすばやく見つけられるようにするために使用できるメタデータをいくつか追加することをお勧めします。 たとえば、データの所有者 (データをアップロードするユーザー) の電子メール アドレスを追加したり、データを識別するタグを追加したりすることができます。 以下の画面キャプチャには、データに追加するタグが示されています。

    ![データ構造の表示](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "データ構造の表示")

    **[登録]**をクリックします。
9. 次のキャプチャ画面は、データが Data Catalog に正常に登録されたことを示しています。

    ![登録完了](./media/data-lake-store-with-data-catalog/registration-complete.png "データ構造の表示")
10. **[ポータルの表示]** をクリックして Data Catalog ポータルに戻り、ポータルから登録されたデータにアクセスできるようになったことを確認します。 データを検索する場合は、データの登録時に使用したタグを使用できます。

     ![カタログ内のデータの検索](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "カタログ内のデータの検索")
11. これで、データへの注釈やドキュメントの追加などの操作を実行できるようになりました。 詳細については、次のリンクを参照してください。

    * [データ ソースに注釈を付ける方法](../data-catalog/data-catalog-how-to-annotate.md)
    * [データ ソースの文書化](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>関連項目
* [データ ソースに注釈を付ける方法](../data-catalog/data-catalog-how-to-annotate.md)
* [データ ソースの文書化](../data-catalog/data-catalog-how-to-documentation.md)
* [Data Lake Store と他の Azure サービスを統合する](data-lake-store-integrate-with-other-services.md)



<!--HONumber=Feb17_HO1-->


