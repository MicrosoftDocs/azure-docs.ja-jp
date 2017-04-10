---
title: "Azure DocumentDB のデータのモデル化 | Microsoft Docs"
description: "DocumentDB (NoSQL ドキュメント データベース) のデータのモデル化について説明します。"
keywords: "データのモデル化"
services: documentdb
author: arramac
manager: jhubbard
editor: mimig1
documentationcenter: 
ms.assetid: 69521eb9-590b-403c-9b36-98253a4c88b5
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 09f42bae67f794f12c7c37cd25c25f4c991fe893
ms.openlocfilehash: 93d0d7276e4ff426e87bdc3dadd736de8d6525fb


---
# <a name="modeling-data-in-documentdb"></a>DocumentDB のデータのモデル化
Azure DocumentDB のようなスキーマのないデータベースでは、データ モデルに対する変更を受け入れることはとても簡単ですが、データについて十分検討する必要があります。 

データをどのように格納するか。 アプリケーションがどのようにデータを取得してクエリを実行するか。 アプリケーションの負荷は読み取りと書き込みのどちらが高いか。 

この記事を読むと、次の質問に回答できるようになります。

* ドキュメント データベース内のドキュメントについてどのように考えるか。
* データのモデル化とは何か、なぜ考慮する必要があるか。 
* データのモデル化は、ドキュメント データベースとリレーショナル データベースでどのように異なるか。
* 非リレーショナル データベース内のデータのリレーションシップをどのように表現するか。
* いつデータを埋め込み、いつデータにリンクするか。

## <a name="embedding-data"></a>データの埋め込み
DocumentDB などのドキュメント ストア内のデータのモデル化を開始する際、JSON で表現された **自己完結型ドキュメント** としてエンティティを扱ってみてください。

さらに詳しく説明する前に、少し戻ってリレーショナル データベースにおけるモデル化のしくみを見てみましょう。これは、多くのユーザーが慣れ親しんでいるテーマです。 次の例は、ある個人がどのようにリレーショナル データベースに格納されるかを示しています。 

![リレーショナル データベース モデル](./media/documentdb-modeling-data/relational-data-model.png)

リレーショナル データベースを操作する際に、長年教え込まれてきたことは "正規化" です。

データの正規化には通常、個人などのエンティティを取得し、細かいデータ要素に分解する操作が含まれます。 上の例では、ある個人が複数の連絡先詳細レコードや複数の住所レコードを持っているとします。 さらにもう一歩進み、型などの共通フィールドを抽出することで、連絡先詳細を細分化します。 住所も同様で、この場合、各レコードには "*自宅*" や "*会社*" などの型があります。 

データを正規化する際に指標となる前提は、各レコードについて **冗長なデータを格納するのではなく** 、データを参照することです。 この例で、連絡先詳細と住所すべてを含む個人のデータを読み取るには、JOIN を使用して実行時にデータを効率的に集約する必要があります。

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

1 人の個人をその連絡先詳細や住所で更新すると、多数の個別のテーブルへの書き込み操作が必要になります。 

では、同じデータをドキュメント データベース内の自己完結型エンティティとしてモデル化する方法を見ていきましょう。

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
            {"email: "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ] 
    }

前に示した方法を使用して、この個人に関するすべての情報 (連絡先詳細や住所など) を**埋め込んだ**個人レコードを**非正規化して**、1 つの JSON ドキュメントにしました。
また、固定スキーマに限定されているわけではないので、まったく別の形で連絡先詳細を含めるような柔軟性もあります。 

データベースから個人レコード全体を取得するのは、1 つのコレクションと 1 つのドキュメントに対する 1 回の読み取り操作です。 連絡先詳細や住所で個人レコードを更新することも、1 つのドキュメントに対する 1 回の書き込み操作です。

データを非正規化することにより、アプリケーションが一般的な操作を完了するために発行する必要があるクエリや更新の数は少なくなります。 

### <a name="when-to-embed"></a>埋め込みをする場合
一般に、埋め込みデータ モデルを使用するのは次のような場合です。

* エンティティ間に **contains** リレーションシップがある場合。
* エンティティ間に **one-to-few** リレーションシップがある場合。
* **頻繁には変更されない**埋め込みデータがある場合。
* **際限なく**増大はしない埋め込みデータがある場合。
* ドキュメント内のデータに **不可欠な** 埋め込みデータがある場合。

> [!NOTE]
> 通常、非正規化データ モデルでは **読み取り** パフォーマンスが向上します。
> 
> 

