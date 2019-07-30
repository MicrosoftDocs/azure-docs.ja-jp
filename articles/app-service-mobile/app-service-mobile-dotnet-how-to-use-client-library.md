---
title: App Service Mobile Apps の管理されたクライアント ライブラリの操作 | Microsoft Docs
description: Windows および Xamarin アプリで Azure App Service Mobile Apps 用の .NET クライアント ライブラリを使用する方法について説明します。
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: af0a4af2bec29e68175d2e15203a02507f08bfeb
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446353"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Azure Mobile Apps 用の管理されたクライアントの使用方法
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-dotnet-how-to-use-client-library) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このガイドでは、Windows および Xamarin アプリで Azure App Service Mobile Apps 用の管理されたクライアント ライブラリを使用する一般的なシナリオの実行方法を示します。 Mobile Apps を初めて使用する場合は、まず、 [Azure Mobile Apps のクイックスタート][1] チュートリアルを完了することを検討してください。 このガイドでは、クライアント側の管理された SDK に重点を置いています。 Mobile Apps 用のサーバー側 SDK の詳細については、[.NET Server SDK][2] or the
[Node.js Server SDK][3] に関するドキュメントを参照してください。

## <a name="reference-documentation"></a>リファレンス ドキュメント
クライアント SDK のリファレンス ドキュメントは、[Azure Mobile Apps .NET クライアントのリファレンス][4]に関するページにあります。
GitHub リポジトリの「 [Azure Samples (Azure サンプル)][5]」にも、いくつかのクライアント サンプルがあります。

## <a name="supported-platforms"></a>サポートされているプラットフォーム
.NET プラットフォームでは、次のプラットフォームをサポートしています。

* API 19 ～ 24 (KitKat から Nougat) 対応 Xamarin Android リリース
* iOS バージョン 8.0 以降対応 Xamarin iOS リリース
* ユニバーサル Windows プラットフォーム
* Windows Phone 8.1
* Windows Phone 8.0 (Silverlight アプリケーションを除く)

"サーバーフロー" の認証では、UI の表示に WebView を使用します。  デバイスで WebView UI を表示できない場合は、他の認証方法が必要になります。  そのため、この SDK は腕時計型デバイスのような制限付きデバイスには適していません。

## <a name="setup"></a>セットアップと前提条件
テーブルを少なくとも 1 つ含むモバイル アプリ バックエンド プロジェクトを既に作成して発行してあるものとします。  このトピックで使用するコードでは、テーブルの名前は `TodoItem` であり、`Id`、`Text`、`Complete` の各列が含まれています。 このテーブルは、 [Azure Mobile Apps クイックスタート][1]で作成したものと同じテーブルです。

C# での対応する型指定されたクライアント側の型は次のクラスです。

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[JsonPropertyAttribute][6] を使用して、クライアントのフィールドとテーブルのフィールド間の *PropertyName* のマッピングが定義されます。

Mobile Apps バックエンドでテーブルを作成する方法については、[.NET Server SDK のトピック][7]
or the [Node.js Server SDK topic][8]を参照してください。 Azure ポータルでクイックスタートを使用してモバイル アプリ バックエンドを作成した場合は、 [Azure ポータル] で **Easy Tables**設定を使用することもできます。

### <a name="how-to-install-the-managed-client-sdk-package"></a>方法:マネージド クライアント SDK パッケージをインストールする
[NuGet][9]から、Mobile Apps 用の管理されたクライアント SDK パッケージをインストールするには、次のいずれかの方法を使用します。

* **Visual Studio** でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。`Microsoft.Azure.Mobile.Client` パッケージを見つけ、 **[インストール]** をクリックします。
* **Xamarin Studio** でプロジェクトを右クリックし、 **[Add]\(追加\)** 、> **[Add NuGet Packages]\(NuGet パッケージの追加\)** の順にクリックします。`Microsoft.Azure.Mobile.Client` パッケージを見つけ、 **[Add Package]\(パッケージの追加\)** をクリックします。

メイン アクティビティ ファイルに、次の **using** ステートメントを必ず追加してください。

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Android プロジェクトで参照されるすべてのサポート パッケージのバージョンが同じである必要があることに注意してください。 SDK には、Android プラットフォームに関して `Xamarin.Android.Support.CustomTabs` の依存関係があるため、プロジェクトで新しいサポート パッケージを使用する場合は、必要なバージョンを使用してこのパッケージを直接インストールし、競合を回避する必要があります。

