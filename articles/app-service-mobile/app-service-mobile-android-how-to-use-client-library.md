---
title: Azure Mobile Apps SDK for Android の使用方法 | Microsoft Docs
description: Azure Mobile Apps SDK for Android の使用方法
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 6a6db136926a7f9d631c717f5cab6c025d97fb48
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443536"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Azure Mobile Apps SDK for Android の使用方法

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーとかかわることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-how-to-use-client-library) を今すぐチェックしてください。
>

このガイドでは、次のような一般的なシナリオを、Mobile Apps の Android クライアント SDK を使用して実装する方法について説明します。

* データに対するクエリ (挿入、更新、削除)
* 認証
* エラーの処理
* クライアントのカスタマイズ

このガイドでは、クライアント側の Android SDK に注目します。  Mobile Apps 用のサーバー側 SDK の詳細については、[.NET バックエンド SDK の操作][10]に関するページまたは [Node.js バックエンド SDK の使用方法][11]に関するページを参照してください。

## <a name="reference-documentation"></a>リファレンス ドキュメント

Android クライアント ライブラリの [Javadocs API リファレンス][12]は、GitHub にあります。

## <a name="supported-platforms"></a>サポートされているプラットフォーム

Azure Mobile Apps SDK for Android では、タブレットとスマートフォンのフォーム ファクター向けの API レベル 19 ～ 24 (KitKat から Nougat) がサポートされます。  認証では、資格情報の収集に一般的な Web フレームワーク アプローチが使用されます。  サーバー フロー認証は、ウォッチなどの小型のフォーム ファクターのデバイスでは機能しません。

## <a name="setup-and-prerequisites"></a>セットアップと前提条件

[Mobile Apps のクイックスタート](app-service-mobile-android-get-started.md) に関するページのチュートリアルを完了してください。  この作業により、Azure Mobile Apps の開発の前提条件がすべて満たされます。  このクイックスタートでは、アカウントを構成し、初めてのモバイル アプリ バックエンドを作成することもできます。

クイックスタート チュートリアルを行わない場合は、次の作業を完了してください。

