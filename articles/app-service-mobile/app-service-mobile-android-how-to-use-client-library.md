---
title: "Android Mobile Apps クライアント ライブラリを使用する方法"
description: "Azure Mobile Apps 向け Android クライアント SDK の使用方法。"
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: adrianha
editor: 
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 277df83870c9cbfcb2407999ca9bc6a799284abe
ms.lasthandoff: 03/09/2017


---
# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Mobile Apps 向け Android クライアント ライブラリの使用方法
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

このガイドでは、次のような一般的なシナリオを、Mobile Apps の Android クライアント SDK を使用して実装する方法について説明します。

* データに対するクエリ (挿入、更新、削除)
* 認証
* エラーの処理
* クライアントのカスタマイズ

また、ほとんどのモバイル アプリで使用される共通のクライアント コードについても詳しく説明します。

このガイドでは、クライアント側の Android SDK に注目します。  Mobile Apps 用のサーバー側 SDK の詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作][10]」または「[Azure Mobile Apps Node.js SDK の使用方法][11]」を参照してください。

## <a name="reference-documentation"></a>リファレンス ドキュメント
Android クライアント ライブラリの [API に関する javadoc リファレンス][12]は、GitHub にあります。

## <a name="supported-platforms"></a>サポートされているプラットフォーム
Azure Mobile Apps の Android SDK では、API レベル 19 から 24 まで (KitKat から Nougat) がサポートされます。  

"サーバーフロー" 認証では、UI の表示に WebView を使用します。 デバイスで WebView UI を表示できない場合は、この製品に含まれない別の認証方法が必要になります。  この SDK は、腕時計型デバイスのような制限付きデバイスには適していません。

## <a name="setup-and-prerequisites"></a>セットアップと前提条件
[Mobile Apps のクイックスタート](app-service-mobile-android-get-started.md) に関するページのチュートリアルを完了してください。  この作業により、Azure Mobile Apps の開発の前提条件がすべて満たされます。  このクイックスタートでは、アカウントを構成し、初めてのモバイル アプリ バックエンドを作成することもできます。

クイックスタート チュートリアルを行わない場合は、次の作業を完了してください。

