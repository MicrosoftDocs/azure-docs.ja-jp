---
title: C++ クライアント ライブラリを使用して Azure Storage リソースを一覧表示する
description: C++ 用 Microsoft Azure Storage クライアント ライブラリの一覧取得 API を使用して、コンテナー、BLOB、キュー、テーブル、エンティティを列挙する方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: dineshm
ms.openlocfilehash: 0f9e80aff20c1b2663491f6d6ceb99aaec58230f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74269448"
---
# <a name="list-azure-storage-resources-in-c"></a>C++ での Azure Storage のリソース一覧の取得

一覧取得操作は、Azure Storage を使用する多くの開発シナリオで重要です。 この記事では、C++ 用 Microsoft Azure Storage クライアント ライブラリで提供される一覧取得 API を使用して、Azure Storage 内のオブジェクトを最も効率的に列挙する方法について説明します。

> [!NOTE]
> このガイドは、C++ 用 Azure Storage クライアント ライブラリのバージョン 2.x を対象としています。このライブラリは、 [NuGet](https://www.nuget.org/packages/wastorage) または [GitHub](https://github.com/Azure/azure-storage-cpp) から入手できます。

ストレージ クライアント ライブラリを使用すると、Azure Storage 内のオブジェクトをさまざまな方法で一覧取得または照会することができます。 この記事では、以下のシナリオを扱います。

* アカウント内のコンテナーを一覧取得する
* コンテナーまたは仮想 BLOB ディレクトリ内の BLOB の一覧を取得する
* アカウント内のキューの一覧を取得する
* アカウント内のテーブルの一覧を取得する
* テーブル内のエンティティを照会する

これらの各方法については、シナリオごとに異なるオーバーロードを使用して示します。

## <a name="asynchronous-versus-synchronous"></a>非同期と同期

C++ 用ストレージ クライアント ライブラリは [C++ REST ライブラリ](https://github.com/Microsoft/cpprestsdk)上に構築されているため、[pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html) を使用した非同期操作が基本的にサポートされます。 例:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

同期操作は、対応する非同期操作をラップします。

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

マルチ スレッドのアプリケーションまたはサービスを使用している場合、同期 API を呼び出すスレッドを作成するとパフォーマンスに大きな影響を与えるため、非同期 API を直接使用することをお勧めします。

## <a name="segmented-listing"></a>セグメント化された一覧取得

クラウド ストレージのスケールでは、一覧取得をセグメント化する必要があります。 たとえば、Azure BLOB コンテナー内には 100 万以上の BLOB を、Azure テーブル内には 10 億以上のエンティティを保持することができます。 こうした数値は仮定上のものではなく、実際の顧客の使用例に基づいています。

したがって、1 回の応答ですべてのオブジェクトの一覧を取得するのは非現実的です。 代わりに、ページングを使用してオブジェクトの一覧を取得することができます。 各一覧取得 API には、 *セグメント化された* オーバーロードがあります。

セグメント化された一覧取得操作に対する応答は次のとおりです。

* *_segment*: 一覧取得 API に対する 1 回の呼び出しで返された結果のセットが含まれます。
* *continuation_token*: 結果の次のページを取得するために、次の呼び出しに渡されます。 返される結果がそれ以上無い場合、継続トークンは null になります。

たとえば、コンテナー内のすべての BLOB を一覧表示する一般的な呼び出しは、次のコード スニペットのようになります。 コードは、「 [サンプル](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)」にあります。

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

1 ページで返される結果の数は、各 API のオーバーロードのパラメーター *max_results* を使用して制御できることに注意してください。次に例を示します。

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

*max_results* パラメーターを指定しない場合、1 ページでは、既定の上限値である 5000 個までの結果が返されます。

また、Azure Table Storage に対するクエリでは、継続トークンが空でないとしても、レコードが返されないか、指定した *max_results* パラメーターの値より少ないレコードが返される場合があることにも注意してください。 考えられる理由の 1 つは、クエリが 5 秒以内に完了しなかったためです。 継続トークンが空でない限りクエリは継続されるため、コードでセグメント結果のサイズが推測されることはありません。

ほとんどのシナリオで推奨されるコーディング パターンは、一覧取得のセグメント化です。これにより、一覧取得またはクエリ実行の進行状況が明示され、各要求に対するサービスの応答が指定されます。 特に C++ のアプリケーションやサービスでは、一覧取得の進行状況を下位レベルで制御することで、メモリおよびパフォーマンスをコントロールしやすくなります。

## <a name="greedy-listing"></a>どん欲な一覧取得

以前のバージョンの C++ 用ストレージ クライアント ライブラリ (バージョン 0.5.0 プレビューおよびそれ以前) には、以下の例に示すような、テーブルおよびキュー向けのセグメント化されていない一覧取得 API が含まれていました。

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

これらのメソッドは、セグメント化された API のラッパーとして実装されていました。 セグメント化された一覧取得の各応答では、コードはベクターに結果を追加し、コンテナー全体のスキャン後にすべての結果が返されていました。

この方法は、ストレージ アカウントまたはテーブルに含まれるオブジェクトの数が少ない場合には機能します。 ただし、すべての結果がメモリ内に残るため、オブジェクトの数が増加すると、必要なメモリの量が制限なく増大する可能性がありました。 1 回の一覧取得操作に非常に長い時間がかかる可能性があり、この間に呼び出し元は進行状況に関する情報を得られません。

SDK のこのようにどん欲な一覧取得 API は、C#、Java、JavaScript Node.js 環境には存在しません。 こうしたどん欲な API は、その使用で起こり得る問題を回避するためにバージョン 0.6.0 プレビューで削除されました。

これらのどん欲な API をコードで呼び出す場合:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

セグメント化された一覧取得 API を使用するようにコードを修正してください。

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

セグメントの *max_results* パラメーターを指定することで、要求の数とメモリ使用量のバランスをとって、アプリケーションのパフォーマンス上の考慮事項を満たすことができます。

さらに、セグメント化された一覧取得 API を使用しているが、"どん欲な" やり方でローカル コレクションにデータを格納している場合、スケールに合わせて注意深くローカル コレクションにデータを格納するようにコードをリファクタリングすることもお勧めします。

## <a name="lazy-listing"></a>限定的な一覧取得

どん欲な一覧取得は問題が発生する可能性もありますが、コンテナー内のオブジェクトの数が多すぎない場合には便利です。

C# または Oracle Java SDK を使用しているのであれば、列挙型プログラミング モデルをご存知でしょう。このモデルでは、データを必要な場合に一定のオフセットでのみフェッチする、限定的な一覧取得が利用できます。 C++ でも、反復子ベースのテンプレートにより同様の方法が提供されます。

たとえば、**list_blobs** を使用した一般的な限定的一覧取得 API は次のようになります。

```cpp
list_blob_item_iterator list_blobs() const;
```

限定的な一覧取得パターンを使用する一般的なコード スニペットは、次のようになります。

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

限定的な一覧取得は同期モードでのみ使用できることに注意してください。

どん欲な一覧取得と比べて、限定的な一覧取得では必要な場合にのみデータをフェッチします。 この一覧取得では、次の反復子が次のセグメントへと移動した場合にのみ、Azure Storage から秘密裏にデータをフェッチします。 そのため、メモリ使用量は制限されたサイズに抑えられ、動作は高速になります。

限定的一覧取得 API は、バージョン 2.2.0 の C++ 用ストレージ クライアント ライブラリに含まれています。

## <a name="conclusion"></a>まとめ

この記事では、C++ 用ストレージ クライアント ライブラリの各種オブジェクト向けの一覧取得 API のオーバーロードごとに説明しました。 まとめると次のようになります。

* マルチスレッドのシナリオでは、非同期 API を強くお勧めします。
* ほとんどのシナリオでは、セグメント化された一覧取得をお勧めします。
* 限定的な一覧取得は、同期シナリオの便利なラッパーとして、ライブラリで提供されます。
* どん欲な一覧取得は推奨されないため、ライブラリから削除されました。

## <a name="next-steps"></a>次の手順

Azure Storage および C++ 用クライアント ライブラリの詳細については、以下のリソースを参照してください。

* [C++ から BLOB ストレージを使用する方法](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [C++ から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [C++ から Queue ストレージを使用する方法](../storage-c-plus-plus-how-to-use-queues.md)
* [C++ 用 Azure Storage クライアント ライブラリのドキュメント](https://azure.github.io/azure-storage-cpp/)
* [Azure のストレージ チーム ブログ](https://blogs.msdn.com/b/windowsazurestorage/)
* [Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)
