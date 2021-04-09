---
title: Azure Data Factory のマッピング データ フローを使用してエラー行を処理する
description: Azure Data Factory のマッピング データ フローを使用して SQL の切り捨てエラーを処理する方法について説明します。
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: a7a03ff1a58f50f16ebefce48b9e2772a16a011a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386341"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Data Factory のマッピング データ フローで SQL の切り捨てエラー行を処理する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data Factory でマッピング データ フローを使用する一般的なシナリオは、変換済みのデータを Azure SQL Database のデータベースに書き込むことです。 このシナリオでは一般に、列の切り捨てが起こりやすく、そのようなエラー状態は回避する必要があります。

ADF データ フロー内のデータベース シンクにデータを書き込むときにエラーを処理するには、主に 2 つの方法があります。

* データベース データを処理するときに、シンクの[エラー行の処理](./connector-azure-sql-database.md#error-row-handling)を [エラーのまま続行する] に設定します。 これは、データ フロー内でカスタム ロジックを必要としない、包括的な自動化された方法です。
* または、次の手順に従って、書き込み先の文字列型列に収まらない列をログに記録するしくみを作れば、データ フローを続行することができます。

> [!NOTE]
> 独自のエラー処理ロジックを記述する次のメソッドではなく、エラー行の自動処理を有効にすると、エラーをトラップするための 2 段階操作を実行する ADF によって、発生するパフォーマンスの低下が抑えられ、追加の手順が実行されます。

## <a name="scenario"></a>シナリオ

1. 書き込み先のデータベース テーブルには、"name" という ```nvarchar(5)``` 列があります。

2. データ フロー内で、映画のタイトルをシンクから書き込み先の "name" 列にマッピングしたいと考えています。

    ![映画のデータ フロー 1](media/data-flow/error4.png)
    
3. 問題は、シンク列に格納できる文字数が 5 文字までであり、映画のタイトルが入りきらないケースがありそうだということです。 このデータ フローを実行すると、次のようなエラーが発生します: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

このビデオでは、データ フローにエラー行の処理ロジックを設定する例を紹介しています。
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>この状況を回避する設計方法

1. このシナリオでは、"name" 列の最大長は 5 文字です。 そこで、条件分割変換を追加して、"titles" が 5 文字を超える行はログに記録し、スペースに格納できる他の行についてはデータベースに書き込むことができるようにしてみましょう。

    ![条件分割](media/data-flow/error1.png)

2. この条件分割変換では、"title" の最大長を 5 として定義しています。 5 文字以下の行は ```GoodRows``` ストリームに送信されます。 5 文字を超える行は ```BadRows``` ストリームに送信されます。

3. 今度は、失敗した行をログに記録する必要があります。 ```BadRows``` ストリームに、ログのためのシンク変換を追加します。 ここでは、完全なトランザクション レコードのログが得られるよう、すべてのフィールドを "自動マッピング" します。 これは、Blob Storage の単一ファイルに出力されるテキスト区切りの CSV ファイルです。 このログ ファイルを "badrows.csv" と呼ぶことにしましょう。

    ![不良な行](media/data-flow/error3.png)
    
4. 完成したデータ フローは次のとおりです。 エラー行を切り離すことで SQL 切り捨てエラーを回避し、それらのエントリをログ ファイルに出力できるようになりました。 その一方で、成功した行は、引き続きターゲット データベースに書き込むことができます。

    ![完成したデータ フロー](media/data-flow/error2.png)

5. シンク変換でエラー行の処理オプションを選択し、[Output error rows]\(エラー行の出力\) を設定した場合、ADF によって、行データの CSV ファイル出力が、ドライバーから報告されたエラー メッセージと共に自動的に生成されます。 別のオプションを使用して、そのロジックをデータ フローに手動で追加する必要はありません。 このオプションを使用すると、パフォーマンスの低下が抑えられるため、ADF による 2 段階手法の実装、およびエラーのトラップとログ記録が可能になります。

    ![エラー出力のある完全なデータ フロー](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>次のステップ

* マッピング データ フローの[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します。