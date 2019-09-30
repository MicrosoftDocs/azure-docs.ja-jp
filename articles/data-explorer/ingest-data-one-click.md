---
title: ワンクリックでのインジェストを使用して Azure Data Explorer にデータを取り込む
description: ワンクリックでのインジェストを使用するだけで、Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69519830"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>ワンクリックでのインジェストを使用して Azure Data Explorer にデータを取り込む

この記事では、ワンクリックでのインジェストを使用して json 形式または csv 形式の新しいテーブルをストレージから Azure Data Explorer に簡単に取り込む方法について説明します。 データが取り込まれたら、Web UI を使用してテーブルを編集したりクエリを実行したりできます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [アプリケーション](https://dataexplorer.azure.com/)にサインインします。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成する
* Azure Storage 内のデータのソース。

## <a name="ingest-new-data"></a>新しいデータを取り込む

1. "*データベース名*" を右クリックし、 **[Ingest new data (Preview)]\(新しいデータの取り込み (プレビュー)\)** を選択します

    ![Web UI でワンクリックでのインジェストを選択する](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. **[データ インジェスト (プレビュー)]** ウィンドウの **[ソース]** タブで、 **[プロジェクトの詳細]** を入力します。

    * 新しい **[テーブル名]** を入力します。 
    * **[Ingestion type]\(インジェストの種類\)**  > [ストレージから] **の順に選択します**。
    * **[ストレージへのリンク]** を入力します。ストレージへの URL を追加します。 プライベート ストレージ アカウントには BLOB SAS URL を使用します。 
    * **[スキーマの編集]** を選択します。
 
    ![ワンクリックでのインジェスト ソースの詳細](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. **[スキーマ]** タブの **[データ形式]** で、ドロップダウンから **[JSON]** または **[CSV]** を選択します。 
   
   **[CSV]** を選択した場合:
    * **[Ignore headline]\(見出しを無視する\)** チェックボックスをオンにすると、csv ファイルの見出し行が無視されます。    
    * **[マッピング名]** は自動的に設定されますが、編集できます。

    ![ワンクリックでのインジェスト csv 形式スキーマ.png](media/ingest-data-one-click/one-click-csv-format.png)

   **[JSON]** を選択した場合:
    * **[JSON レベル]** を選択します。ドロップダウンで 1 - 10 を選択します。 Json ファイルのレベルは、右下の表に表示されます。 
    * **[マッピング名]** は自動的に設定されますが、編集できます。

    ![ワンクリックでのインジェスト json 形式スキーマ](media/ingest-data-one-click/one-click-json-format.png)  

1. **[エディター]** で、右側の **[V]** を選択してエディターを開きます。 エディターでは、ご自分の入力から生成された自動クエリを表示およびコピーできます。 

1.  右下にあるテーブルでは、次の操作を行います。 
    * **[列名の変更]** 、 **[列の削除]** 、 **[昇順で並べ替え]** 、 **[降順で並べ替え]** を行うには列の右側にある **[V]** を選択します
    * 編集するには列名をダブルクリックします。
    * データ型を変更するには、列名の左側にあるアイコンを選択します。 

1. テーブルの作成、マッピングの作成、およびデータ インジェストを行うには、 **[Start ingestion]\(インジェストの開始\)** を選択します。
 
## <a name="query-data"></a>データのクエリを実行する

1. **[Data ingestion completed]\(データ インジェストが完了しました\)** ウィンドウでは、データ インジェストが正常に完了した場合、3 つのステップすべてに緑色のチェックマークが表示されます。 
 
    ![ワンクリックでのデータ インジェストの完了](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. **[V]** を選択してクエリを開きます。 クエリを編集するには、Web UI にコピーします。

1. 右側のメニューには、 **[Quick queries]\(クイック クエリ\)** と **[ツール]** が含まれています。 

    * **[Quick queries]\(クイック クエリ\)** には、クエリの例を備えた Web UI へのリンクが含まれています。
    * **[ツール]** には、**Drop コマンド** を備えた Web UI へのリンクが含まれています。これにより、関連する `.drop` コマンドを実行して問題のトラブルシューティングを行うことができます。

    > [!TIP]
    > `.drop` コマンドを使用した場合は、データが失われることがあります。 慎重に使用してください。

## <a name="next-steps"></a>次の手順

* [Azure Data Explorer の Web UI でデータのクエリを実行する](web-query-data.md)
* [Kusto クエリ言語を使用して Azure Data Explorer のクエリを作成する](write-queries.md)
