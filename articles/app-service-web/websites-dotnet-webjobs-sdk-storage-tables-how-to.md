---
title: "Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法"
description: "Web ジョブ SDK で Azure テーブル ストレージを使用する方法について説明します。 テーブルを作成し、エンティティをテーブルに追加し、既存のテーブルを読み取ります。"
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: 451432cc-c780-4310-85d3-84f44fe48afe
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 47db68afb8ea938a9861765f9e60c78436569110
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-azure-table-storage-with-the-webjobs-sdk"></a>Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法
## <a name="overview"></a>概要
このガイドでは、 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) Version 1.x を使用して Azure Storage テーブルを読み書きする方法を示す C# コード サンプルについて説明します。

このガイドは、Visual Studio で[ストレージ アカウント](websites-dotnet-webjobs-sdk-get-started.md)または[複数のストレージ アカウント](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs)を指定する接続文字列を使って Web ジョブ プロジェクトを作成する方法を理解していることを前提としています。

一部のコード スニペットは、[手動で呼び出される](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual)関数で使用される `Table` 属性を表示します。つまり、トリガー属性を使用して呼び出すのではありません。 

## <a id="ingress"></a> エンティティをテーブルに追加する方法
テーブルにエンティティを追加するには、`Table` 属性を `ICollector<T>` または `IAsyncCollector<T>` パラメーター (`T` は、追加するエンティティのスキーマ) に指定します。 この属性のコンストラクターは、テーブルの名前を指定する文字列パラメーターを受け取ります。 

次のコード サンプルでは、`Person`Ingress* という名前のテーブルに * エンティティを追加しています。

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() { 
                        PartitionKey = "Test", 
                        RowKey = i.ToString(), 
                        Name = "Name" }
                    );
            }
        }

`ICollector` には、`TableEntity` を継承する型または `ITableEntity` を実装する型を用いるのが一般的ですが、必須ではありません。 先ほど `Ingress` メソッドに示したコードは、次の 2 つの `Person` クラスのどちらでも正しく動作します。

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Azure Storage API を直接操作する場合は、メソッド シグネチャに `CloudStorageAccount` パラメーターを追加することもできます。

## <a id="monitor"></a> リアルタイム監視
多くの場合、データの受信関数は大量のデータを処理するため、Web ジョブ SDK のダッシュボードはリアルタイムの監視データを提供します。 **[Invocation Log]** セクションは、関数がまだ実行であるかどうかを示します。

![実行中の受信関数](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

**[Invocation Details]** ページには、実行中の関数の進行状況 (書き込まれたエンティティの数) が表示されます。このページで関数の実行を中止することもできます。 

![実行中の受信関数](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

関数が終了すると、書き込まれた行の数が **[Invocation Details]** ページに表示されます。

![完了した受信関数](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> テーブルから複数のエンティティを読み取る方法
テーブルの読み取りを行うには、`Table` 属性を `IQueryable<T>` パラメーター (`T` は、`TableEntity` を継承する型か、`ITableEntity` を実装する型) に指定します。

次のコード サンプルは、`Ingress` テーブルからすべての行を読み取り、ログに記録します。

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a id="readone"></a> テーブルから 1 つのエンティティを読み取る方法
1 つのテーブル エンティティにバインドする際に、パーティション キーと行キーを指定できる追加の 2 つのパラメーターを持つ `Table` 属性コンストラクターがあります。

次のコード サンプルは、キュー メッセージで受信したパーティション キー値と行キー値に基づいた `Person` エンティティのテーブル行を読み取ります。  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


この例の `Person` クラスは、`ITableEntity` を実装する必要はありません。

## <a id="storageapi"></a> .NET ストレージ API を直接使用して、テーブルを操作する方法
テーブルを操作する際に、`CloudTable` オブジェクトを使用して、より柔軟に `Table` 属性を使用することもできます。

次のコード サンプルは、 `CloudTable` オブジェクトを使用して、1 つのエンティティを *Ingress* テーブルに追加します。 

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

`CloudTable` オブジェクトの使用方法の詳細については、「 [.NET からテーブル ストレージを使用する方法](../storage/storage-dotnet-how-to-use-tables.md)」をご覧ください。 

## <a id="queues"></a>キューのハウツー記事で紹介されている関連トピック
キュー メッセージによってトリガーされるテーブルの処理の方法、テーブル処理に固有ではない WebJobs SDK のシナリオについては、「 [WebJobs SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)」をご覧ください。 

その記事では、以下のようなトピックが紹介されています。

* Async 関数
* 複数のインスタンス
* グレースフル シャットダウン
* 関数本体での Web ジョブ SDK 属性の使用
* コード内での SDK 接続文字列の設定
* コードの Web ジョブ SDK コンストラクター パラメーター値の設定
* 手動での関数のトリガー
* ログの書き込み

## <a id="nextsteps"></a> 次のステップ
このガイドでは、Azure テーブルを操作するための一般的なシナリオの処理方法を示すコードのサンプルを提供しました。 Azure Web ジョブ および Web ジョブ SDK の使用方法の詳細については、「 [Azure Web ジョブの推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)」を参照してください。