### <a name="when-not-to-embed"></a>埋め込みをしない場合
ドキュメント データベースでよく使われる方法は、すべてを非正規化してすべてのデータを 1 つのドキュメントに埋め込むことですが、この方法でうまくいかない場合もあります。

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

これは、典型的なブログや CMS システムをモデル化しようとしたときに、コメントが埋め込まれた投稿エンティティがどのようになるかを示したものです。 この例の問題点は、コメントの配列が **無制限である**、つまり、1 つの投稿に対するコメントの数に実質的な制限がないということです。 これは、ドキュメントのサイズが著しく大きくなると、問題になります。

ドキュメントのサイズが増大すると、ネットワーク経由でデータを送信する機能は、ドキュメントの読み取りや更新と同様に、大きな影響を受けます。

このような場合は、次のモデルを検討することをお勧めします。

    Post document:
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

    Comment documents:
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

このモデルでは、投稿自体に最新の 3 つのコメントが埋め込まれており、この場合、配列の上限は固定されています。 その他のコメントは 100 個ずつグループ化され、個別のドキュメントに格納されます。 グループ化するコメントのひとまとまりを 100 に指定したのは、この架空のアプリケーションでユーザーが一度に読み込むことができるコメントの数が 100 個になっているためです。  

また、データの埋め込みが推奨されない事例として、埋め込みデータがドキュメント間で頻繁に使用され、変更される場合があります。 

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

## <a name="a-idreferareferencing-data"></a><a id="Refer"></a>データの参照
このように、データの埋め込みは多くの場合うまく機能しますが、データを非正規化すると、その効果よりも問題の方が多くなるシナリオがあることもまた事実です。 その場合は、どうすればよいでしょうか。 

エンティティ間のリレーションシップを作成できる場所は、リレーショナル データベースだけではありません。 ドキュメント データベース内で、あるドキュメント内の情報と、他のドキュメント内のデータを実際に関連付けることができます。 ここでは、DocumentDB のリレーショナル データベースや他のドキュメント データベースにより適したシステムを構築しようというわけではなく、シンプルなリレーションシップが適切であり、非常に役立ちます。 

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
> 
> 

### <a name="what-about-foreign-keys"></a>外部キー
現在は制約の概念がないため、外部キーかどうかは別にして、ドキュメント間のリレーションシップがドキュメント内にあったとしても、実際には "弱いリンク" であり、データベース自体によって検証されることはありません。 ドキュメントが参照しているデータが実際に存在していることを確認したい場合は、アプリケーション内で確認するか、または DocumentDB 上でサーバー側トリガーかストアド プロシージャを使用して確認する必要があります。

### <a name="when-to-reference"></a>参照を使用する場合
一般に、次のような場合に正規化されたデータ モデルを使用します。

* **一対多** リレーションシップを表している。
* **多対多** リレーションシップを表している。
* 関連するデータが **頻繁に変更される**。
* 参照されるデータに **制限がない**可能性がある。

> [!NOTE]
> 通常は、正規化により **書き込み** パフォーマンスが向上します。
> 
> 

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
    {"id": "1", "name": "DocumentDB 101" }
    {"id": "2", "name": "DocumentDB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure DocumentDB" }
    ...
    {"id": "1000", "name": "Deep Dive in to DocumentDB" }

発行元あたりの書籍数が少なく、増加率が限定的な場合は、書籍の参照を発行元ドキュメント内に格納することが有効な場合もあります。 ただし、発行元あたりの書籍数に制限がない場合は、このデータ モデルは上の発行元ドキュメントに示すような、拡大し続ける可変配列になります。 

位置を少し入れ替えることにより、モデルは、同じデータを表しながらも、このように大きい可変コレクションを回避できるようになります。

    Publisher document: 
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents: 
    {"id": "1","name": "DocumentDB 101", "pub-id": "mspress"}
    {"id": "2","name": "DocumentDB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure DocumentDB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive in to DocumentDB", "pub-id": "mspress"}

上の例では、発行元ドキュメントで無制限のコレクションを使用していません。 代わりに、各書籍ドキュメントに発行元への参照が含まれているだけです。

### <a name="how-do-i-model-manymany-relationships"></a>多対多のリレーションシップのモデル化
リレーショナル データベースでは、 *多対多* のリレーションシップは多くの場合、単に他のテーブルからのレコードを結合する結合テーブルを使用してモデル化されます。 

![テーブルの結合](./media/documentdb-modeling-data/join-table.png)