* Andoroid アプリで使用する[モバイル アプリ バックエンド][13]を作成する。
* Android Studio で [Gradle ビルド ファイルを更新する](#gradle-build)。
* [インターネット アクセス許可を有効にする](#enable-internet)。

### <a name="gradle-build"></a>Gradle ビルド ファイルを更新する
2 つの **build.gradle** ファイルを変更します。

1. 次のコードを、"*プロジェクト*" レベルの **build.gradle** ファイルの *buildscript* タグ内に追加します。

        buildscript {
            repositories {
                jcenter()
            }
        }
2. 次のコードを、"*モジュール アプリ*" レベルの **build.gradle** ファイルの *dependencies* タグ内に追加します。

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    現在の最新バージョンは 3.1.0 です。 サポートされているバージョンの一覧については、[こちら][14]を参照してください。

### <a name="enable-internet"></a>インターネット アクセス許可を有効にする
Azure にアクセスするには、アプリで INTERNET アクセス許可が有効になっている必要があります。 まだ有効になっていない場合は、次のコード行を **AndroidManifest.xml** ファイルに追加します。

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>基本の詳細情報
このセクションでは、Quickstart アプリのコードのうちで、Azure Mobile Apps の使用に関係する部分について説明します。  

### <a name="data-object"></a>クライアント データ クラスを定義する
SQL Azure のテーブルからデータにアクセスするには、モバイル アプリ バックエンドのテーブルに対応するクライアント データ クラスを定義します。 このトピックの例では、次の列を含む、 **ToDoItem**という名前のテーブルがあるものとします。

* id
* text
* 完了

対応する型指定されたクライアント側オブジェクトは次のとおりです。

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

コードは、 **ToDoItem.java**という名前のファイル内に存在します。

SQL Azure テーブルにさらに列が含まれる場合は、対応するフィールドをこのクラスに追加します。  たとえば、DTO (データ転送オブジェクト) に整数の Priority 列がある場合は、次のフィールドを getter および setter メソッドと共に追加します。

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

Mobile Apps バックエンドに追加のテーブルを作成する方法については、「[方法: テーブル コントローラーを定義する][15]」(.NET バックエンド) または「[方法: 動的スキーマを使用してテーブルを定義する][16]」(Node.js バックエンド) を参照してください。 Node.js バックエンドでは、 **[テーブルの簡単操作]** の [Azure Portal]設定を使用することもできます。

### <a name="create-client"></a>方法: クライアント コンテキストを作成する
次のコードは、モバイル アプリ バックエンドにアクセスするために使用される **MobileServiceClient** オブジェクトを作成します。 このコードは、**MAIN** アクションと **LAUNCHER** カテゴリとして *AndroidManifest.xml* で指定された **Activity** クラスの `onCreate` メソッドにあります。 Quickstart のコードの場合は、 **ToDoActivity.java** ファイルにあります。

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

このコードで、`MobileAppUrl` をモバイル アプリ バックエンドの URL に置き換えます。この URL は、[Azure Portal]のモバイル アプリ バックエンド用ブレードで確認できます。 このコード行をコンパイルするには、次の **import** ステートメントも追加する必要があります。

    import com.microsoft.windowsazure.mobileservices.*;

### <a name="instantiating"></a>方法: テーブル参照を作成する
Java は厳密に型指定された言語であるため、バックエンドのデータを照会または変更する最も簡単な方法は、 *型指定されたプログラミング モデル*を使用することです。 こうしたモデルでは、クライアント オブジェクトとバックエンドの Azure SQL テーブル間でのデータ送信時に、[gson][3] ライブラリを使用して JSON をシームレスにシリアル化および非シリアル化することができます。

テーブルにアクセスするには、最初に [MobileServiceClient][9] の **getTable** メソッドを呼び出して [MobileServiceTable][8] オブジェクトを作成します。  このメソッドには&2; つのオーバーロードがあります。

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

次のコードで、**mClient** は MobileServiceClient オブジェクトへの参照です。  1 つ目のオーバーロードは、クラス名とテーブル名が同じ場合に使用されます。これは Quickstart で使用しているものです。

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

2 つ目のオーバーロードは、テーブル名とクラス名が異なる場合に使用されます。1 番目のパラメーターはテーブル名です。

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="binding"></a>方法: データをユーザー インターフェイスにバインドする
データ バインドには、次の&3; つのコンポーネントが関係します。

* データ ソース
* 画面レイアウト
* これらの&2; つを連結するアダプター

次のサンプル コードでは、Mobile Apps の SQL Azure テーブル **ToDoItem** のデータを配列に返します。 このアクティビティは、データ アプリケーションでは一般的なパターンです。  多くの場合、データベース クエリでは行のコレクションが返されます。クライアントは、これをリストまたは配列に取得します。 この例では、配列はデータ ソースです。

コードで、デバイスに表示されるデータのビューを定義する画面レイアウトを指定します。  これらの&2; つをアダプターによってバインドします。アダプターは、このコードでは **ArrayAdapter&lt;ToDoItem&gt;** クラスの拡張です。

#### <a name="layout"></a>方法: レイアウトを定義する
レイアウトは、XML コードの複数のスニペットで定義されます。 既存のレイアウトがあると仮定して、次のコードでサーバーのデータを設定する **ListView** を表します。

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

前のコードの *listitem* 属性は、リスト内の個々の行のレイアウトの ID を指定します。 次のコードではチェック ボックスおよび関連テキストを指定し、リストの項目ごとに&1; 回インスタンス化しています。 このレイアウトでは **id** フィールドが表示されないため、さらに複雑なレイアウトで、表示する追加のフィールドを指定します。 このコードは、**row_list_to_do.xml** ファイルに含まれています。

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


#### <a name="adapter"></a>方法: アダプターを定義する
このビューのデータ ソースは **ToDoItem** の配列であるため、**ArrayAdapter&lt;ToDoItem&gt;** クラスからアダプターをサブクラス化します。 このサブクラスでは、**row_list_to_do** レイアウトを使用してすべての **ToDoItem** のビューを生成します。

コードでは、**ArrayAdapter&lt;E&gt;** クラスの拡張である次のクラスを定義します。

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

アダプターの **getView** メソッドをオーバーライドします。 次に例を示します。

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

次に示すように、このクラスのインスタンスを Activity 内で作成します。

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

ToDoItemAdapter コンストラクターの&2; つ目のパラメーターは、レイアウトへの参照にします。 これで、**ListView** をインスタンス化して、**ListView** にアダプターを割り当てられるようになりました。

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>API の構造
Mobile Apps のテーブル操作とカスタム API 呼び出しは非同期に行われます。 クエリ、挿入、更新、および削除を行う非同期メソッドでは、[Future] オブジェクトと [AsyncTask] オブジェクトを使用します。 Future を使用すると、複数の入れ子になったコールバックを処理しなくてもバックグラウンド スレッドで簡単に複数の操作を実行できます。

サンプルについては、 **ToDoActivity.java** で Android クイックスタート プロジェクトに含まれる [Azure Portal] ファイルを参照してください。

#### <a name="use-adapter"></a>方法: アダプターを使用する
これで、データ バインドを使用する準備が整いました。 次のコードで、テーブルの項目を取得して、返された項目をローカル アダプターに追加する方法を示します。

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

アダプターは、**ToDoItem** テーブルを変更するときは常に呼び出します。 変更はレコード単位で加えられるため、操作の対象はコレクションではなく単一行になります。 項目を挿入する場合は、アダプターの **add** メソッドを呼び出します。項目を削除する場合は、**remove** メソッドを呼び出します。

## <a name="querying"></a>方法: モバイル アプリ バックエンドのデータを照会する
このセクションでは、モバイル アプリ バックエンドにクエリを発行する方法について説明します。これには次のタスクが含まれます。

* [すべての項目を返す]
* [返されるデータをフィルター処理する]
* [返されるデータを並べ替える]
* [ページにデータを返す]
* [特定の列を選択する]
* [クエリ メソッドを連結する](#chaining)

### <a name="showAll"></a>方法: テーブルからすべての項目を返す
次のクエリは、 **ToDoItem** テーブル内のすべての項目を返します。

    List<ToDoItem> results = mToDoTable.execute().get();

*results* 変数は、クエリの結果セットをリストとして返します。

### <a name="filtering"></a>方法: 返されるデータをフィルター処理する
次のクエリを実行すると、**ToDoItem** テーブルで **complete** が **false** と等しい項目がすべて返されます。

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** は、既に作成したモバイル サービス テーブルへの参照です。

テーブル参照で **where** メソッド呼び出しを使用してフィルターを定義します。 **where** メソッドの次に **field** メソッドを呼び出してから、論理述語を指定するメソッドを呼び出します。 使用できる述語メソッドとしては、**eq** (等しい)、**ne** (等しくない)、**gt** (より大きい)、**ge** (以上)、**lt** (より小さい)、**le** (以下) などがあります。 これらのメソッドを使用して、数値フィールドおよび文字列フィールドを特定の値と比較できます。

日付をフィルター処理できます。 次のメソッドを使用すると、日付フィールド全体または日付の一部を比較できます: **year**、**month**、**day**、**hour**、**minute**、**second**。 次の例では、期限 ( *due* ) が 2013 に等しい項目のフィルターを追加しています。

    mToDoTable.where().year("due").eq(2013).execute().get();

**startsWith**、**endsWith**、**concat**、**subString**、**indexOf**、**replace**、**toLower**、**toUpper**、**trim**、**length** などのメソッドは、文字列フィールドに対する複雑なフィルターをサポートします。 次の例では、 *text* 列が "PRI0" で始まるテーブル行をフィルター処理します。

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

数値フィールドでは、**add**、**sub**、**mul**、**div**、**mod**、**floor**、**ceiling**、**round** 演算子メソッドがサポートされます。 次の例では、 **duration** が偶数のテーブル行をフィルター処理します。

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

論理メソッド **and**、**or**、**not** を使用すると、述語を結合できます。 次の例では、前の&2; つの例を結合しています。

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

論理演算子をグループ化し、入れ子にします。

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

フィルター処理の詳細と例については、「 [Exploring the richness of the Android client query model (Android クライアント クエリ モデルの機能を調査する)](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)」を参照してください。

### <a name="sorting"></a>方法: 返されるデータを並べ替える
次のコードは、 **ToDoItems** テーブルから、 *text* フィールドの値に基づいて昇順に並べ替えられたすべての項目を返します。 *mToDoTable* は、以前に作成したバックエンド テーブルへの参照です。

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

**orderBy** メソッドの&1; つ目のパラメーターは、並べ替えに使用するフィールドの名前に等しい文字列です。 2 番目のパラメーターでは、 **QueryOrder** 列挙を使用して、昇順または降順のどちらで並べ替えを行うかを指定します。  ***where*** メソッドを使用してフィルター処理を行う場合、***where*** メソッドは ***orderBy*** メソッドより前に呼び出す必要があります。

### <a name="paging"></a>方法: ページにデータを返す
1 番目の例として、テーブルから最初の&5; つの項目を選択する方法を示します。 このクエリは、 **ToDoItems**テーブルから項目を返します。 **mToDoTable** は、以前に作成したバックエンド テーブルへの参照です。

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


次に、最初の&5; つの項目をスキップしてその次の&5; つの項目を返すクエリを示します。

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>方法: 特定の列を選択する
次のコードは、**ToDoItems** テーブルからすべての項目を返したうえで **complete** フィールドと **text** フィールドのみを表示する方法を示しています。 **mToDoTable** は、以前に作成したバックエンド テーブルへの参照です。

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

select 関数のパラメーターは、取得するテーブルの列の文字列名です。

**select** メソッドは、**where** や **orderBy** のようなメソッドの後に記述する必要があります。 このメソッドの後に **top**のようなページング メソッドを記述することができます。

### <a name="chaining"></a>方法: クエリ メソッドを連結する
バックエンド テーブルのクエリに使用するメソッドは連結できます。 クエリ メソッドを連結することで、フィルター処理した行を並べ替えてから、それらの行の特定の列を選択してページングを行うことができます。 複雑な論理フィルターも作成できます。
各クエリ メソッドでは、query オブジェクトが返されます。 一連のメソッドを完結させてクエリを実際に実行するには、 **execute** メソッドを呼び出します。 次に例を示します。

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

クエリ メソッドは次の順序で連結する必要があります。

1. フィルター処理 (**where**) メソッド
2. 並べ替え (**orderBy**) メソッド
3. 選択 (**select**) メソッド
4. ページング (**skip** と **top**) メソッド

## <a name="inserting"></a>方法: バックエンドにデータを挿入する
*ToDoItem* クラスのインスタンスをインスタンス化し、そのプロパティを設定します。

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

次に、 **insert()** を使用してオブジェクトを挿入します。

    ToDoItem entity = mToDoTable.insert(item).get();

返されるエンティティは、バックエンドで設定された ID および他の値を含め、バックエンド テーブルに挿入されたデータと一致します。

Mobile Apps テーブルでは、主キー列に **id**という名前を付ける必要があります。 既定では、この列は文字列です。 ID 列の既定値は GUID です。  電子メール アドレスやユーザー名など、別の一意の名前を指定することもできます。 挿入されたレコードに対して文字列 ID 値が指定されていない場合、バックエンドで新しい GUID が生成されます。

文字列 ID 値には次のような利点があります。

* データベースを往復しなくても ID を生成できます。
* 他のテーブルやデータベースのレコードをより簡単にマージできます。
* ID 値はより適切にアプリケーションのロジックと統合します。

オフライン同期をサポートする場合、String ID 値は **必須** です。

## <a name="updating"></a>方法: モバイル アプリのデータを更新する
テーブルのデータを更新するには、新しいオブジェクトを **update()** メソッドに渡します。

    mToDoTable.update(item).get();

この例では、*item* は、いくつかの変更が加えられた *ToDoItem* テーブルの行への参照です。
同じ **id** を持つ行が更新されます。

## <a name="deleting"></a>方法: モバイル アプリのデータを削除する
次のコードでは、データ オブジェクトを指定することによってテーブルのデータを削除する方法を示します。

    mToDoTable.delete(item);

削除する行の **id** フィールドを指定して、項目を削除することもできます。

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
       mToDoTable.delete(myRowId);

## <a name="lookup"></a>方法: 特定の項目を検索する
特定の **id** フィールドを持つ項目を検索するには、**lookUp()** メソッドを使用します。

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

## <a name="untyped"></a>方法: 型指定のないデータを処理する
型指定のないプログラミング モデルでは、JSON のシリアル化を正確に制御することができます。  一般的なシナリオの中には、型指定のないプログラミング モデルをした方が良いものもあります。 たとえば、バックエンド テーブルに多くの行が含まれているものの、列のサブセットを参照するだけでよい場合などです。  型指定されたモデルでは、モバイル アプリ テーブルのすべての列をデータ クラスに定義する必要があります。  

データにアクセスするための API 呼び出しのほとんどは、型指定されたプログラミングでの呼び出しに似ています。 主な違いとして、型指定のないモデルでは、**MobileServiceTable** オブジェクトの代わりに **MobileServiceJsonTable** オブジェクトのメソッドを呼び出します。

### <a name="json_instance"></a>方法: 型指定のないテーブルのインスタンスを作成する
型指定されたモデルと同様、テーブル参照を取得することから始めますが、このケースでのオブジェクトは **MobileServicesJsonTable** です。 クライアントのインスタンスで **getTable()** メソッドを呼び出して、参照を取得します。

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

**MobileServiceJsonTable**のインスタンスを作成すると、このインスタンスでは、型指定のあるプログラミング モデルとほとんど同じ API を使用できるようになります。 一部のメソッドでは、型指定のあるパラメーターではなく型指定のないパラメーターを受け取ります。

### <a name="json_insert"></a>方法: 型指定のないテーブルに挿入する
次のコードは、挿入操作を行う方法を示しています。 最初に [JsonObject][1] を作成します。これは、[gson][3] ライブラリに含まれています。

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

次に、 **insert()** を使用して、型指定のないオブジェクトをテーブルに挿入します。

    mJsonToDoTable.insert(jsonItem).get();

挿入したオブジェクトの ID を取得する必要がある場合は、 **getAsJsonPrimitive()** メソッドを使用します。

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>方法: 型指定のないテーブルから削除する
次のコードでは、インスタンス (このケースでは前の **insert** の例で作成したのと同じ *JsonObject* のインスタンス) を削除する方法を示します。 このコードは型指定されたものと同じですが、メソッドは **JsonObject**を参照するため署名が異なります。

         mToDoTable.delete(jsonItem);

ID を使用してインスタンスを直接削除することもできます。

         mToDoTable.delete(ID);

### <a name="json_get"></a>方法: 型指定のないテーブルからすべての行を返す
次のコードは、テーブル全体を取得する方法を示しています。 JSON テーブルを使用しているため、テーブルの列の一部のみを選択的に取得できます。

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

型指定のないモデルでも、型指定のあるモデルで使用可能なものと同じフィルター処理メソッドとページング メソッドのセットを使用できます。

## <a name="custom-api"></a>方法: カスタム API の呼び出し
カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。 カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

Android クライアントから **invokeApi** メソッドを呼び出して、カスタム API エンドポイントを呼び出します。 次の例では、**completeAll** という名前の API エンドポイントを呼び出す方法を示します。このエンドポイントは、**MarkAllResult** という名前のコレクション クラスを返します。

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

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

POST 要求を新しいカスタム API に送信する **invokeApi** メソッドがクライアントで呼び出されます。 カスタム API から返された結果は、メッセージ ダイアログに表示されます。エラーが発生した場合はそれらも表示されます。 オプションとして、他のバージョンの **invokeApi** を使用すると、要求本文でのオブジェクトの送信、HTTP メソッドの指定、要求でのクエリ パラメーターの送信が可能です。 **invokeApi** の型指定なしバージョンも用意されています。

## <a name="authentication"></a>方法: アプリに認証を追加する
これらの機能を追加する方法については、チュートリアルで既に詳しく説明されています。

App Service は、Facebook、Google、Microsoft アカウント、Twitter、Azure Active Directory などのさまざまな外部 ID プロバイダーを使用した [アプリ ユーザーの認証](app-service-mobile-android-get-started-users.md) をサポートします。 テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。 さらに、認証されたユーザーの ID を使用することにより、バックエンドで承認ルールを実装することもできます。

**サーバー** フローと**クライアント** フローの&2; つの認証フローがサポートされます。 サーバー フローでは ID プロバイダーの Web インターフェイスを使用するため、認証を最も簡単に行うことができます。  サーバー フロー認証の実装では SDK を追加する必要はありません。 サーバー フロー認証は、深いレベルでモバイル デバイスに統合することはできないため、概念実証シナリオでのみ使用することをお勧めします。

クライアント フローでは ID プロバイダー提供の SDK を利用するため、シングル サインオンなどのデバイス固有の機能とより深く統合を行うことができます。  たとえば、Facebook SDK をモバイル アプリケーションに統合することができます。  モバイル クライアントは、Facebook アプリへのスワップを行い、モバイル アプリに再びスワップする前にサインオンを確認します。

アプリで認証を有効にするには、次の&4; つの手順を実行する必要があります。

* 認証対象のアプリを ID プロバイダーに登録する。
* App Service バックエンドを構成する。
* テーブルへのアクセス許可を、App Service バックエンドの認証済みユーザーのみに制限する。
* アプリケーションに認証コードを追加します。

テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。 さらに、認証されたユーザーの SID を使用して要求を変更することもできます。  詳細については、 [認証の概要] に関するページと、Server SDK のハウツー ドキュメントを参照してください。

### <a name="caching"></a>方法: アプリケーションに認証コードを追加する
次のコードでは、Google プロバイダーを使用してサーバー フローのログイン プロセスを開始します。

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

**getUserId** メソッドを使用して **MobileServiceUser** からログイン ユーザーの ID を取得します。 Futures を使用して非同期ログイン API を呼び出す方法の例については、「 [認証の概要]」を参照してください。

### <a name="caching"></a>方法: 認証トークンをキャッシュする
認証トークンをキャッシュするには、ユーザー ID と認証トークンをデバイスにローカルで保存する必要があります。 アプリケーションの次回起動時にキャッシュを確認してください。これらの値が存在する場合は、ログイン手順を省略してクライアントにこのデータを再び渡すことができます。 ただし、このデータは慎重な扱いを要する情報であり、電話の盗難に備えて安全のために暗号化して保存する必要があります。

認証トークンをキャッシュする方法の完全な例については、「[方法: 認証トークンをキャッシュする][7]」セクションを参照してください。

期限切れトークンを使用しようとすると、" *401 権限がありません* " 応答が返されます。 認証エラーはフィルターを使用して処理することができます。  フィルターにより、App Service バックエンドへの要求が遮断されます。 フィルター コードは、401 の応答の有無をテストし、サインイン プロセスをトリガーしてから、401 を生成した要求を再開します。

## <a name="adal"></a>方法: Active Directory 認証ライブラリを使用してユーザーを認証する
Active Directory 認証ライブラリ (ADAL) を使用して、Azure Active Directory を使用しているアプリケーションにユーザーをサインインさせることができます。 クライアント フロー ログインでは、よりネイティブなユーザー エクスペリエンスを提供でき、詳細なカスタマイズを行うこともできるため、多くの場合、 `loginAsync()` メソッドよりもこちらのログインを使用することをお勧めします。

1. 「 [Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](app-service-mobile-how-to-configure-active-directory-authentication.md) 」のチュートリアルに従って、AAD のサインイン用にモバイル アプリ バックエンドを構成します。 ネイティブ クライアント アプリケーションを登録する省略可能な手順を確実に実行します。
2. build.gradle ファイルを変更して以下の定義を追加し、ADAL をインストールします。

```
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
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. 以下のコードを次のように置換してから、アプリケーションに追加します。

* **INSERT-AUTHORITY-HERE** を、アプリケーションをプロビジョニングしたテナントの名前に置き換えます。 形式は、https://login.windows.net/contoso.onmicrosoft.com のようになります。 この値は、[Azure クラシック ポータル] の Azure Active Directory の [ドメイン] タブからコピーできます。
* **INSERT-RESOURCE-ID-HERE** を、モバイル アプリ バックエンドのクライアント ID に置き換えます。 クライアント ID は、ポータルの **[Azure Active Directory の設定]** の **[詳細]** タブで入手できます。
* **INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。
* **INSERT-REDIRECT-URI-HERE** を、HTTPS スキームを使用して、サイトの */.auth/login/done* エンドポイントに置き換えます。 この値は、*https://contoso.azurewebsites.net/.auth/login/done* のようにする必要があります。

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

## <a name="how-to-add-push-notification-to-your-app"></a>方法: アプリにプッシュ通知を追加する
Microsoft Azure Notification Hubs によるさまざまなプッシュ通知のサポート方法については、[概要][6]に関するページをご確認ください。  [こちらのチュートリアル][5]では、レコードが挿入されるたびにすべてのデバイスに対してプッシュ通知を送信します。

## <a name="how-to-add-offline-sync-to-your-app"></a>方法: アプリにオフライン同期を追加する
クイックスタート チュートリアルには、オフライン同期を実装するコードが含まれます。 次のコメントが付いたコードを探します。

    // Offline Sync

次のコード行のコメントを解除することによりオフライン同期を実装することができ、他の Mobile Apps コードに同様のコードを追加できます。

## <a name="customizing"></a>方法: クライアントをカスタマイズする
クライアントの既定の動作は、さまざまな方法でカスタマイズできます。

### <a name="headers"></a>方法: 要求ヘッダーをカスタマイズする
各要求にカスタム HTTP 要求を追加するように **ServiceFilter** を構成します。

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>方法: シリアル化をカスタマイズする
クライアントでは、バックエンドのテーブル名、列名、データ型がすべて、クライアントで定義されているデータ オブジェクトと正確に一致するものと想定されています。 ただし、さまざまな理由により、サーバーとクライアントの間で名前が一致しない場合があります。 実際のシナリオでは、次の種類のカスタマイズを行うことができます。

* App Service テーブルで使用されている列名がクライアントで使用されている名前と一致しない。
* クライアントでマップされているクラスと異なる名前を持つ App Service テーブルを使用する。
* プロパティの大文字の自動設定を有効にする。
* 複合プロパティをオブジェクトに追加する。

### <a name="columns"></a>方法: クライアントとサーバーの間で異なる名前をマップする
Java クライアント コードで、次のプロパティのような **ToDoItem** オブジェクト プロパティに標準の Java 形式の名前を使用しているとします。

* mId
* mText
* mComplete
* mDuration

クライアントの名前を、サーバー上の **ToDoItem** テーブルの列名と一致する JSON 名にシリアル化します。 次のコードでは、[gson][3] ライブラリを使用してプロパティに注釈を付けています。

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>方法: クライアントとバックエンドの間で異なるテーブル名をマップする
クライアント テーブルの名前を別のモバイル サービス テーブルの名前にマップするには、[getTable()][4] メソッドのオーバーライドを使用します。

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>方法: 列名のマッピングを自動化する
[gson][3] API を使用すると、すべての列に適用される変換戦略を指定できます。 Android クライアント ライブラリでは、バックグラウンドで [gson][3] を使用して、Azure App Service へのデータ送信前に Java オブジェクトを JSON データにシリアル化します。  次のコードでは、 **setFieldNamingStrategy()** メソッドを使用して戦略を設定します。 この例では、すべてのフィールド名で最初の文字 ("m") を削除し、その次の文字を小文字にしています。 たとえば、"mId" は "id" になります。

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

このコードは、 **MobileServiceClient**を使用する前に実行する必要があります。

### <a name="complex"></a>方法: オブジェクトまたは配列プロパティをテーブルに保存する
これまでのシリアル化の例では、整数や文字列などのプリミティブ型を使用していました。  プリミティブ型は、簡単に JSON にシリアル化することができます。  自動で JSON にシリアル化されない複雑なオブジェクトを追加するには、JSON のシリアル化方法を用意する必要があります。  JSON シリアル化のカスタマイズ方法の例については、「[Customizing serialization using the gson library in the Mobile Services Android client (Mobile Services Andoroid クライアントで gson ライブラリを使用してシリアル化をカスタマイズする)][2]」を参照してください。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[すべての項目を返す]: #showAll
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure Portal]: https://portal.azure.com
[認証の概要]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html