### <a name="symbolsource"></a>方法: Visual Studio でデバッグ シンボルを使用する
SymbolSource を Visual Studio と統合するには、Microsoft.Azure.Mobile 名前空間のシンボルを [SymbolSource][10].  Refer to the
[SymbolSource instructions][11] に使用します。

## <a name="create-client"></a>Mobile Apps クライアントの作成
次のコードでは、モバイル アプリ バックエンドへのアクセスに使用する [MobileServiceClient][12] オブジェクトを作成します。

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

上記のコードで、`MOBILE_APP_URL` をモバイル アプリ バックエンドの URL に置き換えます。この URL は、[Azure ポータル]のモバイル アプリ バックエンドのブレードで確認できます。 MobileServiceClient オブジェクトはシングルトンである必要があります。

## <a name="work-with-tables"></a>テーブルの操作
以下のセクションでは、レコードを検索し、取得する方法や、テーブル内のデータを変更する方法について詳しく説明します。  次のトピックについて説明します。

* [テーブル参照を作成する](#instantiating)
* [データのクエリを実行する](#querying)
* [返されるデータをフィルター処理する](#filtering)
* [返されるデータを並べ替える](#sorting)
* [ページにデータを返す](#paging)
* [特定の列を選択する](#selecting)
* [ID でレコードを検索する](#lookingup)
* [型指定のないクエリを処理する](#untypedqueries)
* [データを挿入する](#inserting)
* データを更新する
* [データを削除する](#deleting)
* [競合の解決とオプティミスティック コンカレンシー](#optimisticconcurrency)
* [Windows ユーザー インターフェイスへのバインド](#binding)
* [ページ サイズを変更する](#pagesize)

### <a name="instantiating"></a>方法: テーブル参照を作成する
バックエンド テーブルのデータへのアクセスまたはデータの変更を行うすべてのコードで、 `MobileServiceTable` オブジェクトに対して関数を呼び出します。 次のように、 [GetTable] メソッドを呼び出して、テーブルへの参照を取得します。

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

返されたオブジェクトでは、型指定されたシリアル化モデルを使用します。 型指定されていないシリアル化モデルもサポートされます。 次のコード例では、[型指定されていないテーブルへの参照を作成]します。

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

型指定されていないクエリでは、基になる OData クエリ文字列を指定する必要があります。

### <a name="querying"></a>方法: Mobile App のデータを照会する
このセクションでは、モバイル アプリ バックエンドにクエリを発行する方法について説明します。これには次の機能が含まれます。

* [返されるデータをフィルター処理する](#filtering)
* [返されるデータを並べ替える](#sorting)
* [ページにデータを返す](#paging)
* [特定の列を選択する](#selecting)
* [ID でデータを検索する](#lookingup)

> [!NOTE]
> すべての行が返されるのを防ぐために、サーバー側で設定されたページ サイズが適用されます。  ページングは、大きなデータ セットの既定の要求がサービスに悪影響を与えるのを防ぎます。  50 行を超える行を返すには、「[ページにデータを返す](#paging)」で説明するように、`Skip` メソッドと `Take` メソッドを使用します。

### <a name="filtering"></a>方法: 返されるデータをフィルター処理する
次のコードは、クエリに `Where` 句を含めることによってデータをフィルター処理する方法を示しています。 このコードは、`Complete` プロパティが `false` に等しい `todoTable` からすべての項目を返します。 [where] 関数は、テーブルに対するクエリに行のフィルタリング述語を適用します。

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

ブラウザー開発者ツールや [Fiddler]などのメッセージ検査ソフトウェアを使用して、バックエンドに送信された要求の URI を表示できます。 要求 URI を確認すると、クエリ文字列が変更されていることがわかります。

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

この OData 要求は、Server SDK によって SQL クエリに変換されます。

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

`Where` メソッドに渡される関数には、任意の数の条件を設定できます。

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

次の例は、Server SDK によって SQL クエリに変換されます。

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

このクエリは複数の句に分割することもできます。

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

この 2 つの方法は等価であり、区別しないで使用できます。  複数の述語を 1 つのクエリに連結する前のオプション &mdash; のほうが、&mdash;よりコンパクトでありお勧めです。

`Where` 句は、OData サブセットに変換される操作をサポートします。 操作には以下が含まれます。

* 関係演算子 (==、!=、<、<=、>、>=)
* 算術演算子 (+、-、/、*、%)
* 数値の精度 (Math.Floor、Math.Ceiling)
* 文字列関数 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)
* 日付プロパティ (Year、Month、Day、Hour、Minute、Second)
* オブジェクトのプロパティへのアクセス
* これらの操作を組み合わせる式

Server SDK のサポート対象については、 [OData v3 のドキュメント]をご覧ください。

### <a name="sorting"></a>方法: 返されるデータを並べ替える
次のコードは、クエリに [OrderBy] 関数または [OrderByDescending] 関数を含めることによってデータを並べ替える方法を示しています。 次のコードは、`todoTable` から、`Text` フィールドの値に基づいて昇順に並べ替えられた項目を返します。

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>方法: ページにデータを返す
既定では、バックエンドは最初の 50 行のみを返します。 [Take] メソッドを呼び出すことによって、返される行の数を増やすことができます。 [Skip] メソッドと共に `Take` を使用して、クエリによって返されるデータセット全体のうち特定の "ページ" を要求します。 次のクエリを実行すると、テーブルの最初の上位 3 つの項目が返されます。

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

次の変更されたクエリは、最初の 3 つの結果をスキップし、その後の 3 つの結果を返します。 ページ サイズが 3 つの項目である場合、このクエリによってデータの 2 番目の "ページ" が生成されます。

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

[IncludeTotalCount] メソッドは、指定された paging/limit 句を無視して、返された " *すべて* " のレコードの総数を要求します。

```csharp
query = query.IncludeTotalCount();
```

実際のアプリケーションでは、ページャー コントロールまたは同等の UI と共に前の例と同様のクエリを使用して、ページ間を移動することができます。

> [!NOTE]
> また、モバイル アプリ バックエンドの 50 行の制限をオーバーライドするには、 [EnableQueryAttribute] をパブリック GET メソッドに適用し、ページング動作を指定する必要があります。 次のコードをメソッドに適用すると、返される最大行数が 1000 行に設定されます。
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>方法: 特定の列を選択する
クエリに [Select] 句を追加することで、結果に含める一連のプロパティを指定できます。 たとえば、次のコードでは、1 つのフィールドだけを選択する方法と、複数のフィールドを選択し、フォーマットする方法も示しています。

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

これまでに説明した関数はいずれも付加的なものであるため、これらの関数を連鎖させることができます。 連鎖した呼び出しはそれぞれ複数のクエリに影響を及ぼします。 次の例も参照してください。

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>方法: ID でデータを検索する
[LookupAsync] 関数を使用すると、特定の ID でデータベースのオブジェクトを検索できます。

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>方法: 型指定されていないクエリを実行する
型指定されていないテーブル オブジェクトを使用してクエリを実行するときは、次の例のように [ReadAsync]を呼び出して、OData クエリ文字列を明示的に指定する必要があります。

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

プロパティ バッグのように使用できる JSON 値が返されます。 JToken と Newtonsoft Json.NET の詳細については、 [Json.NET] サイトをご覧ください。

### <a name="inserting"></a>方法: モバイル アプリ バックエンドにデータを挿入する
クライアントのすべての型には、**Id** という名前のメンバーが含まれる必要があります。その既定値は文字列です。 この **Id** は、オフライン同期で CRUD 操作を実行するために必要となります。次のコードは、[InsertAsync] メソッドを使用してテーブルに新しい行を挿入する方法を示しています。 パラメーターには、挿入するデータが .NET オブジェクトとして含まれます。

```csharp
await todoTable.InsertAsync(todoItem);
```

挿入時に一意のカスタム ID 値が `todoItem` に含まれていない場合、サーバーによって GUID が生成されます。
生成された ID を取得するには、呼び出しから制御が戻った後にオブジェクトを調べます。

型指定されていないデータを挿入するときは、Json.NET を利用することもできます。

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

電子メール アドレスを一意の文字列 ID として使用する例を次に示します。

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>ID 値の操作
Mobile Apps は、テーブルの **ID** 列で一意のカスタム文字列値をサポートしています。 文字列値により、アプリケーションで電子メール アドレスやユーザー名などのカスタム値を ID に使用することが可能になります。  文字列 ID には、次のような利点があります。

* ID は、データベースとのラウンド トリップを行わずに生成されます。
* 他のテーブルやデータベースのレコードをより簡単にマージできます。
* ID 値をより適切にアプリケーションのロジックに統合できます。

挿入されたレコードで文字列 ID 値が設定されない場合は、モバイル アプリ バックエンドによって ID 用の一意の値が生成されます。 [Guid.NewGuid] メソッドを使用すると、クライアント上またはバックエンドで、独自の ID 値を生成できます。

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>方法: モバイル アプリ バックエンドのデータを変更する
次のコードは、 [UpdateAsync] メソッドを使用して、同じ ID を持つ既存のレコードを新しい情報で更新する方法を示しています。 パラメーターには、更新するデータが .NET オブジェクトとして含まれます。

```csharp
await todoTable.UpdateAsync(todoItem);
```

型指定されていないデータを更新するには、次のように [Json.NET] を利用できます。

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

更新を行うときは、 `id` フィールドを指定する必要があります。 バックエンドでは、 `id` フィールドを使用して更新する行を識別します。 `id` フィールドは、`InsertAsync` 呼び出しの結果から取得できます。 `id` 値を指定せずに項目を更新しようとすると、`ArgumentException` が発生します。

### <a name="deleting"></a>方法: モバイル アプリ バックエンドのデータを削除する
次のコードは、 [DeleteAsync] メソッドを使用して既存のインスタンスを削除する方法を示しています。 インスタンスは、`todoItem` に設定した `id` フィールドで識別されます。

```csharp
await todoTable.DeleteAsync(todoItem);
```

型指定されていないデータを削除するには、次のような Json.NET を利用できます。

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

削除要求を行うときは、ID を指定する必要があります。 それ以外のプロパティは、サービスに渡されないか、またはサービスで無視されます。 通常、`DeleteAsync` の呼び出しの結果は `null` です。 渡す ID は、 `InsertAsync` の呼び出しの結果から取得できます。 `id` フィールドを指定せずに項目を削除しようとすると、`MobileServiceInvalidOperationException` がスローされます。

### <a name="optimisticconcurrency"></a>方法: 競合の解決にオプティミスティック同時実行制御を使用する
複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。 競合を検出しない場合、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。 **オプティミスティック コンカレンシー** では、それぞれのトランザクションがコミットでき、そのためリソース ロックが一切使用されないことを前提としています。  オプティミスティック コンカレンシーではトランザクションをコミットする前に、他のトランザクションがそのデータを変更していないことを確認します。 データが変更されている場合、トランザクションのコミットはロール バックされます。

Mobile Apps はオプティミスティック コンカレンシーをサポートしており、モバイル アプリ バックエンドで各テーブルに定義されている `version` システム プロパティ列を使用して各項目の変更を追跡します。 レコードが更新されるたびに、Mobile Apps はそのレコードの `version` プロパティを新しい値に設定します。 各更新要求の際に、要求に含まれているレコードの `version` プロパティが、サーバー上のレコードの同じプロパティと比較されます。 要求で渡されたバージョンがバックエンドと一致しない場合、クライアント ライブラリは `MobileServicePreconditionFailedException<T>` 例外を生成します。 例外に含まれている型は、レコードのサーバー側のバージョンを含んでいるバックエンドのレコードです。 アプリケーションはこの情報を使用して、バックエンドからの正しい `version` 値で更新要求をもう一度実行して変更をコミットするかどうかを判断できます。

オプティミスティック コンカレンシーを有効にするには、テーブル クラスに `version` システム プロパティ用の列を定義します。 例:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

型指定のないテーブルを使用しているアプリケーションは、テーブルの `SystemProperties` に次のように `Version` フラグを設定することによって、オプティミスティック コンカレンシーを有効にします。

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

オプティミスティック コンカレンシーの有効化に加え、[UpdateAsync] の呼び出し時にコード内で `MobileServicePreconditionFailedException<T>` 例外をキャッチすることも必要です。  更新対象のレコードに適切な `version` を適用して競合を解決し、解決済みのレコードで [UpdateAsync] を呼び出します。 次のコードは、書き込み競合が検出された場合にそれを解決する方法を示しています。

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

詳細については、「 [Azure Mobile Apps でのオフライン データ同期] 」をご覧ください。

### <a name="binding"></a>方法: Mobile Apps のデータを Windows ユーザー インターフェイスにバインドする
このセクションでは、Windows アプリで UI 要素を使用して、返されたデータ オブジェクトを表示する方法について説明します。  次のコード例では、不完全な項目のクエリによってリストのソースにバインドします。 [MobileServiceCollection] は、Mobile Apps 対応のバインディング コレクションを作成します。

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

マネージド ランタイムの一部のコントロールでは、 [ISupportIncrementalLoading]というインターフェイスがサポートされます。 このインターフェイスにより、コントロールはユーザーによるスクロールの際に追加のデータを要求することができます。 このインターフェイスには、コントロールからの呼び出しを自動的に処理する、 [MobileServiceIncrementalLoadingCollection]によるユニバーサル Windows アプリのサポートが組み込まれています。 次のように、Windows アプリで `MobileServiceIncrementalLoadingCollection` を使用します。

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Windows Phone 8 と "Silverlight" アプリで新しいコレクションを使用するには、`IMobileServiceTableQuery<T>` や `IMobileServiceTable<T>` で `ToCollection` 拡張メソッドを使用します。 データを読み込むには、 `LoadMoreItemsAsync()`を呼び出します。

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

`ToCollectionAsync` または `ToCollection` を呼び出して作成されたコレクションを使用する場合は、UI コントロールにバインドできるコレクションを取得します。  このコレクションはページングに対応しています。  このコレクションはネットワークからデータを読み込むため、読み込みが失敗することがあります。 このようなエラーを処理するには、`MobileServiceIncrementalLoadingCollection` で `OnException` メソッドをオーバーライドして、`LoadMoreItemsAsync` の呼び出しによって発生する例外を処理します。

テーブルに多くのフィールドが存在するときに、コントロールにその一部だけを表示する必要があるとします。 UI に表示する特定の列を選択するときは、前述の「[特定の列を選択する](#selecting)」のガイダンスを参考にしてください。

### <a name="pagesize"></a>ページ サイズの変更
Azure Mobile Apps は、既定で要求ごとに最大 50 個の項目を返します。  クライアントとサーバーの両方で最大ページ サイズを増やすことで、ページング サイズを変更できます。  要求されたページ サイズを増やすには、`PullAsync()` の使用時に `PullOptions` を指定します。

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

サーバー内で `PageSize` に 100 以上の値を設定した場合は、要求で最大 100 個の項目が返されます。

## <a name="#offlinesync"></a>オフライン テーブルの操作
オフライン テーブルは、ローカル SQLite ストア データを使用して、オフラインのときに使用するためのデータを格納します。  テーブル操作はすべて、リモート サーバー ストアではなく、ローカル SQLite ストアに対して実行されます。  オフライン テーブルを作成するには、まずプロジェクトを準備します。

1. Visual Studio で、ソリューション、 **[ソリューションの NuGet パッケージの管理...]** の順に右クリックし、ソリューション内のすべてのプロジェクトの **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet パッケージを探してインストールします。
2. (省略可能) Windows デバイスをサポートする場合、次の SQLite ランタイム パッケージのいずれかをインストールします。

   * **Windows 8.1 ランタイム:** [SQLite for Windows 8.1][3] をインストールします。
   * **Windows Phone 8.1:** [SQLite for Windows Phone 8.1][4] をインストールします。
   * **ユニバーサル Windows プラットフォーム**: [ユニバーサル Windows プラットフォーム用 SQLite][5] をインストールします。
3. (省略可能)。 Windows デバイスで、 **[参照]** 、 >  **[参照の追加]** の順に右クリックします。**Windows** フォルダー、 **[拡張機能]** の順に展開してから、**Visual C++ 2013 Runtime for Windows** SDK と共に適切な **SQLite for Windows** SDK を有効にします。
    Windows プラットフォームによって SQLite SDK の名前はわずかに異なります。

テーブル参照を作成する前に、ローカル ストアを準備する必要があります。

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

ストアの初期化は通常、クライアントが作成された直後に行います。  **OfflineDbPath** は、サポートするすべてのプラットフォームで使用できるファイル名にする必要があります。  パスが完全修飾パスである場合 (つまり、スラッシュで始まる場合)、そのパスが使用されます。  パスが完全修飾パスでない場合、ファイルはプラットフォーム固有の場所に配置されます。

* iOS と Android のデバイスの既定のパスは、「個人ファイル」フォルダーです。
* Windows デバイスの既定のパスは、アプリケーション固有の AppData フォルダーです。

テーブル参照は、`GetSyncTable<>` メソッドを使用して取得できます。

```csharp
var table = client.GetSyncTable<TodoItem>();
```

オフライン テーブルを使用する際に、認証は必要ありません。  認証が必要になるのは、バックエンド サービスと通信するときのみです。

### <a name="syncoffline"></a>オフライン テーブルの同期
既定では、オフライン テーブルはバックエンドと同期されません。  同期は、2 つの部分に分割されます。  新しい項目のダウンロードから個別に変更をプッシュできます。  典型的な同期メソッドを次に示します。

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

`PullAsync` の最初の引数が null の場合、増分同期は使用されません。  各同期操作で、すべてのレコードが取得されます。

レコードを取得する前に、SDK で暗黙的な `PushAsync()` が実行されます。

競合の処理は、`PullAsync()` メソッドで行われます。  オンライン テーブルと同じ方法で競合を処理することができます。  競合が発生するのは、挿入、更新、または削除のときではなく、`PullAsync()` が呼び出されたときです。 複数の競合が発生した場合、競合は単一の MobileServicePushFailedException にバンドルされます。  エラーは個別に処理します。

## <a name="#customapi"></a>カスタム API の使用
カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。 カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

クライアントで [InvokeApiAsync] メソッドのいずれかを呼び出して、カスタム API を呼び出します。 たとえば、次のコード行は、バックエンドの **completeAll** API に POST 要求を送信します。

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

この形式は型指定されたメソッド呼び出しであり、**MarkAllResult** の戻り値の型が定義されている必要があります。 型指定および型指定のないメソッドの両方がサポートされます。

API が "/" で始まっていない限り、InvokeApiAsync() メソッドは API の先頭に "/api/" を追加します。
例:

* `InvokeApiAsync("completeAll",...)` はバックエンドで /api/completeAll を呼び出します
* `InvokeApiAsync("/.auth/me",...)` はバックエンドで /.auth/me を呼び出します

InvokeApiAsync を使用して、Azure Mobile Apps で定義されていない WebAPI を含むすべての WebAPI を呼び出すことができます。  InvokeApiAsync() を使用した場合は、認証ヘッダーを含む適切なヘッダーが要求と一緒に送信されます。

## <a name="authentication"></a>ユーザーの認証
Mobile Apps は、Facebook、Google、Microsoft アカウント、Twitter、Azure Active Directory などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。 テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。 さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。 詳細については、チュートリアル「 [アプリケーションへの認証の追加]」を参照してください。

*クライアント側管理フロー*と*サーバー側管理フロー*の 2 つの認証フローがサポートされています。 サーバー側管理フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。 クライアント側管理フローでは、プロバイダー固有とデバイス固有の SDK を利用することから、デバイス固有の機能との統合がさらに進みます。

> [!NOTE]
> 運用環境のアプリでは、クライアント側管理フローをお勧めします。

認証を設定するには、1 つ以上の ID プロバイダーにアプリを登録する必要があります。  ID プロバイダーは、アプリのクライアント ID とクライアント シークレットを生成します。  これらの値は、Azure App Service の認証/承認を有効にするためにバックエンドで設定されます。  詳細については、 [アプリケーションへの認証の追加]のチュートリアルの手順を参照してください。

このセクションでは、次のトピックについて説明します。

* [クライアント側管理認証](#clientflow)
* [サーバー側管理認証](#serverflow)
* [認証トークンをキャッシュする](#caching)

### <a name="clientflow"></a>クライアント側管理認証
アプリ側から自主的に ID プロバイダーにアクセスし、取得したトークンをバックエンドへのログイン時に渡すことができます。 このクライアント フローでは、ユーザーにシングル サインオン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。 ID プロバイダー SDK を使用すると、よりネイティブな UX が実現し、さらにカスタマイズすることが可能になるため、クライアント フローの認証はサーバー フローを使用する方法よりも推奨されます。

クライアント フローの認証パターンとして、次の例が提供されています。

* [Active Directory Authentication Library](#adal)
* [Facebook と Google](#client-facebook)

#### <a name="adal"></a>Active Directory Authentication Library を使用したユーザーの認証
Active Directory Authentication Library (ADAL) を使うと、クライアントから Azure Active Directory 認証を使用してユーザー認証を開始できます。

1. 「[Azure Active Directory ログインを使用するように App Service を構成する方法]」のチュートリアルに従って、AAD のサインイン用にモバイル アプリ バックエンドを構成します。 ネイティブ クライアント アプリケーションを登録する省略可能な手順を確実に実行します。
2. Visual Studio または Xamarin Studio でプロジェクトを開き、 `Microsoft.IdentityModel.Clients.ActiveDirectory` NuGet パッケージへの参照を追加します。 検索時に、プレリリース版を含めます。
3. ご使用のプラットフォームに応じて、以下のコードをアプリケーションに追加します。 それぞれで、次の置換を行います。

   * **INSERT-AUTHORITY-HERE** を、アプリケーションをプロビジョニングしたテナントの名前に置き換えます。 形式は https://login.microsoftonline.com/contoso.onmicrosoft.com である必要があります。 この値は、[Azure ポータル] の Azure Active Directory の [ドメイン] タブからコピーできます。
   * **INSERT-RESOURCE-ID-HERE** を、モバイル アプリ バックエンドのクライアント ID に置き換えます。 クライアント ID は、ポータルの **[Azure Active Directory の設定]** の **[詳細]** タブで入手できます。
   * **INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。
   * **INSERT-REDIRECT-URI-HERE** を、HTTPS スキームを使用して、サイトの */.auth/login/done* エンドポイントに置き換えます。 これは、 *https://contoso.azurewebsites.net/.auth/login/done* のような値である必要があります。

     各プラットフォームに必要なコードは次のとおりです。

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="client-facebook"></a>Facebook または Google から取得したトークンを使用したシングル サインオン
この Facebook や Google のスニペットに示すようにクライアント フローを使用できます。

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="serverflow"></a>サーバー側管理認証
ID プロバイダーを登録したら、プロバイダーの [LoginAsync] 値を指定して、[MobileServiceClient] で [LoginAsync] メソッドを呼び出します。 たとえば、次のコードは、Facebook を使用してサーバー フローのサインインを開始します。

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Facebook 以外の ID プロバイダーを使用している場合は、[LoginAsync] の値をプロバイダーに対応する値に変更してください。

サーバー フローでは、Azure App Service は、選択したプロバイダーのサインイン ページを表示することで OAuth 認証フローを管理します。  ID プロバイダーから制御が戻ったら、Azure App Service は App Service 認証トークンを生成します。 [LoginAsync] メソッドは [MobileServiceUser] を返します。これにより、認証されたユーザーの [UserId] と、JSON Web トークン (JWT) としての [MobileServiceAuthenticationToken] が提供されます。 このトークンをキャッシュし、有効期限が切れるまで再利用できます。 詳細については、「[認証トークンをキャッシュする](#caching)」を参照してください。

### <a name="caching"></a>認証トークンをキャッシュする
最初の認証に成功した後、プロバイダーからの認証トークンを保存することによって、ログイン メソッドの呼び出しを回避できる場合があります。  Microsoft Storeアプリと UWP アプリでは、正常にサインインした後、次にように [PasswordVault] を使用して現在の認証トークンをキャッシュできます：

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

UserId 値は、資格情報の UserName として保存され、トークンは Password として保存されます。 以降の起動時には、資格情報がキャッシュされていないか **PasswordVault** をチェックすることができます。 以下の例では、キャッシュされた資格情報が見つかればそれを使用し、それ以外の場合は、バックエンドに対して再度認証を試みます。

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

ユーザーをサインアウトさせるときは、次のように、保存された資格情報を削除する必要があります。

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin アプリでは、 [Xamarin.Auth] API を使用して、 **Account** オブジェクトに資格情報を安全に保存します。 これらの API の使用例については、[ContosoMoments 写真共有サンプル](https://github.com/azure-appservice-samples/ContosoMoments)の [AuthStore.cs] コード ファイルを参照してください。

クライアント側管理認証を使用するときは、Facebook や Twitter などのプロバイダーから取得したアクセス トークンをキャッシュすることもできます。 このトークンを指定することで、新しい認証トークンをバックエンドに要求することができます。その例を次に示します。

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>プッシュ通知
以下のトピックでは、プッシュ通知について説明します。

* [プッシュ通知に登録する](#register-for-push)
* [Microsoft Store のパッケージ SID を取得する](#package-sid)
* [クロスプラットフォームのテンプレートで登録する](#register-xplat)

### <a name="register-for-push"></a>方法: プッシュ通知に登録する
Mobile Apps クライアントでは、Azure Notification Hubs によるプッシュ通知に登録できます。 登録する場合、プラットフォーム固有のプッシュ通知サービス (PNS) からハンドルを取得します。 この値は、登録を作成するときに、任意のタグと一緒に指定します。 次のコードは、Windows Notification Service (WNS) によるプッシュ通知用の Windows アプリを登録します。

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

WNS に対するプッシュを行う場合は、[Microsoft Store パッケージ SID を取得する](#package-sid)必要があります。  テンプレート登録に登録する方法を含む Windows アプリの詳細については、「 [アプリにプッシュ通知を追加する]」を参照してください。

クライアントからのタグ要求はサポートされていません。  タグ要求は、通告なく登録から削除されます。
タグと共にデバイスを登録したい場合は、Notification Hubs API を使用するカスタム API を作成し、登録を代行させます。  `RegisterNativeAsync()` メソッドの代わりに、カスタム API を呼び出します。

### <a name="package-sid"></a>方法:Microsoft Store のパッケージ SID を取得する
Microsoft Store アプリでプッシュ通知を有効にするには、パッケージ SID が必要です。  パッケージ SID を受け取るには、Microsoft Store　にアプリケーションを登録します。

この値を取得するには:

1. Visual Studio ソリューション エクスプローラーで、Microsoft Store アプリプロジェクトを右クリックし、  **[ストア]** 、 >  **[アプリケーションをストアと関連付ける]** の順にクリックします。
2. ウィザードで **[次へ]** をクリックし、Microsoft アカウントでサインインします。次に、 **[新しいアプリケーション名の予約]** にアプリの名前を入力し、 **[予約]** をクリックします。
3. アプリの登録が正常に作成されたら、アプリ名を選択し、 **[次へ]** をクリックして、 **[関連付け]** をクリックします。
4. Microsoft アカウントを使用して [Windows デベロッパー センター] にログインします。 **[マイ アプリ]** で、作成したアプリ登録をクリックします。
5. **[アプリ管理]** 、 >  **[アプリ ID]** の順にクリックし、下にスクロールして **[パッケージ SID]** を探します。

多くの場合、パッケージ SID は URI として処理されます。その場合、スキームとして *ms-app://* を使用する必要があります。 この値をプレフィックスとして連結して形成されたパッケージ SID のバージョンをメモしておきます。

Xamarin アプリでは、iOS または Android プラットフォームで実行されているアプリを登録できる追加のコードが必要です。 詳細については、ご使用のプラットフォームに関するトピックをご覧ください。

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>方法: プッシュ テンプレートを登録してクロス プラットフォーム通知を送信する
テンプレートを登録するには、次のようにテンプレートで `RegisterAsync()` メソッドを使用します。

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

テンプレートは `JObject` 型である必要があり、次の JSON 形式で複数のテンプレートを含めることができます。

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

**RegisterAsync()** メソッドは次のセカンダリ タイルも受け入れます。

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

セキュリティを確保するために、登録時にすべてのタグが削除されます。 インストールまたはインストール内のテンプレートにタグを追加する方法については、「Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作」を参照してください。

これらの登録済みテンプレートを使用して通知を送信する方法については、 [Notification Hubs API]に関するページをご覧ください。

## <a name="misc"></a>その他のトピック
### <a name="errors"></a>方法: エラーを処理する
バックエンドでエラーが発生すると、クライアント SDK が `MobileServiceInvalidOperationException`を生成します。  次の例では、バックエンドによって返される例外を処理する方法を示します。

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

エラー状況を処理する別の例については、 [Mobile Apps ファイル サンプル]で確認できます。 [LoggingHandler] の例では、ログ記録代理ハンドラーを使用して、バックエンドに対する要求を記録します。

### <a name="headers"></a>方法: 要求ヘッダーをカスタマイズする
特定のアプリケーション シナリオをサポートするには、モバイル アプリ バックエンドとの通信をカスタマイズすることが必要な場合があります。 たとえば、すべての送信要求にカスタム ヘッダーを追加したり、応答のステータス コードを変更したりすることが必要な場合がります。 次の例のように、カスタムの [DelegatingHandler]を使用できます。

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[アプリケーションへの認証の追加]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[アプリにプッシュ通知を追加する]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Azure Active Directory ログインを使用するように App Service を構成する方法]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[型指定されていないテーブルへの参照を作成]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure ポータル]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows デベロッパー センター]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobile Apps ファイル サンプル]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 のドキュメント]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
