---
title: Power BI を使用して Azure Data Lake Storage Gen2 のデータを分析する | Microsoft Docs
description: Power BI を使用して Azure Data Lake Storage Gen2 に格納されているデータを分析する
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: d76ea317271ae0e8eb0d54fcfee5dc005d836fc1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984953"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Power BI を使用して Azure Data Lake Storage Gen2 のデータを分析する

この記事では、Power BI Desktop を使用して、階層型名前空間があるアカウント (Azure Data Lake Storage Gen2) に格納されているデータを分析して視覚化する方法を説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間があるストレージ アカウント。 作成するには、[こちら](data-lake-storage-quickstart-create-account.md)の手順に従います。
> この記事では、`myadlsg2` という名前のアカウントが作成されていることを前提としています。
> * お使いのストレージ アカウントに配置されている `Drivers.txt` という名前のサンプル データ ファイル。
> このサンプルを [Azure Data Lake Git リポジトリ](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)からダウンロードした後、お使いのストレージ アカウントにそのファイルをアップロードできます。
> * **Power BI Desktop**。 これは [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331) からダウンロードできます。 

## <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop でレポートを作成する

1. コンピューターで Power BI Desktop を起動します。
2. リボンの **[ホーム]** タブで、 **[データの取得]** をクリックしてから **[詳細]** をクリックします。
3. **[データの取得]** ダイアログ ボックスで、 **[Azure]** 、 **[Azure Data Lake Store Gen2 (ベータ)]** 、 **[接続]** の順にクリックします。

    ![[データの取得] ページ](media/data-lake-storage-use-power-bi/get-data-page.png)

4. **[Azure Data Lake Storage Gen2]** ダイアログ ボックスで、コンテナーのエンドポイント形式を使用して、ご利用の Azure Data Lake Storage Gen2 アカウント、ファイル システム、またはサブフォルダーの URL を指定できます。 Data Lake Storage Gen2 の URL のパターンは `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` です。その後、 **[OK]** をクリックします。

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. 次のダイアログ ボックスで、 **[サインイン]** をクリックして、お使いのストレージ アカウントにサインインします。 組織のサインイン ページにリダイレクトされます。 画面の指示に従ってアカウントにサインインします。

    ![サインイン ページ](media/data-lake-storage-use-power-bi/sign-in.png)

6. 正常にサインインしたら、 **[接続]** をクリックします。

    ![サインイン後のページ](media/data-lake-storage-use-power-bi/signed-in.png)

7. 次のダイアログ ボックスに、前の手順 4 で指定した URL のすべてのファイルが表示されます (お使いのストレージ アカウントにアップロードされたファイルも含まれます)。 情報を確認し、 **[読み込み]** をクリックします。

    ![ファイル システム](media/data-lake-storage-use-power-bi/file-systems.png)

8. データが Power BI に正常に読み込まれると、 **[フィールド]** タブに以下のフィールドが表示されます。

    ![[フィールド] タブ](media/data-lake-storage-use-power-bi/fields.png)

    ただし、データを視覚化して分析するために、以下のフィールドごとにデータを使用できるようにします。

    ![フィールド](media/data-lake-storage-use-power-bi/preferred-fields.png)

    次の手順では、必要な形式でインポートされたデータを変換するためにクエリを更新します。

9. リボンの **[ホーム]** タブで、 **[クエリの編集]** をクリックします。

    ![クエリ](media/data-lake-storage-use-power-bi/queries.png)

10. **クエリ エディター**で、 **[コンテンツ]** 列の下にある **[バイナリ]** をクリックします。 ファイルが自動的に CSV として検出され、出力が次のように表示されます。 これで、データは視覚エフェクトの作成に使用できる形式で利用できるようになりました。

    ![Output](media/data-lake-storage-use-power-bi/binary.png)

11. リボンの **[ホーム]** タブで、[**閉じて** **適用**]、[**閉じて** **適用**] の順にクリックします。

    ![閉じて適用する](media/data-lake-storage-use-power-bi/close-apply.png)

12. クエリが更新されたら、 **[フィールド]** タブに、視覚エフェクトで使用可能な新しいフィールドが表示されます。

    ![新しいフィールド](media/data-lake-storage-use-power-bi/new-fields.png)

13. 特定の国の各都市のドライバーを示す円グラフを作成してみましょう。 これを行うには、以下のように選択します。

    **[視覚化]** タブで、円グラフのシンボルをクリックします。

    ![視覚化](media/data-lake-storage-use-power-bi/visualizations.png)

    使用する列は Column 4 (都市名) と Column 7 (国名) です。 以下のように、これらの列を **[フィールド]** タブから **[視覚エフェクト]** タブにドラッグします。

    ![フィールドのドラッグ](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    これで、円グラフの外観は以下のようになります。

    ![円グラフ](media/data-lake-storage-use-power-bi/pie-chart.png)

14. これで、ページ レベル フィルターから特定の国を選択し、選択した国の各都市のドライバー数を確認できます。 たとえば、 **[視覚エフェクト]** タブの **[ページ レベル フィルター]** で、 **[ブラジル]** を選択します。

    ![ページ フィルター](media/data-lake-storage-use-power-bi/page-filters.png)

15. 円グラフが自動的に更新され、ブラジルの各都市のドライバーが表示されます。

    ![ブラジル](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. **[ファイル]** メニューの **[保存]** をクリックして、Power BI Desktop ファイルとして視覚エフェクトを保存します。

## <a name="publish-report-to-power-bi-service"></a>レポートを Power BI サービスに発行する

Power BI Desktop で視覚化を作成したら、それを Power BI サービスに発行することで、他のユーザーと共有することができます。 その手順については、「 [Power BI Desktop からの発行](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)」参照してください。
