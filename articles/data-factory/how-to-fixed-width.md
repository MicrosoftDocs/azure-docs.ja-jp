---
title: Azure Data Factory の Mapping Data Flow を使用して固定長テキスト ファイルを処理する
description: Azure Data Factory の Mapping Data Flow を使用して固定長テキスト ファイルを処理する方法について説明します。
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 9964aaf060c43cc3e9992f515bf272011e795043
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962125"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Data Factory の Mapping Data Flow を使用して固定長テキスト ファイルを処理する

Microsoft Azure Data Factory のマッピング データ フローを使用すると、固定幅のテキスト ファイルからデータを変換できます。 次のタスクでは、区切り記号を使用せずにテキスト ファイルのデータセットを定義し、次に序数の位置に基づいて部分文字列の分割を設定します。

## <a name="create-a-pipeline"></a>パイプラインを作成する。

1. **[+ 新しいパイプライン]** を選択して新しいパイプラインを作成します。

2. 固定幅ファイルの処理に使用されるデータ フロー アクティビティを追加します。

    ![固定幅パイプライン](media/data-flow/fwpipe.png)

3. データ フロー アクティビティで、 **[New Mapping Data Flow]\(新しい Mapping Data Flow\)** を選択します

4. ソース、派生列、選択、およびシンクの変換を追加します。

    ![固定幅データ フロー](media/data-flow/fw2.png)

5. 新しいデータ セットを使用するようにソース変換を構成します。これは区切りテキスト型になります。

6. 列区切り記号またはヘッダーは設定しないでください。

   ここで、このファイルの内容のフィールドの開始点と長さを設定します。

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. ソース変換の **[プロジェクション]** タブには、*Column_1* という名前の文字列型の列が表示されます。

8. 派生列で新しい列を作成します。

9. 列には、*col1* のように簡単な名前を付けます。

10. 式ビルダーで、次のように入力します。

    ```substring(Column_1,1,4)```

    ![派生列](media/data-flow/fwderivedcol1.png)

11. 解析する必要があるすべての列について、手順 10 を繰り返します。

12. **[Inspect]\(検査\)** タブを選択すると、生成される新しい列が表示されます。

    ![検査](media/data-flow/fwinspect.png)

13. SELECT 変換を使用して、変換に必要のない列をすべて削除します。

    ![SELECT 変換](media/data-flow/fwselect.png)

14. シンクを使用して、データをフォルダーに出力します。

    ![固定幅シンク](media/data-flow/fwsink.png)

    出力は次のようになります。

    ![固定幅出力](media/data-flow/fxdoutput.png)

  固定幅データがそれぞれ 4 文字で分割され、Col1、Col2、Col3、Col4 などに割り当てられます。 前の例に基づいて、データは 4 つの列に分割されます。

## <a name="next-steps"></a>次の手順

* Mapping Data Flow の[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します
