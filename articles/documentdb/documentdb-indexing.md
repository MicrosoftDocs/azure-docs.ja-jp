---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: d59ebef3cda36ba048b844f0cd2326fff66b4aa5
ms.openlocfilehash: 4c7b8965b7fe5de7a8ff839be2b9e555bd8d502c
ms.lasthandoff: 01/21/2017



---
# <a name="automatic-indexing-in-azure-documentdb"></a>Azure DocumentDB での自動インデックス作成
この記事は、2015 年 8 月 31 日から 9 月 4 日の "[41st Internal Conference on Very Large Data Bases](http://www.vldb.org/2015/)" で発表される論文「[Schema-Agnostic Indexing with Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)」(Azure DocumentDB を使用したスキーマに依存しないインデックス作成) からの抜粋です。また、Azure DocumentDB でのインデックス作成のしくみの概要についても説明します。 

この記事を読むと、次の質問に回答できるようになります。

* DocumentDB は JSON ドキュメントからスキーマをどのように推測するか。
* DocumentDB はさまざまなドキュメントにまたがるインデックスをどのようにビルドするか。
* DocumentDB はスケールに応じた自動インデックス作成をどのように実行するか。

## <a name="a-idhowdocumentdbindexingworksa-how-documentdb-indexing-works"></a><a id="HowDocumentDBIndexingWorks"></a> DocumentDB のインデックス作成のしくみ
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) は JSON 用にビルドされた完全にスキーマフリーのデータベース システムです。 スケールに応じたデータのインデックス作成に、スキーマやセカンダリ インデックスの定義を必要としないため、 DocumentDB を使用してアプリケーション データ モデルをすばやく定義し、反復処理できます。 コレクションにドキュメントを追加するたびに、DocumentDB がすべてのドキュメント プロパティにインデックスを自動作成してクエリを利用可能にします。 自動インデックス作成により、スキーマやセカンダリ インデックスを気にせずに、完全に任意のスキーマに属しているドキュメントを格納できます。

データベースとアプリケーション プログラミング モデル間のインピーダンス ミスマッチを排除することを目標に、DocumentDB はシンプルでスキーマ仕様が不要な JSON を利用します。 これにより、ドキュメントの前提条件がなくなり、DocumentDB コレクション内にインスタンス固有の値やスキーマの異なるドキュメントを格納できるようになります。 その他のドキュメント データベースとは異なり DocumentDB のデータベース エンジンは JSON の文法レベルで直接動作するため、ドキュメント スキーマの概念に依存せず、ドキュメントの構造とインスタンス値との間の境界が不明確になります。 このため、スキーマやセカンダリ インデックスを必要とせずにドキュメント インデックスを自動的に作成できます。

DocumentDB のインデックス作成は、JSON 文法によりドキュメントの **ツリー表現**が可能になるという事実を活用しています。 JSON ドキュメントをツリーとして表現するには、ダミーのルート ノードを作成し、その下のドキュメントに含まれる実際のノードに対する親とする必要があります。 JSON ドキュメント内の配列インデックスを含む各ラベルはツリーのノードになります。 次の図には、JSON ドキュメントと対応するツリー表現の例を示しています。

> [!NOTE]
> JSON は自己記述型であり、各ドキュメントにスキーマ (メタデータ) とデータの両方が含まれています。たとえば、`{"locationId": 5, "city": "Moscow"}` では 2 つのプロパティ (`locationId` と `city`) に数値と文字列のプロパティ値があることがわかります。 DocumentDB はドキュメントのスキーマを推論して、ドキュメントの挿入時や置換時にインデックスを作成できます。このときスキーマやセカンダリ インデックスを定義する必要はありません。
> 
> 

**ツリーとしての JSON ドキュメント:**

![ツリーとしてのドキュメント](media/documentdb-indexing/DocumentsAsTrees.png)

たとえば、上の例は次のように説明できます。

