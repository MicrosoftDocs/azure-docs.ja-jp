---
title: Azure Cosmos DB のデータのモデル化
titleSuffix: Azure Cosmos DB
description: NoSQL データベースでのデータのモデル化、リレーショナル データベースのデータとドキュメント データベースのデータのモデル化の違いについて説明します。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: 47d519523c7ffd1c0b6329d6b4eb12b052466b35
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657374"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Azure Cosmos DB のデータ モデリング

Azure Cosmos DB のようなスキーマのないデータベースでは、非構造化データと半構造化データの格納とクエリを非常に簡単に行うことができますが、パフォーマンスとスケーラビリティ、そしてコストを最小限に抑えるという観点からサービスを最大限に活用するには、時間をとってデータ モデルについて検討する必要があります。

データをどのように格納するか。 アプリケーションがどのようにデータを取得してクエリを実行するか。 ご使用のアプリケーションの負荷は読み取りと書き込みのどちらが高いか。

この記事を読むと、次の質問に回答できるようになります。

* データのモデル化とは何か、なぜ考慮する必要があるか。
* データのモデル化は、Azure Cosmos DB とリレーショナル データベースでどのように異なるか。
* 非リレーショナル データベース内のデータのリレーションシップをどのように表現するか。
* いつデータを埋め込み、いつデータにリンクするか。

## <a name="embedding-data"></a>データの埋め込み

Azure Cosmos DB のデータのモデル化を開始する際、JSON ドキュメントとして表現された**自己完結型項目**としてエンティティを扱ってみてください。

比較のために、まずはリレーショナル データベースのデータをモデル化する方法を見てみましょう。 次の例は、ある個人がどのようにリレーショナル データベースに格納されるかを示しています。

![リレーショナル データベース モデル](./media/sql-api-modeling-data/relational-data-model.png)

リレーショナル データベースを使用する場合、データをすべて正規化するという方針を採ります。 データの正規化には通常、個人などのエンティティを取得し、個別のコンポーネントに分解する操作が含まれます。 上の例では、1 人に対して複数の連絡先詳細レコードと複数の住所レコードが存在する場合があります。 連絡先詳細は、種類などの共通フィールドを抽出することでさらに細分化できます。 同じことが住所にも当てはまり、各レコードの種類として*自宅*や*会社*が考えられます。

データを正規化する際に指標となる前提は、各レコードについて **冗長なデータを格納するのではなく** 、データを参照することです。 この例で、すべての連絡先詳細と住所を含む個人のデータを読み取るには、JOIN を使用して実行時にデータを効率的に作成し直す (非正規化する) 必要があります。

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

1 人の個人をその連絡先詳細や住所で更新すると、多数の個別のテーブルへの書き込み操作が必要になります。

では、同じデータを Azure Cosmos DB 内の自己完結型エンティティとしてモデル化する方法を見ていきましょう。

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

前に示した方法を使用して、この個人に関するすべての情報 (連絡先詳細や住所など) を**埋め込んで**個人レコードを**非正規化**して、"*1 つの JSON*" ドキュメントにしました。
また、固定スキーマに限定されているわけではないので、まったく別の形で連絡先詳細を含めるような柔軟性もあります。

データベースから個人レコード全体を取得するのは、1 つのコンテナーと 1 つの項目に対する **1 回の読み取り操作**です。 連絡先詳細や住所で個人レコードを更新することも、1 つの項目に対する **1 回の書き込み操作**です。

データを非正規化することにより、アプリケーションが一般的な操作を完了するために発行する必要があるクエリや更新の数は少なくなります。

### <a name="when-to-embed"></a>埋め込みをする場合

一般に、埋め込みデータ モデルを使用するのは次のような場合です。

* エンティティ間に **包含された** リレーションシップがある場合。
* エンティティ間に **one-to-few** リレーションシップがある場合。
* **頻繁には変更されない**埋め込みデータがある場合。
* **際限なく**増大はしない埋め込みデータがある場合。
* **頻繁にはまとめてクエリが実行されない**埋め込みデータがある場合。

> [!NOTE]
> 通常、非正規化データ モデルでは **読み取り** パフォーマンスが向上します。

### <a name="when-not-to-embed"></a>埋め込みをしない場合

Azure Cosmos DB でよく使われる方法は、すべてを非正規化してすべてのデータを 1 つの項目に埋め込むことですが、この方法でうまくいかない場合もあります。

この JSON スニペットを見てください。

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

これは、典型的なブログや CMS システムをモデル化しようとしたときに、コメントが埋め込まれた投稿エンティティがどのようになるかを示したものです。 この例の問題点は、コメントの配列が **無制限である**、つまり、1 つの投稿に対するコメントの数に実質的な制限がないということです。 これは、項目のサイズが際限なく大きくなる可能性があるため、問題となる場合があります。

項目のサイズが増大すると、ネットワーク経由でデータを送信する機能は、項目の読み取りや更新と同様に、大きな影響を受けます。

このような場合は、次のデータ モデルを検討することをお勧めします。

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

