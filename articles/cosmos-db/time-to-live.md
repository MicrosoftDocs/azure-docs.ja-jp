---
title: Azure Cosmos DB で TTL を使ってデータを期限切れにする | Microsoft Docs
description: Microsoft Azure Cosmos DB では TTL を使って、一定期間経過後にシステムからドキュメントを自動的に消去することができます。
services: cosmos-db
keywords: 有効期限
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: e1b11d637eec54d43c9f1212936d94b2d7396c97
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615123"
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>TTL (Time to Live) を使って Azure Cosmos DB コレクションのデータの有効期限が自動的に切れるようにする
アプリケーションで膨大なデータを生成し、格納することができます。 このデータの一部 (コンピューターによって生成されるイベント データ、ログ、およびユーザー セッション情報など) は、一定期間でのみ有効です。 アプリケーションで必要以上のデータがある場合は、そのデータを消去し、アプリケーションでのストレージの必要性を減らすのが安全です。

Microsoft Azure Cosmos DB では TTL を使って、一定期間経過後にデータベースからドキュメントを自動的に消去することができます。 既定の TTL はコレクション レベルで設定し、ドキュメントごとにオーバーライドすることができます。 TTL をコレクションの既定値として、またはドキュメント レベルで設定すると、Cosmos DB は、最後に変更されてから一定期間 (秒単位) 経過後に存在するドキュメントを自動的に削除します。

Azure Cosmos DB の TTL では、ドキュメントの最終変更時に対してオフセットを使用します。 そのために、すべてのドキュメントに存在する `_ts` フィールドを使用します。 _ts フィールドは、日付と時刻を表す UNIX 形式のエポック タイムスタンプです。 この `_ts` フィールドは、ドキュメントが変更されるたびに更新されます。 

## <a name="ttl-behavior"></a>TTL の動作
TTL 機能は、コレクション レベルとドキュメント レベルの 2 つのレベルで TTL プロパティによって制御されます。 値は秒単位で設定され、ドキュメントが最後に変更された `_ts` との差分として扱われます。

1. コレクションの DefaultTTL
   
   * 設定されていない (または null に設定されている) 場合、ドキュメントは自動的に削除されません。
   * 設定されており、値が "-1" (無限) に設定されている場合、ドキュメントは既定で期限切れになりません。
   * 設定されており、値がいずれかの数 ("n") に設定されている場合、ドキュメントは最後に変更されてから "n" 秒後に期限切れになります。
2. ドキュメントの TTL: 
   
   * プロパティは、親コレクションの DefaultTTL が存在する場合にのみ適用されます。
   * 親コレクションの DefaultTTL 値をオーバーライドします。

ドキュメントの有効期限が切れるとすぐに (`ttl` + `_ts` が現在のサーバー時間以下になった場合)、ドキュメントは "有効期限切れ" とマークされます。 それ以降はドキュメントを操作することができず、実行されるどのクエリの結果からも除外されます。 システムではドキュメントが物理的に削除され、バックグラウンドでは後で状況に応じて削除されます。 その場合、コレクション予算から [要求ユニット (RU)](request-units.md) は使用されません。

上記のロジックを以下のマトリックスで表すことができます。

|  | コレクションに DefaultTTL が存在しない/設定されていない | コレクションで、DefaultTTL =  -1 | コレクションで、DefaultTTL = "n" |
| --- |:--- |:--- |:--- |
| ドキュメントに TTL が存在しない |ドキュメントとコレクションの両方に TTL の概念がないために、ドキュメント レベルでオーバーライドされません。 |このコレクションのドキュメントは期限切れになりません。 |n 期間が経過すると、このコレクションのドキュメントは期限切れになります。 |
| ドキュメントの TTL が -1 である |ドキュメントでオーバーライドできる DefaultTTL プロパティがコレクションで定義されていないため、ドキュメント レベルでオーバーライドされません。 ドキュメントの TTL はシステムで解釈されません。 |このコレクションのドキュメントは期限切れになりません。 |このコレクションの TTL が -1 のドキュメントが期限切れになることはありません。 その他のすべてのドキュメントは、"n" 期間が経過すると期限切れになります。 |
| ドキュメントに対する TTL が n である |ドキュメント レベルでオーバーライドされません。 ドキュメントの TTL はシステムで解釈されません。 |TTL が n のドキュメントは n (秒単位) 期間が経過すると期限切れになります。 その他のドキュメントは -1 の期間を継承し、期限切れになることはありません。 |TTL が n のドキュメントは n (秒単位) 期間が経過すると期限切れになります。 その他のドキュメントは、コレクションから "n" 期間を継承します。 |

## <a name="configuring-ttl"></a>TTL の構成
既定では、すべての Cosmos DB コレクションおよびすべてのドキュメントの TTL が無効になります。 TTL はプログラミングで設定することも、Azure Portal においてコレクションの **[設定]** セクションで設定することもできます。Azure Potalからは、次のステップでTTLを構成できます。

