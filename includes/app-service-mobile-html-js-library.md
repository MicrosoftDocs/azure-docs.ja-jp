---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905069"
---
## <a name="create-client"></a>クライアント接続の作成
`WindowsAzure.MobileServiceClient` オブジェクトを作成して、クライアント接続を作成します。  `appUrl` を Mobile App の URL に置き換えます。

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="table-reference"></a>テーブルの操作
データへのアクセスやデータの更新を行うには、バックエンド テーブルへの参照を作成します。 `tableName` を実際のテーブルの名前に置き換えます。

```javascript
var table = client.getTable(tableName);
```

テーブル参照を作成したら、テーブルで次の操作を行うことができます。

* [テーブルのクエリ](#querying)
  * [データのフィルター処理](#table-filter)
  * [データのページング](#table-paging)
  * [データの並べ替え](#sorting-data)
* [データの挿入](#inserting)
* [データの変更](#modifying)
* [データの削除](#deleting)

### <a name="querying"></a>方法:テーブル参照でクエリを実行する
テーブル参照を作成したら、それを使用してサーバー上のデータのクエリを実行できます。  クエリは "LINQ のような" 言語で作成します。
テーブルからすべてのデータを返すには、次のコードを使用します。

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

success 関数は results を指定して呼び出します。  success 関数で `for (var i in results)` は使用しないでください。これを指定すると、他のクエリ関数 (`.includeTotalCount()` など) を使用した場合に、その結果に含まれる情報が反復処理されるためです。

クエリ構文の詳細については、[クエリ オブジェクトのドキュメント]を参照してください。

#### <a name="table-filter"></a>サーバー上のデータのフィルター処理
テーブル参照に対して `where` 句を使用できます。

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

オブジェクトをフィルター処理する関数を使用することもできます。  この場合は、フィルター処理対象の現在のオブジェクトに `this` 変数を割り当てます。  次のコードは、機能的には前の例と同じです。

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="table-paging"></a>データのページング
`take()` メソッドと `skip()` メソッドを使用します。  たとえば、テーブルを 100 行のレコードに分割する場合:

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

`.includeTotalCount()` メソッドを使用して、results オブジェクトに totalCount フィールドを追加します。  totalCount フィールドには、ページングを使用しない場合に返されるレコード数の合計が入力されます。

pages 変数といくつかの UI ボタンを使用して、ページ リストを指定できます。`loadPage()` を使用すると、ページごとに新しいレコードを読み込むことができます。  既に読み込まれているレコードへのアクセス時間を短縮するには、キャッシュを実装します。

#### <a name="sorting-data"></a>方法:並べ替えられたデータを返す
`.orderBy()` クエリ メソッドまたは `.orderByDescending()` クエリ メソッドを使用します。

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

クエリ オブジェクトの詳細については、[クエリ オブジェクトのドキュメント]を参照してください。

### <a name="inserting"></a>方法:データを挿入する
適切な日付を指定して JavaScript オブジェクトを作成し、`table.insert()` を非同期に呼び出します。

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

挿入に成功すると、挿入された項目が、同期操作で必要な追加のフィールドで返されます。  以降の更新に対しては、この情報を保持する独自のキャッシュを更新します。

Azure Mobile Apps Node.js サーバー SDK では、開発用に動的なスキーマをサポートしています。  動的スキーマでは、挿入操作または更新操作で列を指定するだけで、テーブルに列を追加できます。  実稼働環境にアプリケーションを移行する前に、動的スキーマを無効にしておくことをお勧めします。

### <a name="modifying"></a>方法:データを変更する
`.insert()` メソッドの場合と同様に、update オブジェクトを作成して `.update()` を呼び出す必要があります。  update オブジェクトには更新するレコードの ID を含める必要があります。この ID は、レコードの読み取り時または `.insert()` の呼び出し時に取得されます。

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="deleting"></a>方法:データの削除
レコードを削除するには、`.del()` メソッドを呼び出します。  オブジェクト参照に ID を渡します。

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
