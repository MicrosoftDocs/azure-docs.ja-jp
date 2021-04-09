---
title: Azure Data Factory を使用して Azure SQL Database テーブルを Azure CosmosDB に移行する
description: Azure SQL Database から既存の正規化されたデータベース スキーマを取得し、Azure Data Factory を使用して Azure CosmosDB 非正規化コンテナーに移行します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 3d67ac9474704fac39dbe7eb91aead5c4babc4ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383944"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>正規化されたデータベース スキーマを Azure SQL Database から Azure CosmosDB 非正規コンテナーに移行する

このガイドでは、Azure SQL Database の既存の正規化されたデータベース スキーマを取得し、それを Azure CosmosDB 非正規化スキーマに変換して Azure CosmosDB に読み込む方法について説明します。

SQL スキーマは、通常、3 番目の標準形式を使用してモデル化されます。その結果、データ整合性のレベルが高く重複するデータ値がより少ない正規化されたスキーマが得られます。 クエリでは、エンティティをテーブル間で結合して読み取りを行うことができます。 CosmosDB は、ドキュメント内でデータが自己完結した非正規化スキーマを介して、コレクションまたはコンテナー内で超迅速のトランザクションおよびクエリを実行できるように最適化されています。

Azure Data Factory を使用すれば、エンティティ リレーションシップとして主キーと外部キーを含む、Azure SQL Database の 2 つの正規化されたテーブルからの読み取りを、マッピング データ フローを使用して実行するパイプラインを作成できます。 ADF では、データ フロー Spark エンジンを使用してこれらのテーブルが 1 つのストリームに結合され、結合した行が配列に収集されてから、新しい Azure CosmosDB コンテナーへの挿入に合わせてクレンジングされたドキュメントが個別に生成されます。

このガイドでは、標準的な SQL Server AdventureWorks サンプル データベースからの ```SalesOrderHeader``` テーブルと ```SalesOrderDetail``` テーブルを使用する、"orders" という名前の新しいコンテナーをその場で構築します。 これらのテーブルは、```SalesOrderID``` によって結合された販売トランザクションを表します。 一意の各詳細レコードには、それぞれ ```SalesOrderDetailID``` という独自の主キーがあります。 ヘッダーと詳細の間のリレーションシップは ```1:M``` です。 ADF 内の ```SalesOrderID``` に参加して、それぞれの関連する詳細レコードを "detail" という配列にロールします。

このガイドの代表的な SQL クエリは次のとおりです。

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

結果として得られた CosmosDB コンテナーでは、内部クエリが 1 つのドキュメントに埋め込まれ、次のようになります。

![コレクション](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>パイプラインを作成する

1. **[+ 新しいパイプライン]** を選択して新しいパイプラインを作成します。

2. データ フロー アクティビティを追加する

3. データ フロー アクティビティで、 **[New mapping data flow]\(新しいマッピング データ フロー\)** を選択します。

4. 次のデータ フロー グラフを作成します

![データ フロー グラフ](media/data-flow/cosmosb1.png)

5. "SourceOrderDetails" 用のソースを定義します。 データセットの場合は、```SalesOrderDetail``` テーブルを指す新しい Azure SQL Database データセットを作成します。

6. "SourceOrderHeader" 用のソースを定義します。 データセットの場合は、```SalesOrderHeader``` テーブルを指す新しい Azure SQL Database データセットを作成します。

7. 上部のソースで、"SourceOrderDetails" の後に派生列変換を追加します。 この新しい変換 "TypeCast" と名付けます。 ```UnitPrice``` 列を丸めて、CosmosDB の Double データ型にキャストする必要があります。 数式を ```toDouble(round(UnitPrice,2))``` に設定します。

8. 別の派生列を追加し、"MakeStruct" 名付けます。 ここでは、details テーブルからの値を保持するための階層構造体を作成します。 details とヘッダーとの関係は ```M:1``` であることを思い出してください。 新しい構造体に ```orderdetailsstruct``` という名前を付けて、この方法で階層を作成します。これにより、各サブ列は入力列名に設定されます。

![構造の作成](media/data-flow/cosmosb9.png)

9. ここで、sales ヘッダー ソースに移りましょう。 結合変換を追加します。 右側の [MakeStruct] を選択します。 これを内部結合に設定されたままにして、結合条件の両側で ```SalesOrderID``` を選択します。

10. 追加した新しい結合の [データのプレビュー] タブをクリックして、この時点までの結果を確認できるようにします。 詳細行と結合されたすべてのヘッダー行が表示されます。 これは、```SalesOrderID``` から結合が形成された結果です。 次に、共通行からの詳細を details 構造体に結合し、共通行を集計します。

![Join](media/data-flow/cosmosb4.png)

11. これらの行を非正規化する配列を作成するには、その前にまず不要な列を削除し、データ値が CosmosDB データ型と一致することを確認する必要があります。

12. 次に変換を選択を追加し、フィールド マッピングを次のように設定します。

![列削除機能](media/data-flow/cosmosb5.png)

13. ここで、もう一度、通貨の列をキャストしてみましょう。今度は ```TotalDue``` です。 上記の手順 7 で行ったように、式を ```toDouble(round(TotalDue,2))``` に設定します。

14. ここでは、共通キー ```SalesOrderID``` をグループ化することによって行を非正規化します。 集計変換を追加し、グループ化を ```SalesOrderID``` に設定します。

15. 集計式に "details" という名前の新しい列を追加し、この式を使用して、前に作成した ```orderdetailsstruct``` という名前の構造体内の値を収集します: ```collect(orderdetailsstruct)```

16. 集計式またはグループ化式の一部である列のみが、集計変換から出力されます。 そのため、sales ヘッダーからの列も含める必要があります。 これを行うには、それと同じ集計変換に列パターンを追加します。 このパターンでは、他のすべての列が出力に含められます。

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. 他のプロパティでも "この" 構文を使用することで、同じ列名を維持し、集計に ```first()``` 関数を使用するようにします。

![Aggregate](media/data-flow/cosmosb6.png)

18. シンク変換を追加すれば、移行フローを完了する準備が整います。 データセットの横にある [新規] をクリックし、ご利用の CosmosDB データベースを指す CosmosDB データセットを追加します。 コレクションの場合は、それに "orders" という名前を付けます。これは、その場で作成されるため、スキーマがなく、ドキュメントも作成されません。

19. [シンクの設定] で、[パーティション キー] を ```\SalesOrderID``` とし、コレクション アクションを "再作成" とします。 ご利用のマッピング タブが次のようになっていることを確認します。

![[マッピング] タブを示すスクリーンショット。](media/data-flow/cosmosb7.png)

20. [データのプレビュー] をクリックして、これらの 32 行が新しいドキュメントとして新しいコンテナーに挿入されるように設定されていることを確認します。

![[データのプレビュー] タブを示すスクリーンショット。](media/data-flow/cosmosb8.png)

すべて問題がないようであれば、新しいパイプラインを作成し、このデータ フロー アクティビティをそのパイプラインに追加して実行する準備ができたことになります。 デバッグまたはトリガーされた実行から実行できます。 数分後に、ご利用の CosmosDB データベースには、"orders" という名前で注文に関する非正規化コンテナーが新規に作成されます。

## <a name="next-steps"></a>次のステップ

* マッピング データ フローの[変換](concepts-data-flow-overview.md)を使用して、残りのデータ フロー ロジックを構築します。
* このチュートリアル用の[完成したパイプライン テンプレートをダウンロード](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip)し、そのテンプレートをご自分のファクトリにインポートします。
