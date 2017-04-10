---
title: "DocumentDB の NoSQL C++ チュートリアル | Microsoft Docs"
description: "C++ 用の DocumentDB 動作保証済み SDK を使用して C++ データベースとコンソール アプリケーションを作成する NoSQL C++ チュートリアル。 DocumentDB は、世界規模の NoSQL データベース サービスです。"
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 78c3da6fd83a6fca0351a90846d10acd82924be3
ms.lasthandoff: 03/08/2017


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>NoSQL C++ チュートリアル: DocumentDB C++ コンソール アプリケーション
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [MongoDB 用 Node.js](documentdb-mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 
 

C++ 用の Azure DocumentDB 動作保証済み SDK の C++ チュートリアルへようこそ。 このチュートリアルでは、C++ データベースなどの DocumentDB リソースを作成してクエリするコンソール アプリケーションを作成します。

ここで説明する操作は以下のとおりです。

* DocumentDB アカウントを作成して接続する
* アプリケーションをセットアップする
* C++ DocumentDB データベースを作成する
* コレクションを作成する
* JSON ドキュメントを作成する
* コレクションをクエリする
* ドキュメントを置換する
* ドキュメントを削除する
* C++ DocumentDB データベースを削除する

時間がなくても 心配はありません。 [GitHub](https://github.com/stalker314314/DocumentDBCpp) で完全なソリューションを入手できます。 簡単な手順については「[完全なソリューションの取得](#GetSolution)」を参照してください。

C++ チュートリアルを完了した後で、このページの下部にある投票ボタンを使用して、フィードバックをお寄せください。 

マイクロソフトから直接ご連絡を差し上げて問題がなければ、コメント欄に電子メール アドレスをご記入いただくか、[こちら](https://www.research.net/r/8BKRJ3Z)からお問い合わせください。 

それでは始めましょう。

## <a name="prerequisites-for-the-c-tutorial"></a>C++ チュートリアルの前提条件
以下のものがそろっていることを確認してください。

* アクティブな Azure アカウント。 これがない場合は、 [Azure の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* C++ 言語のコンポーネントがインストールされた [Visual Studio](https://www.visualstudio.com/downloads/)。

## <a name="step-1-create-a-documentdb-account"></a>手順 1: DocumentDB アカウントを作成する
DocumentDB アカウントを作成しましょう。 使用するアカウントが既にある場合は、「[C++ アプリケーションをセットアップする](#SetupNode)」に進んでかまいません。

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupC++"></a>手順 2: C++ アプリケーションをセットアップする
1. Visual Studio を開き、**[ファイル]** メニューの **[新規作成]** をクリックした後、**[プロジェクト]** をクリックします。 
2. **[新しいプロジェクト]** ウィンドウの **[インストール済み]** ウィンドウで、**[Visual C++]** を展開し、**[Win32]** をクリックしてから、**[Win32 コンソール アプリケーション]** をクリックします。 プロジェクトに hellodocumentdb という名前を付け、**[OK]** をクリックします。 
   
    ![新しいプロジェクト ウィザードのスクリーン ショット](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. Win32 アプリケーション ウィザードが開始されたら、**[完了]** をクリックします。
4. プロジェクトが作成されたら、**ソリューション エクスプローラー**で **hellodocumentdb** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックして NuGet パッケージ マネージャーを開きます。 
   
    ![プロジェクト メニューの [NuGet パッケージの管理...] を示すスクリーン ショット](media/documentdb-cpp-get-started/nuget.png)
5. **[NuGet: hellodocumentdb]** タブで、**[参照]** をクリックし、*documentdbcpp* を検索します。 結果から、次のスクリーン ショットに示すように DocumentDbCPP を選択します。 このパッケージで、DocumentDbCPP の依存関係である C++ REST SDK への参照がインストールされます。  
   
    ![強調表示されている DocumentDbCpp パッケージを示すスクリーン ショット](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    パッケージがプロジェクトに追加されると、コードの記述し始める準備が整います。   

## <a id="Config"></a>手順 3: DocumentDB データベース用に Azure ポータルから接続の詳細をコピーする
[Azure ポータル](https://portal.azure.com)を表示し、作成した NoSQL (DocumentDB) データベース アカウントに移動します。 C++ のコード スニペットからの接続を確立するために、次の手順で Azure ポータルの URI と プライマリ キーが必要になります。 

![Azure ポータル内の DocumentDB URI と キー](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>手順 4: DocumentDB アカウントに接続する
1. ソース コードの `#include "stdafx.h"` の後に、次のヘッダーと名前空間を追加します。
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. 次に、main 関数に次のコードを追加し、手順 3. の DocumentDB の設定に合わせてアカウント構成とプライマリ キーを置き換えます。 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    documentdb クライアントを初期化するためのコードは以上で完成です。DocumentDB のリソースを使って動作を確認してみましょう。

## <a id="CreateDBColl"></a>手順 5: C++ データベースとコレクションを作成する
この手順を実行する前に、DocumentDB の初心者向けにデータベース、コレクション、およびドキュメントの相互関係を確認しておきましょう。 [データベース](documentdb-resources.md#databases)は、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。 [コレクション](documentdb-resources.md#collections)には、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。 DocumentDB 階層型リソース モデルと概念の詳細については、「[DocumentDB 階層型リソース モデルと概念](documentdb-resources.md)」をご覧ください。

データベースと、対応するコレクションを作成するには、main 関数の末尾に次のコードを追加します。 これにより、前の手順で宣言したクライアント構成を使用して、'FamilyRegistry' というデータベースと 'FamilyCollection' というコレクションが作成されます。

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>手順 6: ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 これで、DocumentDB にドキュメントを挿入できます。 ドキュメントを作成するには、main 関数の末尾に次のコードをコピーします。 

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

まとめると、このコードで、DocumentDB データベース、コレクション、およびドキュメントが作成されます。これらに対して、Azure ポータルのドキュメント エクスプローラーでクエリを実行できます。 

![C++ チュートリアル - アカウント、データベース、コレクション、およびドキュメントの間の階層関係を示す図](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a id="QueryDB"></a>手順 7: DocumentDB リソースをクエリする
DocumentDB では、各コレクションに格納された JSON ドキュメントに対する [リッチ クエリ](documentdb-sql-query.md) をサポートしています。 次のサンプル コードは、前の手順で作成したドキュメントに対して実行できるクエリを示しています。DocumentDB SQL 構文が使用されています。

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
DocumentDB は、次のコードに示すように、JSON ドキュメントの置換をサポートします。 このコードを executesimplequery 関数の後に追加します。

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
DocumentDB では、JSON ドキュメントの削除がサポートされています。次のコードをコピーして replacedocument 関数の後に貼り付けることで実行できます。 

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
このように、さまざまな DocumentDB リソースに対して作成、クエリ、変更、および削除を実行するためのコードを追加しました。  hellodocumentdb.cpp の main 関数からこれらのさまざまな関数への呼び出しを診断メッセージと共に追加してつないでみましょう。

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

![DocumentDB C++ アプリケーションの出力](media/documentdb-cpp-get-started/docdbconsole.png)

ご利用ありがとうございます。 以上で C++ チュートリアルが完了し、初めての DocumentDB コンソール アプリケーションが完成しました。

## <a id="GetSolution"></a>完全な C++ チュートリアル ソリューションを入手する
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

* [DocumentDB アカウント][documentdb-create-account]。
* GitHub で入手可能な [GetStarted](https://github.com/stalker314314/DocumentDBCpp) ソリューション。

## <a name="next-steps"></a>次のステップ
* [DocumentDB アカウントを監視する](documentdb-monitor-accounts.md)方法について学習します。
* [クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
* プログラミング モデルの詳細については、 [DocumentDB のドキュメントに関するページ](https://azure.microsoft.com/documentation/services/documentdb/)の「開発」セクションを参照してください。

[documentdb-create-account]: documentdb-create-account.md