1. [Azure Potal](https://portal.azure.com/) にサインインし、Azure Cosmos DBアカウントを選択します。  

2. TTLを設定したいコレクションを選択し、**Scale & Settings (設定)** を開きます。TTL (Time to Live) がデフォルトで **off** になっています。それを **on(no default)** または **on** に変更します。  

   **off** - ドキュメントは自動的に削除されません。  
   **on (no default)** - TTLの値を "-1" (無限) に設定します。これにより、ドキュメントは既定で有効期限切れになりません。  
   **on** - ドキュメントは、最後に変更されてから"n"秒後に有効期限が切れます。  

   ![Set time to live](./media/time-to-live/set-ttl-in-portal.png)

## <a name="enabling-ttl"></a>TTL の有効化
コレクションまたはコレクション内のドキュメントに対して TTL を有効にするには、コレクションの DefaultTTL プロパティを -1、またはゼロ以外の正数に設定する必要があります。 DefaultTTL を -1 に設定すると、既定でコレクションのすべてのドキュメントは永続的に存続しますが、Cosmos DB サービスはこの既定値をオーバーライドしたドキュメントに対するこのコレクションを監視する必要があります。

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>コレクションに対する既定の TTL の構成
コレクション レベルで既定の TTL を構成することができます。 コレクションに対して TTL を設定するには、ドキュメントの最終変更タイムスタンプ (`_ts`) 後にコレクションのすべてのドキュメントが期限切れになるように、期間 (秒単位) を示すゼロ以外の正数を指定する必要があります。 または、コレクションに挿入されたすべてのドキュメントが既定で無期限に存続することを意味する -1 に既定値を設定することができます。

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>ドキュメントに対する TTL の設定
コレクションに対する既定の TTL の設定に加え、ドキュメント レベルでは特定の TTL を設定することができます。 これを行うと、コレクションの既定値がオーバーライドされます。

* ドキュメントに対して TTL を設定するには、ドキュメントの最終変更タイムスタンプ (`_ts`) 後にドキュメントが期限切れになるように、期間 (秒単位) を示すゼロ以外の正数を指定する必要があります。
* ドキュメントに TTL フィールドがない場合は、コレクションの既定値が適用されます。
* TTL がコレクション レベルで無効になっている場合、TTL がコレクションで再び有効になるまで、ドキュメントの TTL フィールドは無視されます。

次に示すのは、ドキュメントに対して TTL の有効期限を設定するスニペットです。

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>既存のドキュメントに対する TTL の延長
ドキュメントで書き込み操作を実行して、ドキュメントの TTL をリセットすることができます。 これを行うと、`_ts` が現在の時刻に設定され、`ttl` で設定されたドキュメントの有効期限までのカウントダウンが再び始まります。 ドキュメントの `ttl` を変更する場合は、他の設定可能なフィールドの場合と同じようにフィールドを更新できます。

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="removing-ttl-from-a-document"></a>ドキュメントからの TTL の削除
TTL が設定されているドキュメントを期限切れにする必要がなくなった場合には、そのドキュメントを取得し、TTL フィールドを削除して、サーバーのドキュメントを置き換えることができます。 ドキュメントから TTL フィールドが削除されると、コレクションの既定値が適用されます。 ドキュメントが期限切れにならないようにし、コレクションから継承されないようにするには、TTL 値を -1 に設定する必要があります。

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="disabling-ttl"></a>TTL の無効化
コレクションの TTL を完全に無効にし、バックグラウンド プロセスで期限切れのドキュメントが検索されないようにするには、コレクションの DefaultTTL プロパティを削除する必要があります。 このプロパティの削除は、-1 に設定することとは異なります。 -1 に設定すると、コレクションに追加される新しいドキュメントが永続的に存続しますが、コレクションの特定のドキュメントでこれをオーバーライドすることができます。 コレクションからこのプロパティを完全に削除すると、以前の既定値を明示的にオーバーライドしたドキュメントがある場合でも、ドキュメントは期限切れになりません。

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>TTL とインデックスの相互作用
コレクションで TTL の設定が追加される、または変更されると、基になるインデックスが変更されます。 TTL の値がオフからオンに変更されると、コレクションのインデックスが再作成されます。 インデックス作成モードに一貫性がある場合に、インデックス作成ポリシーが変更されると、ユーザーはインデックスの変更に気付きません。 インデックス作成モードが遅延に設定されている場合、インデックスは常にキャッチ アップし、TTL の値が変更されると、インデックスが一から再作成されます。 インデックス作成モードが遅延に設定されている場合に、TTL の値が変更されると、インデックスの再構築中に実行されたクエリから完全な結果または正しい結果が返されません。

正確なデータを返す必要があるときには、インデックス作成モードが遅延に設定されている場合に、TTL の値を変更しないでください。 一貫性のあるクエリ結果を得るには、一貫性のあるインデックスを選択するのが理想的です。 

## <a name="faq"></a>FAQ
**TTL にコストはかかりますか?**

ドキュメントに TTL を設定する場合、追加のコストはかかりません。

**TTL が切れてからドキュメントが削除されるまでどれくらいかかりますか?**

TTL が切れるとすぐにドキュメントは期限切れになり、CRUD またはクエリ API を通じてアクセスできなくなります。 

**ドキュメントの TTL は RU 料金に影響しますか?**

いいえ。Cosmos DB の TTL を通じて期限切れになったドキュメントを削除しても、RU 料金への影響はありません。

**TTL 機能はドキュメント全体のみに適用されるのですか? それとも、個々のドキュメントのプロパティ値で有効期限を設定することはできますか?**

TTL はドキュメント全体に適用されます。 ドキュメントの一部のみを期限切れにしたい場合は、メイン ドキュメントの当該部分を別個の "リンク先" ドキュメントに抽出してから、その抽出したドキュメントで TTL を使用することをお勧めします。

**TTL 機能には特定のインデックス作成要件がありますか?**

はい。 コレクションでは、Consistent または Lazy に[インデックス作成ポリシーを設定](indexing-policies.md)する必要があります。 インデックス作成が None に設定されているコレクションに対して DefaultTTL を設定しようとすると、エラーになります。DefaultTTL が既に設定されているコレクションのインデックス作成を無効にしようとする場合も同様です。

## <a name="next-steps"></a>次の手順
Azure Cosmos DB の詳細については、サービスの "[*ドキュメント*](https://azure.microsoft.com/documentation/services/cosmos-db/)" のページを参照してください。

