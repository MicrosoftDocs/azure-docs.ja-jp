---
title: "要求ユニットとスループットの推定 - Azure DocumentDB | Microsoft Docs"
description: "DocumentDB の要求ユニット要件を確認、指定、推定する方法について説明します。"
services: documentdb
author: syamkmsft
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b098e3087cb08528c5fbdc2d0d768ce40e7ffe0d
ms.lasthandoff: 03/15/2017


---
# <a name="request-units-in-documentdb"></a>DocumentDB の要求ユニット
DocumentDB の [要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)が新たに公開されました。 詳細については、「 [スループットのニーズの推定](documentdb-request-units.md#estimating-throughput-needs)」を参照してください。

![Throughput calculator][5]

## <a name="introduction"></a>はじめに
[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) は、JSON ドキュメント用の完全に管理された、スケーラブルな NoSQL データベース サービスです。 DocumentDB では、仮想マシンのレンタル、ソフトウェアのデプロイ、データベースの監視などを自分で行う必要はありません。 Microsoft のエンジニアによって運用され、継続的な監視が行われる DocumentDB は、卓越した可用性、パフォーマンス、データ保護を提供します。 DocumentDB のデータは、柔軟で可用性の高いコンテナーであるコレクションに格納されます。 コレクションの CPU、メモリ、IOPS などのハードウェア リソースを考慮したり管理したりする代わりに、1 秒あたりの要求数の観点からスループットを予約できます。 DocumentDB は、コレクションのプロビジョニング、透過的なパーティション分割、スケーリングを自動的に管理して、プロビジョニングされた要求数を処理します。 

DocumentDB は、読み取り、書き込み、クエリ、ストアド プロシージャ実行のための多数の API をサポートしています。 すべての要求が同等ではないため、要求を処理するために必要な計算量に基づいて、正規化された**要求ユニット**の量が割り当てられます。 1 つの操作の要求ユニットの数は確定的であり、DocumentDB のすべての操作で使用された要求ユニットの数は応答ヘッダーを介して追跡できます。

DocumentDB の各コレクションは、スループットで予約できます。これは要求ユニットによっても表されます。 つまり、1 秒あたり 100 要求ユニットのブロックで、1 秒あたり数百から数百万の要求ユニットまで予約できます。 プロビジョニングしたスループットは、そのコレクションの有効期間にわたって、アプリケーションの処理ニーズの変化やアクセス パターンに合わせて調整できます。 

この記事を読むと、次の質問に回答できるようになります。  

* 要求ユニットと要求の使用量とは何ですか。
* コレクションの要求ユニットの量をどのように指定しますか。
* アプリケーションの要求ユニットのニーズをどのように推定しますか。
* コレクションの要求ユニットの量を超過すると、どうなりますか。

## <a name="request-units-and-request-charges"></a>要求ユニットと要求の使用量
DocumentDB と MongoDB 用 API を使用して、アプリケーションのスループット ニーズを満たすリソースを "*予約する*" ことで、高速の予測可能なパフォーマンスが得られます。  アプリケーション負荷とアクセス パターンは時間と共に変化するため、DocumentDB はアプリケーションで利用できる予約済みスループットの量を簡単に増減できる機能を備えています。

DocumentDB では、1 秒間に処理する要求ユニットで、予約済みスループットを指定します。  要求ユニットはスループットの通貨と考えることができます。この通貨によって、アプリケーションで利用できる保証された要求ユニット量を秒単位で "*予約*" できます。  ドキュメントの作成、クエリの実行、ドキュメントの更新など、DocumentDB での各操作は、CPU、メモリ、IOPS を消費します。  つまり、それぞれの操作により、"*要求の使用量*" が発生し、それが "*要求ユニット*" で表されます。  アプリケーションのスループット要件と共に、要求ユニット使用量に影響を及ぼす要因を理解しておくと、できるだけコスト効率の高い方法でアプリケーションを実行できます。 

まずは以下のビデオを見ることをお勧めします。このビデオでは、Aravind Ramachandran が要求ユニットと DocumentDB での予測可能なパフォーマンスについて説明しています。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-documentdb"></a>DocumentDB での要求ユニット量の指定
DocumentDB コレクションの作成時に、そのコレクション用に予約する&1; 秒ごとの要求ユニット数 (RU/秒) を指定します。 プロビジョニング済みのスループットに基づいて、DocumentDB はコレクションをホストする物理パーティションを割り当て、データの増加に応じてパーティション間でデータの分割やバランスの再調整を行います。

DocumentDB では、10,000 以上の要求ユニットでコレクションがプロビジョニングされるとき、パーティション キーを指定する必要があります。 パーティション キーは、今後 10,000 要求ユニットを超えてコレクションのスループットをスケーリングするためにも必要です。 したがって、初期のスループットに関係なく、コレクションの作成時に[パーティション キー](documentdb-partition-data.md)を構成しておくことを強くお勧めします。 データが複数のパーティションに分割される場合があるため、コレクションと要求が DocumentDB によって一様にスケーリングできるように、高基数 (数百から数百万の個別の値) のパーティション キーを選択する必要があります。 

> [!NOTE]
> パーティション キーは論理境界であり、物理的な境界ではありません。 したがって、個別のパーティション キー値の数を制限する必要はありません。 DocumentDB には他にも負荷分散のオプションがあるので、個別のパーティション キー値は多くしておくことをお勧めします。

次に示すコード スニペットは、.NET SDK を使用して、1 秒あたり 3,000 要求ユニットのコレクションを作成します。

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

DocumentDB は、スループットの予約モデルで運用されます。 つまり、実際に "*使用した*" スループット量ではなく、コレクション用に "*予約した*" スループット量に対する料金が請求されます。 アプリケーションの負荷やデータ、使用パターンが変化したときは、DocumentDB SDK や [Azure Portal](https://portal.azure.com) を使用して、予約済み RU の量を簡単にスケールアップしたりスケールダウンしたりできます。

各コレクションは、DocumentDB の `Offer` リソースにマップされます。このリソースは、コレクションのプロビジョニング済みスループットに関するメタデータを持っています。 割り当て済みのスループットを変更するには、コレクションに対応する Offer リソースを検索して、新しいスループット値に更新します。 次に示すコード スニペットは、.NET SDK を使用して、コレクションのスループットを 1 秒あたり 5,000 要求ユニットに変更します。

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

スループットを変更しても、コレクションの可用性には影響しません。 通常、新しく予約されたスループットは、数秒後にアプリケーションで有効になります。

## <a name="specifying-request-unit-capacity-in-api-for-mongodb"></a>MongoDB 用 API での要求ユニット量の指定
MongoDB 用 API では、コレクション用に予約する&1; 秒ごとの要求ユニット数 (RU/秒) を指定することができます。

MongoDB 用 API は、DocumentDB 同様、スループットに基づく予約モデルで動作します。 つまり、実際に "*使用した*" スループット量ではなく、コレクション用に "*予約した*" スループット量に対する料金が請求されます。 アプリケーションの負荷やデータ、使用パターンが変化したときは、[Azure Portal](https://portal.azure.com) を使用して、予約済み RU の量を簡単にスケールアップしたりスケールダウンしたりできます。

スループットを変更しても、コレクションの可用性には影響しません。 通常、新しく予約されたスループットは、数秒後にアプリケーションで有効になります。

## <a name="request-unit-considerations"></a>要求ユニットの考慮事項
DocumentDB コレクション用に予約する要求ユニット数を推定する際は、以下の変数についても検討することが重要です。

* **ドキュメント サイズ**。 ドキュメントのサイズが増大すると、データの読み取りまたは書き込みに使用される単位も増加します。
* **ドキュメント プロパティの数**。 すべてのプロパティに既定でインデックスが作成されると想定すると、プロパティ数の増加に伴って、ドキュメントの書き込みに使用される単位が増加します。
* **データ整合性**。 Strong または Bounded Staleness のデータの整合性レベルを使用すると、ドキュメントの参照に使用される単位が増加します。
* **インデックス付きプロパティ**。 各コレクションのインデックス ポリシーによって、既定でインデックスが作成されるプロパティが決まります。 インデックス付きプロパティの数を制限するか、非同期インデックス作成を有効にして、使用する要求ユニットを削減できます。
* **ドキュメントのインデックス作成**。 各ドキュメントには既定で自動的にインデックスが作成されます。一部のドキュメントにインデックスを作成しないようにすると、使用される要求単位が少なくなります。
* **クエリのパターン**。 クエリの複雑さは、操作で消費される要求ユニット数に影響します。 述語の数、述語の特性、プロジェクション、UDF 数、ソース データ セットのサイズのすべてが、クエリ操作のコストに影響します。
* **スクリプトの使用**。  クエリと同様に、ストアド プロシージャとトリガーは、実行する操作の複雑さに基づいて要求ユニットを使用します。 アプリケーションを開発するときは、ヘッダーを調べて、各操作で使用される要求単位の量を詳しく把握するようにしてください。

## <a name="estimating-throughput-needs"></a>スループットのニーズの推定
要求単位は、要求の処理コストを標準化した測定単位です。 1 つの要求ユニットは、10 個の一意のプロパティ値 (システム プロパティを除く) で構成される 1 KB JSON ドキュメントの読み取り (self リンクまたは ID による) に必要な処理能力を表します。 同じドキュメントを作成 (挿入)、置換、または削除する要求では、必要になるサービスの処理が多くなるため、使用される要求ユニットも多くなります。   

> [!NOTE]
> 1 KB のドキュメントに対する 1 つの要求ユニットのベースラインは、ドキュメントの self リンクまたは ID による単純な GET に対応します。
> 
> 

たとえば、次の表は、3 種類のサイズのドキュメント (1 KB、4 KB、64 KB) を 2 種類のパフォーマンス レベル (500 読み取り/秒 + 100 書き込み/秒と、500 読み取り/秒 + 500 書き込み/秒) でプロビジョニングした場合の要求ユニット数を示しています。 データの一貫性はセッションで構成され、インデックス作成ポリシーはなしに設定されています。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>ドキュメント サイズ</strong></p></td>
            <td valign="top"><p><strong>読み取り数/秒</strong></p></td>
            <td valign="top"><p><strong>書き込み数/秒</strong></p></td>
            <td valign="top"><p><strong>要求ユニット</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1,000 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3,000 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = 1,350 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = 4,150 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9,800 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29,000 RU/秒</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>要求ユニット計算ツールの使用
スループットをユーザーが緻密に推定できるように、Web ベースの[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)が用意されています。たとえば以下に示す標準的な操作で必要になる要求ユニットを見積もることができます。

* ドキュメントの作成 (書き込み)
* ドキュメントの読み取り
* ドキュメントの削除
* ドキュメントの更新

このツールには、指定したサンプル ドキュメントに基づいたデータ ストレージ ニーズの見積もりのサポートも含まれています。

ツールの使い方は簡単です。

1. 代表的な JSON ドキュメントを少なくとも&1; つアップロードします。
   
    ![Upload documents to the request unit calculator][2]
2. データ ストレージの要件を見積もるには、格納するドキュメントの総数を入力します。
3. ドキュメントの作成、読み取り、更新、削除に関して必要な操作数 (毎秒あたり) を入力します。 ドキュメントの更新操作の要求ユニットの料金を見積もるには、上記の手順 1. のサンプル ドキュメントのうち、代表的なフィールドの更新が含まれているドキュメントのコピーをアップロードします。  たとえば、ドキュメントの更新では、通常、lastLogin と userVisits という&2; つのプロパティだけを変更する場合は、サンプル ドキュメントをコピーし、その&2; つのプロパティの値を更新して、コピーしたドキュメントをアップロードします。
   
    ![Enter throughput requirements in the request unit calculator][3]
4. 計算ボタンをクリックして結果を確認します。
   
    ![Request unit calculator results][4]

> [!NOTE]
> ドキュメントの種類によって、サイズとインデックス付きプロパティの数が大きく異なる場合は、それぞれの *種類* における代表的なドキュメントのサンプルをツールにアップロードしたうえで、結果を計算してください。
> 
> 

### <a name="use-the-documentdb-request-charge-response-header"></a>DocumentDB の "要求の使用量" 応答ヘッダーの使用
DocumentDB サービスからの各応答には、要求で使用される要求ユニットを含むカスタム ヘッダー (`x-ms-request-charge`) が付きます。 このヘッダーには、DocumentDB SDK を介してアクセスすることもできます。 .NET SDK では、RequestCharge は ResourceResponse オブジェクトのプロパティです。  Azure ポータルの DocumentDB クエリ エクスプローラーは、実行されたクエリに関する要求の使用量情報を示します。

![Examining RU charges in the Query Explorer][1]

これを踏まえて、アプリケーションに必要な予約済みスループットの量を推定するには、典型的な操作の実行に関連する要求ユニット使用量を記録し、アプリケーションが使用する代表的なドキュメントに基づいて、1 秒ごとに実行される操作数を推定します。  さらに、通常のクエリと DocumentDB スクリプトの使用も忘れずに測定し、考慮に入れます。

> [!NOTE]
> ドキュメントの種類によって、サイズとインデックス付きプロパティの数が大きく異なる場合は、典型的なドキュメントの *種類* ごとに、適用可能な操作の要求ユニット使用量を記録してください。
> 
> 

次に例を示します。

1. 典型的なドキュメント作成 (挿入) の要求ユニット使用量を記録します。 
2. 典型的なドキュメント読み取りの要求ユニット使用量を記録します。
3. 典型的なドキュメント更新の要求ユニット使用量を記録します。
4. 典型的な共通ドキュメントのクエリの要求ユニット使用量を記録します。
5. アプリケーションが利用するカスタム スクリプト (ストアド プロシージャ、トリガー、ユーザー定義関数) の要求ユニット使用量を記録します。
6. 予想される&1; 秒あたりの操作実行数の推定値に基づいて、必要な要求ユニットを計算します。

### <a id="GetLastRequestStatistics"></a> MongoDB 用 API の GetLastRequestStatistics コマンドを使用する
MongoDB 用 API は、指定した操作の要求の使用量を取得するためのカスタム コマンド*getLastRequestStatistics* をサポートしています。

たとえば、Mongo シェルで、要求の使用量を確認する操作を実行します。
```
> db.sample.find()
```

次に、*getLastRequestStatistics* コマンドを実行します。
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

これを踏まえて、アプリケーションに必要な予約済みスループットの量を推定するには、典型的な操作の実行に関連する要求ユニット使用量を記録し、アプリケーションが使用する代表的なドキュメントに基づいて、1 秒ごとに実行される操作数を推定します。

> [!NOTE]
> ドキュメントの種類によって、サイズとインデックス付きプロパティの数が大きく異なる場合は、典型的なドキュメントの *種類* ごとに、適用可能な操作の要求ユニット使用量を記録してください。
> 
> 

## <a name="use-api-for-mongodbs-portal-metrics"></a>MongoDB 用 API のポータルのメトリックを使用する
MongoDB データベース用 API の要求ユニットの適切な推定使用量を取得する簡単な方法は、[Azure Portal](https://portal.azure.com) のメトリックを使用することです。 "*要求数*" のグラフと "*要求の使用量*" のグラフから、各操作が使用している要求単位の数と、互いに使用する要求単位の数を推定できます。

![MongoDB 用 API のポータルのメトリック][6]

## <a name="a-request-unit-estimation-example"></a>要求ユニット推定の例
次の&1; KB 未満のドキュメントについて考えてみましょう。

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> ドキュメントは DocumentDB で縮小されるため、システムで計算される上記ドキュメントのサイズは 1 KB をわずかに下回ります。
> 
> 

次の表は、このドキュメントに対する典型的な操作に要する、大まかな要求ユニット使用量を示しています (大まかな要求ユニット使用量は、アカウント一貫性レベルが "セッション" に設定され、すべてのドキュメントに自動でインデックス作成が行われることを想定しています)。

| 操作 | 要求ユニット使用量 |
| --- | --- |
| ドキュメントの作成 |～15 RU |
| ドキュメントの読み取り |～1 RU |
| ID でのドキュメントのクエリ |～2.5 RU |

さらに次の表では、アプリケーションで通常使用されるクエリについて、大まかな要求ユニット使用量を示しています。

| クエリ | 要求ユニット使用量 | # 返されるドキュメント数 |
| --- | --- | --- |
| ID で食品を選択 |～2.5 RU |1 |
| 製造者で食品を選択 |～7 RU |7 |
| 食品グループで選択し、重量順に配列 |～70 RU |100 |
| 食品グループの上位 10 食品を選択 |～10 RU |10 |

> [!NOTE]
> RU 使用量は返されるドキュメントの数により異なります。
> 
> 

この情報があれば、予測される&1; 秒あたりの操作数とクエリ数に基づいて、このアプリケーションに必要な RU 要件を推定できます。

| 操作/クエリ | 1 秒あたりの推定数 | 必要な RU |
| --- | --- | --- |
| ドキュメントの作成 |10 |150 |
| ドキュメントの読み取り |100 |100 |
| 製造者で食品を選択 |25 |175 |
| 食品グループで選択 |10 |700 |
| 上位 10 を選択 |15 |合計&150; |

この例では、平均スループット要件を 1,275 RU/s と想定しています。  100 の位で丸めて、このアプリケーションのコレクションに 1,300 RU/s をプロビジョニングすることになります。

## <a id="RequestRateTooLarge"></a> DocumentDB での予約されたスループット上限の超過
要求ユニットの消費は、1 秒あたりのレートとして評価されることを思い出してください。 コレクションに対してプロビジョニングされた要求単位レートをアプリケーションが超過した場合、そのレートが予約されたレベルを下回るまで、コレクションに対する要求は制限されます。 スロットルが発生すると、サーバーはいち早く RequestRateTooLargeException (HTTP 状態コード 429) で要求を終了させ、x-ms-retry-after-ms ヘッダーを返して、ユーザーが要求の試行を再開できるまでに待機しなければならない時間をミリ秒で示します。

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

.NET クライアント SDK と LINQ クエリ使用していると、ほとんどの場合は、この例外に対処する必要がありません。なぜなら、最新バージョンの .NET クライアント SDK は暗黙的にこの応答を取得し、サーバーが規定した retry-after ヘッダーを考慮して、要求を再試行するからです。 アカウントに複数のクライアントが同時アクセスしている状況でなければ、次回の再試行は成功します。

複数のクライアントが上述の要求レートで累積的に操作を実行している場合は、既定の再試行動作では十分な結果が得られず、クライアントは状態コード 429 で DocumentClientException をアプリケーションにスローします。 こうした場合は、再試行動作とアプリケーションのエラー処理ルーチンのロジックを制御するか、対象コレクションの予約済みスループットを増やすことを検討します。

## <a id="RequestRateTooLargeAPIforMongoDB"></a> MongoDB 用 API での予約されたスループット上限の超過
コレクションに対してプロビジョニングされた要求単位を超過したアプリケーションは、そのレートが予約されたレベルを下回るまで調整されます。 調整が発生すると、バックエンドは、エラー コード *16500* ("*要求が多すぎます*") で機先を制して要求を終了します。 既定では、MongoDB 用 API は、"*要求が多すぎる*" ことを示すエラー コードを返す前に、最大 10 回の再試行を自動的に実行します。 "*要求が多すぎる*" ことを示すエラー コードが多数発生する場合は、アプリケーションのエラー処理ルーチンに再試行動作を追加するか、[コレクションの予約済みスループットを増やす](documentdb-set-throughput.md)ことを検討することができます。

## <a name="next-steps"></a>次のステップ
Azure DocumentDB データベースの予約済みスループットの詳細については、以下のリソースを参照してください。

* [DocumentDB の料金](https://azure.microsoft.com/pricing/details/documentdb/)
* [DocumentDB のデータのモデル化](documentdb-modeling-data.md)
* [DocumentDB のパフォーマンス レベル](documentdb-partition-data.md)

DocumentDB の詳細については、Azure DocumentDB に関する [ドキュメント](https://azure.microsoft.com/documentation/services/documentdb/)を参照してください。 

DocumentDB に関するスケールとパフォーマンスのテストを始めるには、「 [Azure DocumentDB のパフォーマンスとスケールのテスト](documentdb-performance-testing.md)」をご覧ください。

[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
[6]: ./media/documentdb-request-units/api-for-mongodb-metrics.png

