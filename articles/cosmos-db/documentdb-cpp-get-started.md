---
title: "Azure Cosmos DB の C++ チュートリアル | Microsoft Docs"
description: "C++ 用の Azure Cosmos DB 動作保証済み SDK を使用して C++ データベースとコンソール アプリケーションを作成する C++ チュートリアル。 Azure Cosmos DB は、世界規模のデータベース サービスです。"
services: cosmos-db
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 12/25/2016
ms.author: aasthan
ms.openlocfilehash: 324b5844362c2712d54fd766eb95e1d9102f757a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-documentdb-api"></a>Azure Cosmos DB: DocumentDB API 用の C++ コンソール アプリケーションのチュートリアル
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB 用 Node.js](mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 
 

C++ 用の Azure Cosmos DB DocumentDB API 動作保証済み SDK の C++ チュートリアルへようこそ。 このチュートリアルに従うと、C++ データベースなどの Azure Cosmos DB リソースを作成してクエリを実行するコンソール アプリケーションが作成されます。

ここで説明する操作は以下のとおりです。

* Azure Cosmos DB アカウントを作成し、アカウントに接続する
* アプリケーションをセットアップする
* C++ Azure Cosmos DB データベースを作成する
* コレクションを作成する
* JSON ドキュメントを作成する
* コレクションをクエリする
* ドキュメントを置換する
* ドキュメントを削除する
* C++ Azure Cosmos DB データベースを削除する

時間がなくても 心配はありません。 [GitHub](https://github.com/stalker314314/DocumentDBCpp) で完全なソリューションを入手できます。 簡単な手順については「[完全なソリューションの取得](#GetSolution)」を参照してください。

C++ チュートリアルを完了した後で、このページの下部にある投票ボタンを使用して、フィードバックをお寄せください。 

マイクロソフトから直接ご連絡を差し上げて問題がなければ、コメント欄に電子メール アドレスをご記入いただくか、[こちら](https://www.research.net/r/8BKRJ3Z)からお問い合わせください。 

それでは始めましょう。

## <a name="prerequisites-for-the-c-tutorial"></a>C++ チュートリアルの前提条件
以下のものがそろっていることを確認してください。

* アクティブな Azure アカウント。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* C++ 言語のコンポーネントがインストールされた [Visual Studio 2017](https://www.visualstudio.com/downloads/)。 まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>手順 1: Azure Cosmos DB アカウントを作成する
それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、「[C++ アプリケーションをセットアップする](#SetupC++)」に進んでかまいません。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>手順 2: C++ アプリケーションをセットアップする
1. Visual Studio を開き、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。 
2. **[新しいプロジェクト]** ウィンドウの **[インストール済み]** ウィンドウで、**[Visual C++]** を展開し、**[Win32]** をクリックしてから、**[Win32 コンソール アプリケーション]** をクリックします。 プロジェクトに hellodocumentdb という名前を付け、**[OK]** をクリックします。 
   
    ![新しいプロジェクト ウィザードのスクリーン ショット](media/documentdb-cpp-get-started/hello.png)
3. Win32 アプリケーション ウィザードが開始されたら、**[完了]** をクリックします。
4. プロジェクトが作成されたら、**ソリューション エクスプローラー**で **hellodocumentdb** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックして NuGet パッケージ マネージャーを開きます。 
   
    ![プロジェクト メニューの [NuGet パッケージの管理...] を示すスクリーン ショット](media/documentdb-cpp-get-started/nuget.png)
5. **[NuGet: hellodocumentdb]** タブで、**[参照]** をクリックし、*documentdbcpp* を検索します。 結果から、次のスクリーン ショットに示すように DocumentDbCPP を選択します。 このパッケージで、DocumentDbCPP の依存関係である C++ REST SDK への参照がインストールされます。  
   
    ![強調表示されている DocumentDbCpp パッケージを示すスクリーン ショット](media/documentdb-cpp-get-started/cpp.png)
   
    パッケージがプロジェクトに追加されると、コードの記述し始める準備が整います。   

## <a id="Config"></a>手順 3: Azure Cosmos DB データベース用に Azure Portal から接続の詳細をコピーする
[Azure Portal](https://portal.azure.com) を表示し、作成した Azure Cosmos DB データベース アカウントに移動します。 C++ のコード スニペットからの接続を確立するために、次の手順で Azure ポータルの URI と プライマリ キーが必要になります。 

![Azure Portal 内の Azure Cosmos DB URI とキー](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>手順 4: Azure Cosmos DB アカウントに接続する
1. ソース コードの `#include "stdafx.h"` の後に、次のヘッダーと名前空間を追加します。
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. 次に、main 関数に次のコードを追加し、手順 3. の Azure Cosmos DB の設定に合わせてアカウント構成とプライマリ キーを置き換えます。 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    documentdb クライアントを初期化するためのコードは以上で完成です。続いて、Azure Cosmos DB リソースの使用方法について説明します。

## <a id="CreateDBColl"></a>手順 5: C++ データベースとコレクションを作成する
この手順を実行する前に、Azure Cosmos DB の初心者向けにデータベース、コレクション、ドキュメントの相互関係を確認しておきましょう。 [データベース](documentdb-resources.md#databases)は、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。 [コレクション](documentdb-resources.md#collections)には、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。 Azure Cosmos DB 階層型リソース モデルと概念の詳細については、[Azure Cosmos DB 階層型リソース モデルと概念](documentdb-resources.md)に関するページを参照してください。

データベースと、対応するコレクションを作成するには、main 関数の末尾に次のコードを追加します。 これにより、前の手順で宣言したクライアント構成を使用して、'FamilyRegistry' というデータベースと 'FamilyCollection' というコレクションが作成されます。

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>手順 6: ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 これで、Azure Cosmos DB にドキュメントを挿入できます。 ドキュメントを作成するには、main 関数の末尾に次のコードをコピーします。 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

まとめると、このコードで、Azure Cosmos DB データベース、コレクション、ドキュメントが作成されます。これらに対して、Azure Portal のドキュメント エクスプローラーでクエリを実行できます。 

![C++ チュートリアル - アカウント、データベース、コレクション、およびドキュメントの間の階層関係を示す図](media/documentdb-cpp-get-started/docs.png)

## <a id="QueryDB"></a>手順 7: Azure Cosmos DB リソースにクエリを実行する
Azure Cosmos DB では、各コレクションに格納された JSON ドキュメントに対する[豊富なクエリ](documentdb-sql-query.md)がサポートされています。 次のサンプル コードは、前の手順で作成したドキュメントに対して実行できる SQL 構文を使用したクエリを示しています。

この関数は、ドキュメント クライアントと共に、データベースとコレクションの一意の識別子またはリソース id を引数として受け取ります。 main 関数の前にこのコードを追加します。

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>手順 8: ドキュメントを置換する
Azure Cosmos DB は、次のコードに示すように、JSON ドキュメントの置換をサポートします。 このコードを executesimplequery 関数の後に追加します。

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>手順 9: ドキュメントを削除する
Azure Cosmos DB では、JSON ドキュメントの削除がサポートされています。次のコードをコピーして replacedocument 関数の後に貼り付けることで実行できます。 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>手順 10: データベースを削除する
作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。

次のコード スニペット (cleanup 関数) をコピーして deletedocument 関数の後に貼り付けます。この関数によって、データベースとすべての子リソースが削除されます。

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>手順 11: C++ アプリケーションの全体的な実行の流れ
このように、さまざまな Azure Cosmos DB リソースの作成、クエリ、変更、削除を実行するためのコードを追加しました。  hellodocumentdb.cpp の main 関数からこれらのさまざまな関数への呼び出しを診断メッセージと共に追加してつないでみましょう。

アプリケーションの main 関数を次のコードに置き換えると、これを実行することができます。 これは、手順 3. でコードにコピーした account_configuration_uri と primary_key を上書きします。そのため、該当する行を保存しておくか、ポータルからもう一度その値をコピーします。 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Visual Studio で F5 キーを押すか、ターミナル ウィンドウでアプリケーションを検索し実行可能ファイルを実行することで、コードをビルドして実行できます。 

開始したアプリケーションの出力が表示されます。 出力は、次のスクリーンショットに一致します。

![Azure Cosmos DB C++ アプリケーションの出力](media/documentdb-cpp-get-started/console.png)

ご利用ありがとうございます。 C++ チュートリアルが完了し、初めての Azure Cosmos DB コンソール アプリケーションが完成しました。

## <a id="GetSolution"></a>完全な C++ チュートリアル ソリューションを入手する
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

* [Azure Cosmos DB アカウント][create-account]。
* GitHub で入手可能な [GetStarted](https://github.com/stalker314314/DocumentDBCpp) ソリューション。

## <a name="next-steps"></a>次のステップ
* [Azure Cosmos DB アカウントを監視する](monitor-accounts.md)方法を学習します。
* [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
* プログラミング モデルの詳細については、[Azure Cosmos DB のドキュメントに関するページ](https://azure.microsoft.com/documentation/services/documentdb/)の開発に関するセクションを参照してください。

[create-account]: create-documentdb-dotnet.md#create-account