* Android アプリで使用する[モバイル アプリ バックエンド][13]を作成する。
* Android Studio で [Gradle ビルド ファイルを更新する](#gradle-build)。
* [インターネット アクセス許可を有効にする](#enable-internet)。

### <a name="gradle-build"></a>Gradle ビルド ファイルを更新する

2 つの **build.gradle** ファイルを変更します。

1. 次のコードを、"*プロジェクト*" レベルの **build.gradle** ファイルに追加します。

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. 次のコードを、"*モジュール アプリ*" レベルの **build.gradle** ファイルの *dependencies* タグ内に追加します。

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    現在の最新バージョンは 3.4.0 です。 サポートされているバージョンの一覧については、[Bintray][14] を参照してください。

### <a name="enable-internet"></a>インターネット アクセス許可を有効にする

Azure にアクセスするには、アプリで INTERNET アクセス許可が有効になっている必要があります。 まだ有効になっていない場合は、次のコード行を **AndroidManifest.xml** ファイルに追加します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>クライアント接続の作成

Azure Mobile Apps は、モバイル アプリに次の 4 つの機能を提供します。

* Azure Mobile Apps Service でのデータ アクセスと Azure Mobile Apps Service とのオフライン同期
* Azure Mobile Apps Server SDK で記述されたカスタム API の呼び出し
* Azure App Service の認証/承認での認証
* Notification Hubs を使用したプッシュ通知登録

これらの各機能を使用するには、まず `MobileServiceClient` オブジェクトを作成する必要があります。  モバイル クライアント内に `MobileServiceClient` オブジェクトを 1 つのみ作成してください (シングルトン パターン)。  `MobileServiceClient` オブジェクトを作成するには、次の手順を実行します。

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` は、モバイル バックエンドをポイントする文字列か URL オブジェクトです。  モバイル バックエンドのホストに Azure App Service を使用した場合、URL のセキュリティ保護された `https://` バージョンを使用してください。

また、クライアントは、アクティビティまたはコンテキストにアクセスできる必要があります。この例では `this` パラメーターです。  MobileServiceClient の構築が `AndroidManifest.xml` ファイルで参照されるアクティビティの `onCreate()` メソッド内で発生する必要があります。

ベスト プラクティスとして、サーバーの通信を独自の (シングルトン パターン) クラスに要約する必要があります。  この場合、サービスを適切に構成するために、コンストラクター内のアクティビティを渡す必要があります。  例:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

これで、メイン アクティビティの `onCreate()` メソッドで `AzureServiceAdapter.Initialize(this);` を呼び出せるようになります。  クライアントへのアクセスを必要とするその他のメソッドは `AzureServiceAdapter.getInstance();` を使用して、サービス アダプターへの参照を取得します。

## <a name="data-operations"></a>データ操作

Azure Mobile Apps SDK の核となるのは、モバイル アプリ バックエンドの SQL Azure 内に格納されたデータへのアクセスの提供です。  このデータには、厳密に型指定されたクラス (推奨) または型指定のないクエリ (非推奨) を使用してアクセスします。  このセッションの大部分では、厳密に型指定されたクラスが使用されています。

### <a name="define-client-data-classes"></a>クライアント データ クラスを定義する

SQL Azure のテーブルからデータにアクセスするには、モバイル アプリ バックエンドのテーブルに対応するクライアント データ クラスを定義します。 このトピックの例では、次の列を含む、**MyDataTable** という名前のテーブルがあるものとします。

* id
* text
* 完了

対応する型指定されたクライアント側オブジェクトは、**MyDataTable.java** という名前のファイル内に存在します。

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

追加する各フィールドの getter および setter メソッドを追加します。  SQL Azure テーブルにさらに列が含まれる場合は、対応するフィールドをこのクラスに追加します。  たとえば、DTO (データ転送オブジェクト) に整数の Priority 列がある場合は、次のフィールドを getter および setter メソッドと共に追加します。

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Mobile Apps バックエンドに追加のテーブルを作成する方法については、「[方法:テーブル コントローラーを定義する][15]」(.NET バックエンド) または「[動的スキーマを使用してテーブルを定義する][16]」(Node.js バックエンド) を参照してください。

Azure Mobile Apps バックエンド テーブルでは 5 つの特別なフィールドが定義されます。そのうちの 4 つはクライアントで使用できます。

* `String id`:レコードのグローバルに一意の ID。  ベスト プラクティスとして、ID は [UUID][17] オブジェクトの文字列表現にします。
* `DateTimeOffset updatedAt`:前回の更新の日付/時刻。  updatedAt フィールドはサーバーによって設定されます。クライアント コードで設定されることはありません。
* `DateTimeOffset createdAt`:オブジェクトが作成された日付/時刻。  createdAt フィールドはサーバーによって設定されます。クライアント コードで設定されることはありません。
* `byte[] version`:通常、文字列として表され、バージョンもサーバーによって設定されます。
* `boolean deleted`:レコードが削除されたが、まだ消去されていないことを示します。  クラスのプロパティとして `deleted` を使用しないでください。

`id` フィールドは必須です。  `updatedAt` フィールドと `version` フィールドはオフライン同期に使用されます (それぞれ増分同期、競合の解決用)。  `createdAt` フィールドは参照フィールドであり、クライアントによって使用されません。  名前は、プロパティの "ネットワーク経由" の名前であり、調整することはできません。  ただし、[gson][3] ライブラリを使用して、オブジェクトと "ネットワーク経由" の名前との間のマッピングを作成することができます。  例:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>テーブル参照を作成する

テーブルにアクセスするには、最初に [MobileServiceClient][9] の **getTable** メソッドを呼び出して [MobileServiceTable][8] オブジェクトを作成します。  このメソッドには 2 つのオーバーロードがあります。

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

次のコードで、**mClient** は MobileServiceClient オブジェクトへの参照です。  1 つ目のオーバーロードは、クラス名とテーブル名が同じ場合に使用されます。これは Quickstart で使用しているものです。

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

2 つ目のオーバーロードは、テーブル名とクラス名が異なる場合に使用されます。1 番目のパラメーターはテーブル名です。

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>バックエンド テーブルのクエリ実行

最初にテーブル参照を取得します。  次に、テーブル参照にクエリを実行します。  クエリでは、以下の任意の組み合わせを使用します。

* `.where()` [フィルター句](#filtering)
* `.orderBy()` [Ordering 句](#sorting)
* `.select()` [フィールド選択句](#selection)
* [ページングされた結果](#paging)の `.skip()` および `.top()`

句は、上記の順で表される必要があります。

### <a name="filter"></a> フィルタリングの結果

クエリの一般的な形式は、次のとおりです。

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

前の例では、(サーバーで設定された最大ページ サイズまでの) すべての結果が返されます。  `.execute()` メソッドは、バックエンドでクエリを実行します。  クエリは、Mobile Apps バックエンドへの送信前に、[OData v3][19] クエリに変換されます。  受信後、Mobile Apps バックエンドは、SQL Azure インスタンスで実行する前にクエリを SQL ステートメントに変換します。  ネットワーク アクティビティに時間がかかるため、`.execute()` メソッドは [`ListenableFuture<E>`][18] を返します。

### <a name="filtering"></a>返されるデータをフィルター処理する

次のクエリを実行すると、**ToDoItem** テーブルで **complete** が **false** と等しい項目がすべて返されます。

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** は、既に作成したモバイル サービス テーブルへの参照です。

テーブル参照で **where** メソッド呼び出しを使用してフィルターを定義します。 **where** メソッドの次に **field** メソッドを呼び出してから、論理述語を指定するメソッドを呼び出します。 使用できる述語メソッドとしては、**eq** (等しい)、**ne** (等しくない)、**gt** (より大きい)、**ge** (以上)、**lt** (より小さい)、**le** (以下) などがあります。 これらのメソッドを使用して、数値フィールドおよび文字列フィールドを特定の値と比較できます。

日付をフィルター処理できます。 次のメソッドを使用すると、日付フィールド全体または日付の一部を比較できます: **year**、**month**、**day**、**hour**、**minute**、**second**。 次の例では、期限 ( *due* ) が 2013 に等しい項目のフィルターを追加しています。

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

**startsWith**、**endsWith**、**concat**、**subString**、**indexOf**、**replace**、**toLower**、**toUpper**、**trim**、**length** などのメソッドは、文字列フィールドに対する複雑なフィルターをサポートします。 次の例では、 *text* 列が "PRI0" で始まるテーブル行をフィルター処理します。

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

数値フィールドでは、**add**、**sub**、**mul**、**div**、**mod**、**floor**、**ceiling**、**round** 演算子メソッドがサポートされます。 次の例では、 **duration** が偶数のテーブル行をフィルター処理します。

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

論理メソッド **and**、**or**、**not** を使用すると、述語を結合できます。 次の例では、前の 2 つの例を結合しています。

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

論理演算子をグループ化し、入れ子にします。

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

フィルター処理の詳細と例については、「 [Exploring the richness of the Android client query model (Android クライアント クエリ モデルの機能を調査する)][20]」を参照してください。

### <a name="sorting"></a>返されるデータを並べ替える

次のコードは、 **ToDoItems** テーブルから、 *text* フィールドの値に基づいて昇順に並べ替えられたすべての項目を返します。 *mToDoTable* は、以前に作成したバックエンド テーブルへの参照です。

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

**orderBy** メソッドの 1 つ目のパラメーターは、並べ替えに使用するフィールドの名前に等しい文字列です。 2 番目のパラメーターでは、 **QueryOrder** 列挙を使用して、昇順または降順のどちらで並べ替えを行うかを指定します。  ***where*** メソッドを使用してフィルター処理を行う場合、***where*** メソッドは ***orderBy*** メソッドより前に呼び出す必要があります。

### <a name="selection"></a>特定の列を選択する

次のコードは、**ToDoItems** テーブルからすべての項目を返したうえで **complete** フィールドと **text** フィールドのみを表示する方法を示しています。 **mToDoTable** は、以前に作成したバックエンド テーブルへの参照です。

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

select 関数のパラメーターは、取得するテーブルの列の文字列名です。  **select** メソッドは、**where** や **orderBy** のようなメソッドの後に記述する必要があります。 このメソッドの後に **skip** や **top** のようなページング メソッドを記述することができます。

### <a name="paging"></a>ページにデータを返す

データは**常に**ページに返されます。  返されるレコードの最大数は、サーバーによって設定されます。  クライアントがさらにレコードを要求すると、サーバーは最大数のレコードを返します。  既定では、サーバーの最大ページ サイズは 50 レコードです。

1 番目の例として、テーブルから最初の 5 つの項目を選択する方法を示します。 このクエリは、 **ToDoItems**テーブルから項目を返します。 **mToDoTable** は、以前に作成したバックエンド テーブルへの参照です。

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

次に、最初の 5 つの項目をスキップしてその次の 5 つの項目を返すクエリを示します。

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

テーブル内のすべてのレコードを取得するには、コードを実装して、すべてのページを反復処理します。

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

このメソッドを使用したすべてのレコードの要求では、Mobile Apps バックエンドへの少なくとも 2 つの要求が作成されます。

> [!TIP]
> 適切なページ サイズは、要求実行時のメモリ使用量と、データを完全に受信するときの帯域幅の使用量と遅延と間のバランスです。  既定値 (50 個のレコード) は、あらゆるデバイスに適しています。  メモリがより大きいデバイスで操作する場合は、最大 500 に増やします。  500 レコードを超えるページ サイズを設定すると、許容できない遅延や大容量メモリの問題が発生することがわかっています。

### <a name="chaining"></a>方法:クエリ メソッドを連結する

バックエンド テーブルのクエリに使用するメソッドは連結できます。 クエリ メソッドを連結することで、フィルター処理した行を並べ替えてから、それらの行の特定の列を選択してページングを行うことができます。 複雑な論理フィルターも作成できます。  各クエリ メソッドでは、query オブジェクトが返されます。 一連のメソッドを完結させてクエリを実際に実行するには、 **execute** メソッドを呼び出します。 例:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

クエリ メソッドは次の順序で連結する必要があります。

1. フィルター処理 (**where**) メソッド
2. 並べ替え (**orderBy**) メソッド
3. 選択 (**select**) メソッド
4. ページング (**skip** と **top**) メソッド

## <a name="binding"></a>データをユーザー インターフェイスにバインドする

データ バインドには、次の 3 つのコンポーネントが関係します。

* データ ソース
* 画面レイアウト
* これらの 2 つを連結するアダプター

次のサンプル コードでは、Mobile Apps の SQL Azure テーブル **ToDoItem** のデータを配列に返します。 このアクティビティは、データ アプリケーションでは一般的なパターンです。  多くの場合、データベース クエリでは行のコレクションが返されます。クライアントは、これをリストまたは配列に取得します。 この例では、配列はデータ ソースです。  コードで、デバイスに表示されるデータのビューを定義する画面レイアウトを指定します。  これらの 2 つをアダプターによってバインドします。アダプターは、このコードでは **ArrayAdapter&lt;ToDoItem&gt;** クラスの拡張です。

#### <a name="layout"></a>レイアウトを定義する

レイアウトは、XML コードの複数のスニペットで定義されます。 既存のレイアウトがあると仮定して、次のコードでサーバーのデータを設定する **ListView** を表します。

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

前のコードの *listitem* 属性は、リスト内の個々の行のレイアウトの ID を指定します。 次のコードではチェック ボックスおよび関連テキストを指定し、リストの項目ごとに 1 回インスタンス化しています。 このレイアウトでは **id** フィールドが表示されないため、さらに複雑なレイアウトで、表示する追加のフィールドを指定します。 このコードは、**row_list_to_do.xml** ファイルに含まれています。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>アダプターを定義する
このビューのデータ ソースは **ToDoItem** の配列であるため、**ArrayAdapter&lt;ToDoItem&gt;** クラスからアダプターをサブクラス化します。 このサブクラスでは、**row_list_to_do** レイアウトを使用してすべての **ToDoItem** のビューを生成します。  コードでは、**ArrayAdapter&lt;E&gt;** クラスの拡張である次のクラスを定義します。

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

アダプターの **getView** メソッドをオーバーライドします。 例:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

次に示すように、このクラスのインスタンスを Activity 内で作成します。

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

ToDoItemAdapter コンストラクターの 2 つ目のパラメーターは、レイアウトへの参照にします。 これで、**ListView** をインスタンス化して、**ListView** にアダプターを割り当てられるようになりました。

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>UI にバインドするアダプターを使用する

これで、データ バインドを使用する準備が整いました。 次のコードで、テーブルの項目を取得して、返された項目をローカル アダプターに追加する方法を示します。

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

アダプターは、**ToDoItem** テーブルを変更するときは常に呼び出します。 変更はレコード単位で加えられるため、操作の対象はコレクションではなく単一行になります。 項目を挿入する場合は、アダプターの **add** メソッドを呼び出します。項目を削除する場合は、**remove** メソッドを呼び出します。

完全な例については、[Android クイックスタート プロジェクト][21]を参照してください。

## <a name="inserting"></a>バックエンドにデータを挿入する

*ToDoItem* クラスのインスタンスをインスタンス化し、そのプロパティを設定します。

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

次に、 **insert()** を使用してオブジェクトを挿入します。

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

返されるエンティティは、バックエンドで設定された ID および他の値 (`createdAt`、`updatedAt`、および `version` フィールドなど) を含め、バックエンド テーブルに挿入されたデータと一致します。

Mobile Apps テーブルでは、主キー列に **id**という名前を付ける必要があります。この列は、文字列である必要があります。 ID 列の既定値は GUID です。  電子メール アドレスやユーザー名など、別の一意の名前を指定することもできます。 挿入されたレコードに対して文字列 ID 値が指定されていない場合、バックエンドで新しい GUID が生成されます。

文字列 ID 値には次のような利点があります。

* データベースを往復しなくても ID を生成できます。
* 他のテーブルやデータベースのレコードをより簡単にマージできます。
* ID 値はより適切にアプリケーションのロジックと統合します。

オフライン同期をサポートする場合、String ID 値は **必須** です。  バックエンド データベースに一旦保存された ID は変更できません。

## <a name="updating"></a>モバイル アプリのデータを更新する

テーブルのデータを更新するには、新しいオブジェクトを **update()** メソッドに渡します。

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

この例では、*item* は、いくつかの変更が加えられた *ToDoItem* テーブルの行への参照です。  同じ **id** を持つ行が更新されます。

## <a name="deleting"></a>モバイル アプリのデータを削除する

次のコードでは、データ オブジェクトを指定することによってテーブルのデータを削除する方法を示します。

```java
mToDoTable
    .delete(item);
```

削除する行の **id** フィールドを指定して、項目を削除することもできます。

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>ID で特定の項目を検索する

特定の **id** フィールドを持つ項目を検索するには、**lookUp()** メソッドを使用します。

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>方法:型指定のないデータを処理する

型指定のないプログラミング モデルでは、JSON のシリアル化を正確に制御することができます。  一般的なシナリオの中には、型指定のないプログラミング モデルをした方が良いものもあります。 たとえば、バックエンド テーブルに多くの行が含まれているものの、列のサブセットを参照するだけでよい場合などです。  型指定されたモデルでは、Mobile Apps バックエンドで定義されたすべての列をデータ クラスに定義する必要があります。  データにアクセスするための API 呼び出しのほとんどは、型指定されたプログラミングでの呼び出しに似ています。 主な違いとして、型指定のないモデルでは、**MobileServiceTable** オブジェクトの代わりに **MobileServiceJsonTable** オブジェクトのメソッドを呼び出します。

### <a name="json_instance"></a>型指定のないテーブルのインスタンスを作成する

型指定されたモデルと同様、テーブル参照を取得することから始めますが、このケースでのオブジェクトは **MobileServicesJsonTable** です。 クライアントのインスタンスで **getTable()** メソッドを呼び出して、参照を取得します。

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

**MobileServiceJsonTable**のインスタンスを作成すると、このインスタンスでは、型指定のあるプログラミング モデルとほとんど同じ API を使用できるようになります。 一部のメソッドでは、型指定のあるパラメーターではなく型指定のないパラメーターを受け取ります。

### <a name="json_insert"></a>型指定のないテーブルに挿入する
次のコードは、挿入操作を行う方法を示しています。 最初に [JsonObject][1] を作成します。これは、[gson][3] ライブラリに含まれています。

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

次に、 **insert()** を使用して、型指定のないオブジェクトをテーブルに挿入します。

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

挿入したオブジェクトの ID を取得する必要がある場合は、 **getAsJsonPrimitive()** メソッドを使用します。

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>型指定のないテーブルから削除する
次のコードでは、インスタンス (このケースでは前の **insert** の例で作成したのと同じ *JsonObject* のインスタンス) を削除する方法を示します。 このコードは型指定されたものと同じですが、メソッドは **JsonObject**を参照するため署名が異なります。

```java
mToDoTable
    .delete(insertedItem);
```

ID を使用してインスタンスを直接削除することもできます。

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>型指定のないテーブルからすべての行を返す
次のコードは、テーブル全体を取得する方法を示しています。 JSON テーブルを使用しているため、テーブルの列の一部のみを選択的に取得できます。

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

型指定のないモデルでも、型指定のあるモデルで使用可能なものと同じフィルター処理メソッドとページング メソッドのセットを使用できます。

## <a name="offline-sync"></a>オフライン同期を実装する

Azure Mobile Apps クライアント SDK はオフライン同期も実装します。このためには、SQLite データベースを使用して、サーバー データのコピーをローカルに格納します。  オフライン テーブルで実行される操作では、モバイル接続は不要です。  オフライン同期は回復力とパフォーマンスに優れる一方で、競合の解決にはより複雑なロジックを必要とします。  Azure Mobile Apps クライアント SDK は、次の機能を実装します。

* 増分同期:更新されたレコードや新しいレコードのみがダウンロードされるため、帯域幅とメモリ消費量を節約できます。
* オプティミスティック コンカレンシー:操作は成功することが前提とされます。  競合の解決は、更新がサーバーで実行されるまで延期されます。
* 競合の解決:SDK が、サーバーで競合の変更が行われたことを検出して、ユーザーに通知するフックを提供します。
* 論理削除:削除されたレコードは削除済みとマークされ、他のデバイスはオフラインキャッシュを更新することができます。

### <a name="initialize-offline-sync"></a>オフライン同期を初期化する

各オフライン テーブルは、使用前にオフライン キャッシュで定義される必要があります。  通常、テーブルの定義は、クライアントの作成後すぐに行われます。

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>オフライン キャッシュ テーブルへの参照を取得する

オンライン テーブルでは `.getTable()` を使用しますが、  オフライン テーブルでは `.getSyncTable()` を使用します。

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

オンライン テーブルで使用できるすべてのメソッド (フィルター処理、並べ替え、ページング、データの挿入、データの更新、データの削除など) は、オンライン テーブルでもオフライン テーブルでも同じように正しく動作します。

### <a name="synchronize-the-local-offline-cache"></a>ローカルのオフライン キャッシュを同期する

同期は、アプリによって管理されます。  同期方法の例を以下に示します。

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

`.pull(query, queryname)` メソッドにクエリ名を指定した場合、増分同期を使用して、最後に正常に完了したプル以降に作成された、または変更されたレコードのみが返されます。

### <a name="handle-conflicts-during-offline-synchronization"></a>オフライン同期中に競合を処理する

`.push()` 操作時に競合が発生した場合、`MobileServiceConflictException` がスローされます。   サーバーが発行した項目が例外に埋め込まれており、例外で `.getItem()` によりこれを取得できます。  MobileServiceSyncContext オブジェクトで次の項目を呼び出して、プッシュを調整します。

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

必要に応じてすべての競合をマークしたら、もう一度 `.push()` を呼び出して、すべての競合を解決します。

## <a name="custom-api"></a>カスタム API を呼び出す

カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。 カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

Android クライアントから **invokeApi** メソッドを呼び出して、カスタム API エンドポイントを呼び出します。 次の例では、**completeAll** という名前の API エンドポイントを呼び出す方法を示します。このエンドポイントは、**MarkAllResult** という名前のコレクション クラスを返します。

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

POST 要求を新しいカスタム API に送信する **invokeApi** メソッドがクライアントで呼び出されます。 カスタム API から返された結果は、メッセージ ダイアログに表示されます。エラーが発生した場合はそれらも表示されます。 オプションとして、他のバージョンの **invokeApi** を使用すると、要求本文でのオブジェクトの送信、HTTP メソッドの指定、要求でのクエリ パラメーターの送信が可能です。 **invokeApi** の型指定なしバージョンも用意されています。

## <a name="authentication"></a>アプリに認証を追加する

これらの機能を追加する方法については、チュートリアルで既に詳しく説明されています。

App Service は、Facebook、Google、Microsoft アカウント、Twitter、Azure Active Directory などのさまざまな外部 ID プロバイダーを使用した[アプリ ユーザーの認証](app-service-mobile-android-get-started-users.md)をサポートします。 テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。 さらに、認証されたユーザーの ID を使用することにより、バックエンドで承認ルールを実装することもできます。

**サーバー** フローと**クライアント** フローの 2 つの認証フローがサポートされます。 サーバー フローでは ID プロバイダーの Web インターフェイスを使用するため、認証を最も簡単に行うことができます。  サーバー フロー認証の実装では SDK を追加する必要はありません。 サーバー フロー認証は、深いレベルでモバイル デバイスに統合することはできないため、概念実証シナリオでのみ使用することをお勧めします。

クライアント フローでは ID プロバイダー提供の SDK を利用するため、シングル サインオンなどのデバイス固有の機能とより深く統合を行うことができます。  たとえば、Facebook SDK をモバイル アプリケーションに統合することができます。  モバイル クライアントは、Facebook アプリへのスワップを行い、モバイル アプリに再びスワップする前にサインオンを確認します。

アプリで認証を有効にするには、次の 4 つの手順を実行する必要があります。

* 認証対象のアプリを ID プロバイダーに登録する。
* App Service バックエンドを構成する。
* テーブルへのアクセス許可を、App Service バックエンドの認証済みユーザーのみに制限する。
* アプリケーションに認証コードを追加します。

テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。 さらに、認証されたユーザーの SID を使用して要求を変更することもできます。  詳細については、 [認証の概要] に関するページと、Server SDK のハウツー ドキュメントを参照してください。

### <a name="caching"></a>認証:サーバー フロー

次のコードでは、Google プロバイダーを使用してサーバー フローのログイン プロセスを開始します。  Google プロバイダーのセキュリティ要件のため、追加の構成が必要です。

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

さらに、メインの Activity クラスに次のメソッドを追加します。

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

メイン Activity で定義された `GOOGLE_LOGIN_REQUEST_CODE` は、`login()` メソッドに対して、また `onActivityResult()` メソッド内で使用されます。  `login()` メソッドと `onActivityResult()` メソッド内で同じ数字が使用されていれば、あらゆる一意の数字を選択できます。  (前述したように) クライアント コードをサービス アダプターに要約した場合、サービス アダプターで適切なメソッドを呼び出す必要があります。

customtabs にプロジェクトを構成する必要もあります。  まず、リダイレクト URL を指定します。  次のスニペットを `AndroidManifest.xml` に追加します。

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

**redirectUriScheme** を、アプリケーションの `build.gradle` ファイルに追加します。

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

最後に、`build.gradle` ファイルの依存関係一覧に `com.android.support:customtabs:28.0.0` を追加します。

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

**getUserId** メソッドを使用して **MobileServiceUser** からログイン ユーザーの ID を取得します。 Futures を使用して非同期ログイン API を呼び出す方法の例については、「 [認証の概要]」を参照してください。

> [!WARNING]
> 説明されている URL スキームでは、大文字と小文字が区別されます。  `{url_scheme_of_you_app}` のすべての出現箇所で大文字と小文字を同じように使用してください。

### <a name="caching"></a>認証トークンをキャッシュする

認証トークンをキャッシュするには、ユーザー ID と認証トークンをデバイスにローカルで保存する必要があります。 アプリケーションの次回起動時にキャッシュを確認してください。これらの値が存在する場合は、ログイン手順を省略してクライアントにこのデータを再び渡すことができます。 ただし、このデータは慎重な扱いを要する情報であり、電話の盗難に備えて安全のために暗号化して保存する必要があります。  認証トークンをキャッシュする方法の完全な例については、「[認証トークンをキャッシュする][7]」を参照してください。

期限切れトークンを使用しようとすると、" *401 権限がありません* " 応答が返されます。 認証エラーはフィルターを使用して処理することができます。  フィルターにより、App Service バックエンドへの要求が遮断されます。 フィルター コードは、401 の応答の有無をテストし、サインイン プロセスをトリガーしてから、401 を生成した要求を再開します。

### <a name="refresh"></a>更新トークンを使用する

Azure App Service の認証および承認によって返されるトークンには、1 時間の有効期間が定義されています。  この期間が過ぎたら、ユーザーを再認証する必要があります。  クライアント フローの認証経由で受信した有効期間が長いトークンを使用する場合、同じトークンを使用して Azure App Service の認証および承認で再認証を行うことができます。  新しい有効期間で別の Azure App Service トークンが生成されます。

更新トークンを使用するようにプロバイダーを登録することもできます。  更新トークンを使用できない場合もあります。  追加の構成が必要になります。

* **Azure Active Directory** の場合、Azure Active Directory アプリのクライアント シークレットを構成します。  Azure Active Directory 認証を構成するときに、Azure App Service にクライアント シークレットを指定します。  `.login()` の呼び出し時に、パラメーターとして `response_type=code id_token` を渡します。

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* **Google** の場合、パラメーターとして `access_type=offline` を渡します。

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* **Microsoft アカウント**の場合、`wl.offline_access` スコープを選択します。

トークンを更新するには、`.refreshUser()` を呼び出します。

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

ベスト プラクティスとして、サーバーからの 401 応答を検出し、ユーザー トークンの更新を試行するフィルターを作成します。

## <a name="log-in-with-client-flow-authentication"></a>クライアント フローの認証でログインする

クライアント フロー認証を使用してログインするための一般的なプロセスは、次のとおりです。

* 従来のサーバー フローの認証と同様に、Azure App Service の認証と承認を構成します。
* 認証用の認証プロバイダー SDK を統合して、アクセス トークンを生成します。
* 次のように `.login()` メソッドを呼び出します (`result` は `AuthenticationResult` である必要があります)。

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

次のセクションで完全なコード例を参照してください。

`onSuccess()` メソッドを、正常なログインで使用する任意のコードに置き換えます。  `{provider}` 文字列は有効なプロバイダーです: **aad** (Azure Active Directory)、**facebook**、**google**、**microsoftaccount**、または **twitter**。  カスタム認証を実装している場合、カスタム認証プロバイダー タグも使用することができます。

### <a name="adal"></a>Active Directory Authentication Library (ADAL) を使用したユーザーの認証

Active Directory 認証ライブラリ (ADAL) を使用して、Azure Active Directory を使用しているアプリケーションにユーザーをサインインさせることができます。 クライアント フロー ログインでは、よりネイティブなユーザー エクスペリエンスを提供でき、詳細なカスタマイズを行うこともできるため、多くの場合、 `loginAsync()` メソッドよりもこちらのログインを使用することをお勧めします。

1. 「 [Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法][22] 」のチュートリアルに従って、AAD のサインイン用にモバイル アプリ バックエンドを構成します。 ネイティブ クライアント アプリケーションを登録する省略可能な手順を確実に実行します。
2. build.gradle ファイルを変更して以下の定義を追加し、ADAL をインストールします。

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. 以下のコードを次のように置換してから、アプリケーションに追加します。

    * **INSERT-AUTHORITY-HERE** を、アプリケーションをプロビジョニングしたテナントの名前に置き換えます。 形式は https://login.microsoftonline.com/contoso.onmicrosoft.com である必要があります。
    * **INSERT-RESOURCE-ID-HERE** を、モバイル アプリ バックエンドのクライアント ID に置き換えます。 クライアント ID は、ポータルの **[Azure Active Directory の設定]** の **[詳細]** タブで入手できます。
    * **INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。
    * **INSERT-REDIRECT-URI-HERE** を、HTTPS スキームを使用して、サイトの */.auth/login/done* エンドポイントに置き換えます。 これは、 *https://contoso.azurewebsites.net/.auth/login/done* のような値である必要があります。

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>クライアント/サーバー間通信を調整する

通常、クライアント接続は、Android SDK に付属する基になる HTTP ライブラリを使用した基本的な HTTP 接続です。  これを変更する理由はいくつかあります。

* 代替 HTTP ライブラリを使用してタイムアウトを調整したい場合
* 進行状況バーを提供したい場合
* API 管理機能をサポートするためにカスタム ヘッダーを追加したい場合
* 再認証を実装できるように、失敗した応答をインターセプトしたい場合
* 分析サービスにバックエンド要求を記録したい場合

### <a name="using-an-alternate-http-library"></a>代替 HTTP ライブラリの使用

クライアント リファレンスを作成した直後に、`.setAndroidHttpClientFactory()` メソッドを呼び出します。  接続タイムアウトを 60 秒 (既定の 10 秒ではなく) に設定する場合の例は、以下のとおりです。

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>進行状況フィルターを実装する

`ServiceFilter` を実装して、各要求のインターセプトを実装することができます。  たとえば、以下では、事前作成済みの進行状況バーが更新されます。

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

次のように、このフィルターをクライアントにアタッチできます。

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>要求ヘッダーをカスタマイズする

次の `ServiceFilter` を使用して、`ProgressFilter` と同じ方法でフィルターをアタッチします。

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>自動シリアル化の構成

[gson][3] API を使用すると、すべての列に適用される変換戦略を指定できます。 Android クライアント ライブラリでは、バックグラウンドで [gson][3] を使用して、Azure App Service へのデータ送信前に Java オブジェクトを JSON データにシリアル化します。  次のコードでは、 **setFieldNamingStrategy()** メソッドを使用して戦略を設定します。 この例では、すべてのフィールド名で最初の文字 ("m") を削除し、その次の文字を小文字にしています。 たとえば、"mId" は "id" になります。  ほぼすべてのフィールドで `SerializedName()` 注釈の必要性を減らすために変換戦略を実装します。

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

このコードは、**MobileServiceClient** を使用してモバイル クライアント リファレンスを作成する前に実行する必要があります。

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[認証の概要]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