このモデルでは、投稿コンテナー (一連の属性が固定されている配列) に最新の 3 つのコメントが埋め込まれています。 その他のコメントは 100 個ずつグループ化され、個別の項目として格納されます。 グループ化するコメントのひとまとまりを 100 に指定したのは、この架空のアプリケーションでユーザーが一度に読み込むことができるコメントの数が 100 個になっているためです。  

また、データの埋め込みが推奨されない事例として、埋め込みデータが項目間で頻繁に使用され、変更される場合があります。

この JSON スニペットを見てください。

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

これは、ある個人の株式ポートフォリオを表したものです。 各ポートフォリオ ドキュメントに株式情報が埋め込まれています。 株式取引アプリケーションのように関連データが頻繁に変更される環境では、埋め込みデータが頻繁に変更されるので、株が取引されるたびに各ポートフォリオ ドキュメントを絶えず更新しなければならないことになります。

株式 *zaza* は 1 日に数百回取引され、数千人ものユーザーのポートフォリオに *zaza* が含まれている可能性があります。 上のようなデータ モデルでは、毎日、数千ものポートフォリオ ドキュメントを何回も更新する必要があり、システムの拡張が不十分になる可能性もあります。

## <a name="referencing-data"></a>データの参照

データの埋め込みは多くの場合うまく機能しますが、データを非正規化すると、その効果よりも問題の方が多くなるシナリオがあります。 その場合は、どうすればよいでしょうか。

エンティティ間のリレーションシップを作成できる場所は、リレーショナル データベースだけではありません。 ドキュメント データベース内で、あるドキュメント内の情報と、他のドキュメント内のデータを関連付けることができます。 Azure Cosmos DB のリレーショナル データベースや他のドキュメント データベースにより適したシステムの構築は推奨しません。シンプルなリレーションシップが適切であり役に立ちます。

以下の JSON では、前の株式ポートフォリオの例を使用していますが、ここでは株式に関する項目を埋め込むのではなく、ポートフォリオ上で参照しています。 これにより、株式の項目が終日頻繁に変更された場合でも、更新する必要があるのは 1 つの株式ドキュメントのみです。

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

ただし、この方法にも欠点があります。アプリケーションで個人のポートフォリオを表示する際に、保有する各株式の情報を表示する必要がある場合、何度もデータベースにアクセスして、各株式ドキュメントの情報を読み込むことが必要になります。 ここで、終日頻繁に発生する書き込み操作の効率を向上させ、この特定のシステムにはあまり影響がないと考えられる読み取り操作の効率を下げるという意思決定を行いました。

> [!NOTE]
> 正規化されたデータ モデルは、サーバーに **より多くのラウンド トリップを要求する可能性があります** 。

### <a name="what-about-foreign-keys"></a>外部キー

現在は制約の概念がないため、外部キーかどうかは別にして、ドキュメント間のリレーションシップがドキュメント内にあったとしても、実際には "弱いリンク" であり、データベース自体によって検証されることはありません。 ドキュメントが参照しているデータが実際に存在していることを確認したい場合は、アプリケーション内で確認するか、または Azure Cosmos DB 上でサーバー側トリガーかストアド プロシージャを使用して確認する必要があります。

### <a name="when-to-reference"></a>参照を使用する場合

一般に、次のような場合に正規化されたデータ モデルを使用します。

* **一対多** リレーションシップを表している。
* **多対多** リレーションシップを表している。
* 関連するデータが **頻繁に変更される**。
* 参照されるデータに **制限がない**可能性がある。

> [!NOTE]
> 通常は、正規化により **書き込み** パフォーマンスが向上します。

### <a name="where-do-i-put-the-relationship"></a>リレーションシップの場所

リレーションシップの拡大により、どのドキュメントに参照を格納するかを決定しやすくなります。

発行元と書籍をモデル化する、以下の JSON を見てみましょう。

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

発行元あたりの書籍数が少なく、増加率が限定的な場合は、書籍の参照を発行元ドキュメント内に格納することが有効な場合もあります。 ただし、発行元あたりの書籍数に制限がない場合は、このデータ モデルは上の発行元ドキュメントに示すような、拡大し続ける可変配列になります。

位置を少し入れ替えることにより、モデルは、同じデータを表しながらも、このように大きい可変コレクションを回避できるようになります。

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

上の例では、発行元ドキュメントで無制限のコレクションを使用していません。 代わりに、各書籍ドキュメントに発行元への参照が含まれているだけです。

### <a name="how-do-i-model-manymany-relationships"></a>多対多のリレーションシップのモデル化

リレーショナル データベースでは、 *多対多* のリレーションシップは多くの場合、単に他のテーブルからのレコードを結合する結合テーブルを使用してモデル化されます。

![テーブルの結合](./media/sql-api-modeling-data/join-table.png)

ドキュメントを使用して同じ内容を複製し、次の例のようなデータ モデルを作成したくなるかもしれません。

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

