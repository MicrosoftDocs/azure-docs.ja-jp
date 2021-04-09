---
title: Azure Data Lake Storage Gen1 のデータを分析する - Power BI
description: Power BI Desktop を使用して Azure Data Lake Storage Gen1 に格納されているデータを分析し、視覚化する方法を説明します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91574196"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Power BI を使用して Azure Data Lake Storage Gen1 のデータを分析する
この記事では、Power BI Desktop を使用して Azure Data Lake Storage Gen1 に格納されているデータを分析し、視覚化する方法を説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Data Lake Storage Gen1 アカウント**。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。 この記事では、既に **myadlsg1** という Data Lake Storage Gen1 アカウントを作成しており、サンプル データ ファイル (**Drivers.txt**) をそのアカウントにアップロードしていることを前提としています。 このサンプル ファイルは、 [Azure Data Lake Git リポジトリ](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)からダウンロードできます。
* **Power BI Desktop**。 これは [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331) からダウンロードできます。 

## <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop でレポートを作成する
1. コンピューターで Power BI Desktop を起動します。
2. **[ホーム]** リボンの **[データの取得]** をクリックしてから [詳細] をクリックします。 **[データの取得]** ダイアログ ボックスで、 **[Azure]** 、 **[Azure Data Lake Store]** 、 **[接続]** の順にクリックします。
   
    ![[データの取得] ダイアログ ボックスのスクリーンショット。[Azure Data Lake Store] オプションが強調表示されて [接続] オプションが選択されています。](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Data Lake Storage Gen1 への接続")
3. 開発段階にあるコネクタに関するダイアログ ボックスが表示された場合は、続行することを選択します。
4. **[Azure Data Lake Store]** ダイアログ ボックスで、Data Lake Storage Gen1 アカウントの URL を指定してから **[OK]** をクリックします。
   
    ![Data Lake Storage Gen1 の URL](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Data Lake Storage Gen1 の URL")
5. 次のダイアログ ボックスで、 **[サインイン]** をクリックして Data Lake Storage Gen1 アカウントにサインインします。 組織のサインイン ページにリダイレクトされます。 画面の指示に従ってアカウントにサインインします。
   
    ![Data Lake Storage Gen1 へのサインイン](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Data Lake Storage Gen1 へのサインイン")
6. 正常にサインインしたら、 **[接続]** をクリックします。
   
    ![[Azure Data Lake Store] ダイアログ ボックスのスクリーンショット。[接続] オプションが選択されています。](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Data Lake Storage Gen1 への接続")
7. 次のダイアログ ボックスに、Data Lake Storage Gen1 アカウントにアップロードしたファイルが表示されます。 情報を確認してから **[ロード]** をクリックします。
   
    ![Data Lake Storage Gen1 からデータをダウンロードする](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Data Lake Storage Gen1 からデータをダウンロードする")
8. データが Power BI に正常に読み込まれたら、 **[フィールド]** タブに以下のフィールドが表示されます。
   
    ![インポートされたフィールド](./media/data-lake-store-power-bi/imported-fields.png "インポートされたフィールド")
   
    ただし、ここではデータを視覚化して分析するために、以下のフィールドごとにデータを使用可能にします。
   
    ![必要なフィールド](./media/data-lake-store-power-bi/desired-fields.png "必要なフィールド")
   
    次の手順では、必要な形式でインポートされたデータを変換するためにクエリを更新します。
9. **[ホーム]** リボンの **[クエリの編集]** をクリックします。
   
    ![[ホーム] リボンのスクリーンショット。[クエリの編集] オプションが選択されています。](./media/data-lake-store-power-bi/edit-queries.png "[クエリの編集]")
10. クエリ エディターで、 **[コンテンツ]** 列の下にある **[バイナリ]** をクリックします。
    
    ![クエリ エディターのスクリーンショット。[コンテンツ] 列が選択されています。](./media/data-lake-store-power-bi/convert-query1.png "[クエリの編集]")
11. アップロードした **Drivers.txt** ファイルを表すファイル アイコンが表示されます。 このファイルを右クリックしてから **[CSV]** をクリックします。    
    
    ![クエリ エディターのスクリーンショット。[CSV] オプションが選択されています。](./media/data-lake-store-power-bi/convert-query2.png "[クエリの編集]")
12. 出力は次のようになります。 これで、データは視覚エフェクトの作成に使用できる形式で利用できるようになりました。
    
    ![クエリ エディターのスクリーンショット。想定どおりの出力が表示されています。](./media/data-lake-store-power-bi/convert-query3.png "[クエリの編集]")
13. **[ホーム]** リボンで、 **[閉じて適用]** 、 **[閉じて適用]** の順にクリックします。
    
    ![[ホーム] リボンのスクリーンショット。[閉じて適用] オプションが選択されています。](./media/data-lake-store-power-bi/load-edited-query.png "[クエリの編集]")
14. クエリが更新されたら、 **[フィールド]** タブに、視覚エフェクトで使用可能な新しいフィールドが表示されます。
    
    ![更新されたフィールド](./media/data-lake-store-power-bi/updated-query-fields.png "更新されたフィールド")
15. 特定の国または地域の各都市のドライバーを示す円グラフを作成してみましょう。 これを行うには、以下のように選択します。
    
    1. [視覚エフェクト] タブで、円グラフのシンボルをクリックします。
       
        ![円グラフを作成する](./media/data-lake-store-power-bi/create-pie-chart.png "円グラフを作成する")
    2. 使用する列は **Column 4** (都市名) と **Column 7** (国または地域名) です。 以下のように、これらの列を **[フィールド]** タブから **[視覚エフェクト]** タブにドラッグします。
       
        ![視覚化の作成](./media/data-lake-store-power-bi/create-visualizations.png "グラフを作成します")
    3. これで、円グラフの外観は以下のようになります。
       
        ![円グラフ](./media/data-lake-store-power-bi/pie-chart.png "グラフを作成します")
16. これで、ページ レベル フィルターから特定の国または地域を選択し、選択した国または地域の各都市のドライバー数を確認できます。 たとえば、 **[視覚エフェクト]** タブの **[ページ レベル フィルター]** で、 **[ブラジル]** を選択します。
    
    ![国または地域の選択](./media/data-lake-store-power-bi/select-country.png "国/リージョンの選択")
17. 円グラフが自動的に更新され、ブラジルの各都市のドライバーが表示されます。
    
    ![国または地域のドライバー](./media/data-lake-store-power-bi/driver-per-country.png "国/地域ごとのドライバー")
18. **[ファイル]** メニューの **[保存]** をクリックして、Power BI Desktop ファイルとして視覚エフェクトを保存します。

## <a name="publish-report-to-power-bi-service"></a>レポートを Power BI サービスに発行する
Power BI Desktop で視覚エフェクトを作成したら、Power BI サービスに発行して他のユーザーと共有することができます。 その手順については、「 [Power BI Desktop からの発行](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)」参照してください。

## <a name="see-also"></a>関連項目
* [Data Lake Analytics を使用して Data Lake Storage Gen1 using Data Lake Analytics のデータを分析する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