* 上の例の JSON プロパティである `{"headquarters": "Belgium"}` プロパティは、パス /headquarters/Belgium に対応しています。
* JSON 配列 `{"exports": [{"city": “Moscow"}` と `{"city": Athens"}]}` は、パス `/exports/[]/city/Moscow` と `/exports/[]/city/Athens` に対応しています。

自動インデックス作成を使用すると、(1) ドキュメント ツリー内のすべてのパスにインデックスが作成されます (開発者が特定のパス パターンを除外するインデックス作成ポリシーを明示的に設定しない場合)。 (2) ドキュメントを DocumentDB コレクションに更新するたびに、インデックスの構造も更新されます (つまり、ノードが追加、削除されます)。 ドキュメントの自動インデックス作成における主な要件の 1 つは、深く入れ子になった構造 (10 レベルなど) を持つドキュメントのインデックス作成とクエリ実行のコストが、主要な値のペアで構成された 1 階層のみのフラットな JSON ドキュメントのコストと同じになることを保証することです。 そのため、正規化されたパス表現は、自動インデックス作成とクエリ サブシステムの両方がビルドされる基礎となります。

スキーマとインスタンスの値をパス上で均等に扱うことは、上図に示す&2; つのドキュメントのパス間のマップを保持するインデックスと、そのパスを含むドキュメント ID も、個々のドキュメントと同様に論理的にツリーとして表現できる点で、重要な意味を持ちます。 DocumentDB はこのしくみを利用して、コレクション内の個々 のドキュメントを表現するすべてのツリーの和集合から構成されたインデックス ツリーをビルドします。 DocumentDB コレクションのインデックスのツリーは、時間の経過と共に新しいドキュメントがコレクションに追加、更新されていくため、増大します。

**ツリーとしての DocumentDB インデックス:**

![ツリーとしてのインデックス](media/documentdb-indexing/IndexAsTree.png)

スキーマフリーであっても、DocumentDB の SQL と JavaScript クエリ言語では、リレーショナルなプロジェクションとフィルター、ドキュメント間の階層型ナビゲーション、空間演算、および完全に JavaScript で記述された UDF の呼び出しが提供されます。 DocumentDB のクエリ ランタイムは、このデータのインデックス ツリー表現に対して直接処理を行えるので、これらのクエリをサポートできます。

既定のインデックス作成ポリシーは、自動的にすべてのドキュメントの全プロパティのインデックスを作成し、一貫性のあるクエリ (つまり、インデックスは、ドキュメントの書き込み時に同期的に更新されます) を提供します。 DocumentDB はスケールに応じたインデックス ツリーへの一貫性のある更新をどのようにサポートするかというと、 DocumentDB は、書き込み最適化、ロック フリー、ログ構造化インデックスのメンテナンス技法を使用します。 つまり、DocumentDB は一貫性のあるクエリを提供する一方で、高速なボリュームの書き込みもサポートします。 

DocumentDB のインデックス作成は、ストレージの効率性とマルチテナントの処理用に設計されています。 コスト効率性を実現するため、インデックスのディスク上のストレージ オーバーヘッドは低く、予測可能なものにします。 また、インデックスの更新は DocumentDB コレクションごとに割り当てられたシステム リソースの予算内で実行されます。

## <a name="a-namenextstepsa-next-steps"></a><a name="NextSteps"></a> 次のステップ
* 2015 年 8 月 31 日から 9 月 4 日の 41st Internal Conference on Very Large Databases (第 41 回 VLDB 社内会議) で発表される論文「 [Schema-Agnostic Indexing with Azure DocumentDB (Azure DocumentDB を使用したスキーマに依存しないインデックス作成)](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)」をダウンロードします。
* [DocumentDB SQL を使用したクエリ](documentdb-sql-query.md)
* DocumentDB のインデックスをカスタマイズする方法については、 [こちら](documentdb-indexing-policies.md)