これは、機能はします。 ただし、作者とその著作、または書籍とその作者の読み込みでは、常に、データベースに対して少なくとも 2 つの追加のクエリが必要になります。 1 つは結合するドキュメントに対するクエリ、もう 1 つは結合される実際のドキュメントを取得するクエリです。

この結合テーブルで行われる処理が 2 つのデータ要素の結合だけであれば、これを削除してはどうでしょうか。
次の例を検討してみてください。

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

ここで、作者がわかっていれば、その作者の著作はすぐにわかります。そして逆に、書籍ドキュメントを読み込んでいれば、作者の ID がわかります。 これにより、結合テーブルに対する中間クエリは省略され、アプリケーションで行う必要があるサーバー ラウンド トリップの数が減少します。

## <a name="hybrid-data-models"></a>ハイブリッド データ モデル

ここまで、データの埋め込み (非正規化) と参照 (正規化) を見てきましたが、それぞれに長所と短所がありました。

必ずしもどちらか一方にする必要はなく、両方を多少併用してもかまいません。

アプリケーション固有の使用パターンと負荷に基づき、埋め込みデータと参照データの併用が理にかなっていて、結果として、適切なパフォーマンス レベルを維持しながらアプリケーション ロジックがシンプルになり、サーバー ラウンド トリップが少なくなる場合もあります。

以下の JSON を検討してみてください。

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

ここでは、(主に) 埋め込みモデルに従います。この埋め込みモデルでは、他のエンティティのデータが最上位のドキュメントに埋め込まれていますが、他のデータは参照されています。

書籍ドキュメントを見ると、作者の配列にいくつか興味深いフィールドがあります。 作者ドキュメントを戻って参照する (正規化モデルでの標準的な手法) ために使用するフィールドである `id` フィールドがありますが、`name` と `thumbnailUrl` もあります。 `id` の使用を続け、アプリケーションに、 "リンク" を使用して必要な追加情報をそれぞれの作者ドキュメントから取得させることはできますが、アプリケーションは、表示される書籍ごとに作者の名前とサムネイル写真を表示するので、作者の**一部の**データを非正規化することにより、一覧表にある書籍ごとにサーバーとやりとりしないで済みます。

確かに作者の名前が変更された場合や、作者の写真を更新したい場合は、それまでに出版されたすべての書籍を更新する必要がありますが、このアプリケーションでは、作者の名前が頻繁に変更されることはないという前提に基づいて、この設計決定を容認可能としています。  

この例には**事前に計算された集計**の値があり、読み取り操作の処理時間を削減しています。 この例の作者ドキュメントに埋め込まれたデータの一部は、実行時に計算されるデータです。 新しい書籍が発行されるたびに、書籍ドキュメントが作成され、 **さらに** その作者に対応する書籍ドキュメントの数に基づいて計算された値が countOfBooks フィールドに設定されます。 この最適化は、読み取りの負荷が高く、読み取りを最適化するために書き込み時に計算を実行する余裕のあるシステムに適しています。

モデルに事前計算フィールドを含める機能は、Azure Cosmos DB が**マルチドキュメント トランザクション**をサポートしていることにより実現されています。 多くの NoSQL ストアでは、ドキュメント間のトランザクションを実行できないため、この制限によって "常にすべてを埋め込む" などの設計における決定が提唱されています。 Azure Cosmos DB では、ACID トランザクション内で書籍の挿入や作者の更新をすべて実行する、サーバー側トリガー、またはストアド プロシージャを使用できます。 これで、データの整合性を維持するためだけに、1 つのドキュメントにすべてを埋め込む必要は**なくなります**。

## <a name="distinguishing-between-different-document-types"></a>さまざまなドキュメントの種類の区別

一部のシナリオで、さまざまなドキュメントの種類を同じコレクション内に混在させたい場合があります。複数の関連ドキュメントを同じ [パーティション](partitioning-overview.md) に含めたい場合などがこれに当たります。 たとえば、書籍とブック レビューを同じコレクションに入れて、`bookId` でパーティション分割することができます。 そのような場合は、通常、それらを差別化するために種類を識別するフィールドをドキュメントに追加することができます。

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>次の手順

この記事における最大の収穫は、スキーマのない状況でのデータのモデル化は依然として重要であると理解できたことです。

データの要素を画面上に表現する方法が 1 つではないように、データをモデル化する方法も 1 つではありません。 使用するアプリケーションを理解し、アプリケーションがどのようにデータを作成、使用、処理するかを理解することが必要です。 次に、ここで示したガイドラインのいくつかを適用することにより、アプリケーションの当面のニーズに対応するモデルの作成を開始することができます。 アプリケーションの変更が必要な場合にも、スキーマのないデータベースの柔軟性を活用して、その変更を受け入れ、データ モデルを容易に進化させることができます。

Azure Cosmos DB の詳細については、サービスの[ドキュメント](https://azure.microsoft.com/documentation/services/cosmos-db/)のページを参照してください。

複数のパーティション間でデータをシャーディングする方法については、[Azure Cosmos DB でのデータのパーティション分割](sql-api-partition-data.md)に関するページを参照してください。
