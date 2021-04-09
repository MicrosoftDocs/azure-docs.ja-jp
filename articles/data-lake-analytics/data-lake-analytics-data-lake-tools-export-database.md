---
title: U-SQL データベースをエクスポートする - Azure Data Lake Tools for Visual Studio
description: Azure Data Lake Tools for Visual Studio を使用して、U-SQL データベースをエクスポートし、ローカル アカウントに自動的にインポートする方法を説明します。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 11/27/2017
ms.openlocfilehash: 0a768990a33f0d60a3222bd3ad323edcc53c9ec0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219090"
---
# <a name="export-a-u-sql-database"></a>U-SQL データベースのエクスポート

この記事では、[Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) を使用して U-SQL データベースを 1 つの U-SQL スクリプトとダウンロードされたリソースとしてエクスポートする方法について説明します。 同じプロセスで、エクスポートされたデータベースをローカル アカウントにインポートできます。

お客様は、通常、開発、テストおよび運用のために複数の環境を管理しています。 これらの環境は、開発者のローカル コンピューター上のローカル アカウントと、Azure 上の Azure Data Lake Analytics アカウントの両方でホストされています。 

開発環境とテスト環境で U-SQL クエリを開発およびチューニングする際に、多くの場合、開発者は運用データベースに作業内容を再作成する必要があります。 データベースのエクスポート ウィザードを使用して、このプロセスを高速化できます。 ウィザードを使用すると、開発者は、既存のデータベース環境とサンプル データを他の Data Lake Analytics アカウントに複製できます。

## <a name="export-steps"></a>エクスポートの手順

### <a name="step-1-export-the-database-in-server-explorer"></a>手順 1: サーバー エクスプローラーでデータベースをエクスポートする

自分が権限を持つすべての Data Lake Analytics アカウントがサーバー エクスプローラーに表示されます。 データベースをエクスポートするには:

1. サーバー エクスプローラーで、エクスポートするデータベースを含むアカウントを展開します。
2. データベースを右クリックし、**[エクスポート]** を選択します。 
   
    ![サーバー エクスプローラー - データベースのエクスポート](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     **[エクスポート]** メニュー オプションが使用可能でない場合は、[ツールを最新リリースに更新](https://aka.ms/adltoolsvs)する必要があります。

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>手順 2: エクスポートするオブジェクトを構成する

大規模データベースのごく一部のみが必要な場合は、エクスポート ウィザードでエクスポートするオブジェクトのサブセットを構成できます。 

U-SQL ジョブを実行して、エクスポート アクションを完了します。 そのため、Azure アカウントからのエクスポートではコストが発生します。

![データベースのエクスポート ウィザード - エクスポートするオブジェクトの選択](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>手順 3: オブジェクト一覧やその他の構成を確認する

この手順では、選択したオブジェクトを **[エクスポート オブジェクト一覧]** ボックスで確認することができます。 エラーがある場合は、**[前へ]** を選択して前に戻り、エクスポートするオブジェクトを正しく構成します。

エクスポート ターゲットの他の設定を構成することもできます。 構成の説明を次の表に示します。

|構成|説明|
|-------------|-----------|
|宛先名|この名前は、エクスポートされたデータベース リソースを保存する場所を示します。 たとえば、アセンブリ、追加のファイル、サンプル データなどです。 この名前のフォルダーが、ローカル データ ルート フォルダー下に作成されます。|
|プロジェクト ディレクトリ|このパスは、エクスポートされた U-SQL スクリプトを保存する場所を定義します。 すべてのデータベース オブジェクト定義はこの場所に保存されます。|
|スキーマのみ|このオプションを選択すると、データベースの定義およびリソース (アセンブリや追加のファイルなど) のみがエクスポートされます。|
|スキーマとデータ|このオプションを選択すると、データベース定義、リソース、データがエクスポートされます。 テーブルの上位 N 行がエクスポートされます。|
|ローカル データベースに自動的にインポートします|このオプションを選択した場合、エクスポートが完了すると、エクスポートされたデータベースがローカル データベースに自動的にインポートされます。|

![データベースのエクスポート ウィザード - エクスポート オブジェクト一覧とその他の構成](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>手順 4: エクスポート結果を確認する

エクスポートが完了したら、ウィザードのログ ウィンドウでエクスポート結果を表示できます。 次の例では、エクスポートされた U-SQL スクリプトと、アセンブリ、追加のファイル、サンプル データなどのデータベース リソースを検索する方法を示します。

![データベースのエクスポート ウィザード - エクスポート結果](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>エクスポートされたデータベースをローカル アカウントにインポートする

エクスポートされたデータベースをインポートする最も便利な方法は、手順 3. のエクスポート プロセス中に **[ローカル データベースに自動的にインポートします]** チェック ボックスをオンにすることです。 このボックスをオンにしなかった場合は、エクスポートされた U-SQL スクリプトをエクスポート ログで検索します。 次に、ローカルで U-SQL スクリプトを実行して、ローカル アカウントにデータベースをインポートします。

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>エクスポートされたデータベースを Data Lake Analytics アカウントにインポートする

データベースを別の Data Lake Analytics アカウントにインポートするには:

1. アセンブリ、追加のファイル、サンプル データを含むエクスポートされたリソースを、インポートする Data Lake Analytics アカウントの既定の Azure Data Lake Store アカウントにアップロードします。 エクスポートしたリソース フォルダーはローカル データ ルート フォルダー下に見つかります。 既定の Data Lake Store アカウントのルートにフォルダー全体をアップロードします。
2. アップロードが完了したら、データベースをインポートする Data Lake Analytics アカウントに、エクスポートされた U-SQL スクリプトを送信します。

## <a name="known-limitations"></a>既知の制限事項

現時点では、ステップ 3. で **[スキーマとデータ]** を選択した場合は、テーブルに格納されているデータをエクスポートするために U-SQL ジョブが実行されます。 このため、データのエクスポート プロセスが低速になり、コストが増加する可能性があります。 

## <a name="next-steps"></a>次のステップ

* [U-SQL データベースの詳細](/u-sql/data-definition-language-ddl-statements) 
* [ローカル実行と Azure Data Lake U-SQL SDK を使用した U-SQL ジョブのテストおよびデバッグ](data-lake-analytics-data-lake-tools-local-run.md)