ドキュメントを使用して同じ内容を複製し、次の例のようなデータ モデルを作成したくなるかもしれません。

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "DocumentDB 101" }
    {"id": "b2", "name": "DocumentDB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure DocumentDB" }
    {"id": "b5", "name": "Deep Dive in to DocumentDB" }

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
    {"id": "b1", "name": "DocumentDB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "DocumentDB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure DocumentDB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to DocumentDB", "authors": ["a2"]}

ここで、作者がわかっていれば、その著作はすぐにわかり、逆に書籍ドキュメントを読み込んでいれば、作者の ID がわかります。 これにより、結合テーブルに対する中間クエリは省略され、アプリケーションで行う必要があるサーバー ラウンド トリップの数が減少します。 

## <a name="a-idwrapupahybrid-data-models"></a><a id="WrapUp"></a>ハイブリッド データ モデル
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
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "DocumentDB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "DocumentDB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

ここでは、(主に) 埋め込みモデルに従います。この埋め込みモデルでは、他のエンティティのデータが最上位のドキュメントに埋め込まれていますが、他のデータは参照されています。 

書籍ドキュメントを見ると、作者の配列にいくつか興味深いフィールドがあります。 もう一度作者ドキュメントを参照するために使用する *id* フィールドがあります。これは正規化モデルの標準的な手法ですが、*name* フィールドと *thumbnailUrl* フィールドも使用します。 *id* を引き続き使用し、アプリケーションでは、必要な追加情報があればそれぞれの作者ドキュメントから "リンク" を使用して取得するようにしていますが、このアプリケーションでは、表示される各書籍と共に作者名とサムネイル写真も表示されるため、作者の**一部の**データを非正規化することにより、リスト内の書籍ごとのサーバー ラウンド トリップを減らすことができます。

確かに、作者名が変更された場合や、作者が写真を更新したい場合は、これまでに発行されたすべての書籍に対して更新を行う必要がありましたが、このアプリケーションでは、作者名はそれほど頻繁に変更されないという前提に基づいて、この設計が仕様を満たしていると判断しています。  

この例には **事前に計算された集計** の値があり、読み取り操作の処理時間を削減しています。 この例の作者ドキュメントに埋め込まれたデータの一部は、実行時に計算されるデータです。 新しい書籍が発行されるたびに、書籍ドキュメントが作成され、 **さらに** その作者に対応する書籍ドキュメントの数に基づいて計算された値が countOfBooks フィールドに設定されます。 この最適化は、読み取りの負荷が高く、読み取りを最適化するために書き込み時に計算を実行する余裕のあるシステムに適しています。

モデルに事前計算フィールドを含める機能は、DocumentDB が **マルチドキュメント トランザクション**をサポートしていることにより実現されています。 多くの NoSQL ストアでは、ドキュメント間のトランザクションを実行できないため、この制限によって "常にすべてを埋め込む" などの設計における決定が提唱されています。 DocumentDB では、ACID トランザクション内で書籍の挿入や作者の更新をすべて実行する、サーバー側トリガー、またはストアド プロシージャを使用できます。 これで、データの整合性を維持するためだけに、1 つのドキュメントにすべてを埋め込む必要は **なくなります** 。

## <a name="a-namenextstepsanext-steps"></a><a name="NextSteps"></a>次のステップ
この記事における最大の収穫は、スキーマのない状況でのデータのモデル化は以前として重要であると理解できたことです。 

データの要素を画面上に表現する方法が 1 つではないように、データをモデル化する方法も 1 つではありません。 使用するアプリケーションを理解し、アプリケーションがどのようにデータを作成、使用、処理するかを理解することが必要です。 次に、ここで示したガイドラインのいくつかを適用することにより、アプリケーションの当面のニーズに対応するモデルの作成を開始することができます。 アプリケーションの変更が必要な場合にも、スキーマのないデータベースの柔軟性を活用して、その変更を受け入れ、データ モデルを容易に進化させることができます。 

Azure DocumentDB の詳細については、サービスの [ドキュメント](https://azure.microsoft.com/documentation/services/documentdb/) のページを参照してください。 

Azure DocumentDB でのインデックスのチューニングの詳細については、 [インデックス作成ポリシー](documentdb-indexing-policies.md)に関する記事を参照してください。

複数のパーティション間でデータをシャーディングする方法については、「 [DocumentDB でのデータのパーティション分割](documentdb-partition-data.md)」を参照してください。 

最後に、マルチテナント アプリケーションのデータのモデル化とシャーディングのガイダンスについては、 [Azure DocumentDB でのマルチテナント アプリケーションの拡張](http://blogs.msdn.com/b/documentdb/archive/2014/12/03/scaling-a-multi-tenant-application-with-azure-documentdb.aspx)に関するページを参照してください。




<!--HONumber=Feb17_HO3-->


