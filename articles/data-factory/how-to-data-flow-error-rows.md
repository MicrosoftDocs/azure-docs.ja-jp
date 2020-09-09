---
title: Azure Data Factory のマッピング データ フローを使用してエラー行を処理する
description: Azure Data Factory のマッピング データ フローを使用して SQL の切り捨てエラーを処理する方法について説明します。
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 3f8ac2d1434019548b01d8468015a543d89d0fba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85254414"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Data Factory のマッピング データ フローで SQL の切り捨てエラー行を処理する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data Factory でマッピング データ フローを使用する一般的なシナリオは、変換済みのデータを Azure SQL Database のデータベースに書き込むことです。 このシナリオでは一般に、列の切り捨てが起こりやすく、そのようなエラー状態は回避する必要があります。 以降の手順に従って、書き込み先の文字列型列に収まらない列をログに記録するしくみを作れば、そのようなシナリオでもデータ フローを続行することができます。

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

## <a name="next-steps"></a>次のステップ

* マッピング データ フローの[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します。
