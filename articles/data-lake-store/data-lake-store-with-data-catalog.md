---
title: Azure Data Catalog に Data Lake Storage Gen1 を統合する
description: ご自分の組織でデータを検出できるように、Azure Data Catalog に Data Lake Storage Gen1 のデータを登録する方法を説明します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02544489816f5711ca6e599c2bce03737c747934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106622"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Azure Data Catalog に Azure Data Lake Storage Gen1 のデータを登録する
この記事では、Azure Data Lake Storage Gen1 と Azure Data Catalog を統合し、データを Data Catalog と統合することで組織内で検出できるようにする方法について説明します。 データのカタログ化の詳細については、「 [Azure Data Catalog](../data-catalog/overview.md)」を参照してください。 Data Catalog を使用できるシナリオを理解するには、「 [Azure Data Catalog の一般的なシナリオ](../data-catalog/data-catalog-common-scenarios.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* Data Lake Storage Gen1 の **有効な Azure サブスクリプション**。 [手順](data-lake-store-get-started-portal.md)を参照してください。
* **Data Lake Storage Gen1 アカウント**。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。 このチュートリアルでは、**datacatalogstore** という Data Lake Storage Gen1 アカウントを作成してください。

    アカウントを作成したら、サンプル データ セットをアップロードします。 このチュートリアルでは、 **Azure Data Lake Git リポジトリ** の [AmbulanceData](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/)フォルダーにあるすべての .csv ファイルをアップロードします。 [Azure Storage Explorer](https://storageexplorer.com/)などのさまざまなクライアントを使用して、BLOB コンテナーにデータをアップロードすることができます。
* **Azure Data Catalog**。 組織で Azure Data Catalog が既に作成されている必要があります。 組織ごとに使用できるカタログは 1 つのみです。

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Data Catalog のソースとして Data Lake Storage Gen1 を登録する

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. `https://azure.microsoft.com/services/data-catalog`にアクセスし、 **[はじめに]** をクリックします。
1. Azure Data Catalog ポータルにログインし、 **[データの発行]** をクリックします。

    ![データ ソースの登録](./media/data-lake-store-with-data-catalog/register-data-source.png "データ ソースの登録")
1. 次のページで、 **[アプリケーションの起動]** をクリックします。 これで、コンピューター上にアプリケーション マニフェスト ファイルがダウンロードされます。 アプリケーションを起動するには、このマニフェスト ファイルをダブルクリックします。
1. [ようこそ] ページで、 **[サインイン]** をクリックし、資格情報を入力します。

    ![[ようこそ] 画面](./media/data-lake-store-with-data-catalog/welcome.screen.png "[ようこそ] 画面")
1. [データ ソースの選択] ページで、 **[Azure Data Lake Store]** を選択してから **[次へ]** をクリックします。

    ![データ ソースの選択](./media/data-lake-store-with-data-catalog/select-source.png "データ ソースの選択")
1. 次のページで、Data Catalog に登録する Data Lake Storage Gen1 アカウントの名前を指定します。 その他のオプションは既定値のままにし、 **[接続]** をクリックします。

    ![データ ソースへの接続](./media/data-lake-store-with-data-catalog/connect-to-source.png "データ ソースへの接続")
1. 次のページは以下のセグメントに分けることができます。

    a. **[サーバー階層]** ボックスには、Data Lake Storage Gen1 アカウントのフォルダー構造が示されます。 **$Root** は Data Lake Storage Gen1 アカウントのルートを表し、**AmbulanceData** は Data Lake Storage Gen1 アカウントのルートに作成されているフォルダーを表します。

    b. **[使用可能なオブジェクト]** ボックスには、**AmbulanceData** フォルダーにあるファイルとフォルダーがリストされます。

    c. **[登録されるオブジェクト]** ボックスには、Azure Data Catalog に登録するファイルとフォルダーがリストされます。

    ![[Microsoft Azure Data Catalog] の [ストアのアカウント] ダイアログボックスのスクリーンショット。](./media/data-lake-store-with-data-catalog/view-data-structure.png "データ構造の表示")
1. このチュートリアルでは、ディレクトリ内のすべてのファイルを登録する必要があります。 そのため、![オブジェクトの移動](./media/data-lake-store-with-data-catalog/move-objects.png "オブジェクトの移動")ボタンをクリックして、すべてのファイルを **[登録するオブジェクト]** ボックスに移動します。

    データは組織全体のデータ カタログに登録されるため、後でデータをすばやく見つけるために使用できるメタデータをいくつか追加することをお勧めします。 たとえば、データの所有者 (データをアップロードするユーザー) の電子メール アドレスを追加したり、データを識別するタグを追加したりすることができます。 以下の画面キャプチャには、データに追加するタグが示されています。

    ![[Microsoft Azure Data Catalog] の [ストアのアカウント] ダイアログボックスのスクリーンショット。呼び出されているデータにタグが追加されています。](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "データ構造の表示")

    **[登録]** をクリックします。
1. 次のキャプチャ画面は、データが Data Catalog に正常に登録されたことを示しています。

    ![登録の完了](./media/data-lake-store-with-data-catalog/registration-complete.png "データ構造の表示")
1. **[ポータルの表示]** をクリックして Data Catalog ポータルに戻り、ポータルから登録されたデータにアクセスできるようになったことを確認します。 データを検索する場合は、データの登録時に使用したタグを使用できます。

     ![カタログ内のデータの検索](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "カタログ内のデータの検索")
1. これで、データへの注釈やドキュメントの追加などの操作を実行できるようになりました。 詳細については、次のリンクを参照してください。

    * [データ ソースに注釈を付ける方法](../data-catalog/data-catalog-how-to-annotate.md)
    * [データ ソースの文書化](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>関連項目
* [データ ソースに注釈を付ける方法](../data-catalog/data-catalog-how-to-annotate.md)
* [データ ソースの文書化](../data-catalog/data-catalog-how-to-documentation.md)
* [Data Lake Storage Gen1 とその他の Azure サービスの統合](data-lake-store-integrate-with-other-services.md)