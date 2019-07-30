---
title: Microsoft Azure Storage でのコンカレンシー制御の管理
description: BLOB、キュー、テーブル、およびファイル サービスのコンカレンシー制御を管理する方法
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 427cc34cc5a2801a2da98259f932678cdcf71ef7
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67870828"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Microsoft Azure Storage でのコンカレンシー制御の管理
## <a name="overview"></a>概要
最新のインターネットを基盤とするアプリケーションでは、複数のユーザーが同時にデータを表示し、更新することが一般的です。 このような場合、アプリケーション開発者は予測可能なエクスペリエンスをエンド ユーザーに提供する方法を注意深く検討する必要があり、特に複数のユーザーが同じデータを更新できる場合はこれが重要になります。 開発者が通常検討するデータのコンカレンシー制御戦略には、主に次の 3 つがあります。  

1. オプティミスティック コンカレンシー – アプリケーションがデータを更新する場合、更新処理の一部として、データがそのアプリケーションに最後に読み込まれた後に更新されていないか確認します。 たとえば、wiki のページを表示している 2 人のユーザーが同じページを更新しようとしている場合、wiki のプラットフォームは、2 番目の更新が最初の更新を上書きせず、両方のユーザーが自身の更新の成否を把握できるようにする必要があります。 この戦略は、Web アプリケーションで最も広く使用されています。
2. ペシミスティック コンカレンシー制御 - 更新を実行しようとするアプリケーションがオブジェクトをロックし、ロックが解除されるまで他のユーザーがデータを更新できないようにします。 たとえば、マスターと下位の間でデータを複製する場合、マスターのみが更新を実行するため、通常は、データを一定期間、排他的にロックして、該当するデータを他から更新できないようにします。
3. 最終書き込み者を優先 - アプリケーションが最初にデータを読み込んだ後に他のアプリケーションが該当するデータを更新したかどうかを検証せず、すべての更新操作を許可する手法です。 この戦略 (つまり正式な戦略を持たない) は、通常、データが分割されており、複数のユーザーが同時に同じデータにアクセスする可能性がほとんどない場合に使用されます。 また、有効期限が短いデータ ストリームの処理にも有効です。  

この記事では、Azure Storage プラットフォームで上記 3 つのコンカレンシー制御すべてについて最高品質のサポートが提供されていることで、開発がいかに簡素化されるかについて概要を説明します。  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure Storage – クラウド開発を簡素化
Azure ストレージ サービスでは、上記 3 つの戦略をすべてサポートしていますが、特に特徴的なのは、オプティミスティック コンカレンシーとペシミスティックコンカレンシー制御のフル サポートを提供していることです。強力な整合性モデルを備えているため、Storage サービスでデータの挿入や更新の処理がコミットされた後にサービスがそのデータにアクセスした場合でも、確実に最新のデータが提供されます。 最終的な整合性モデルを使用するストレージ プラットフォームでは、あるユーザーが書き込んだ後に他のユーザーがそのデータを表示できるようになるまでにはタイム ラグが発生します。このため、エンド ユーザーがこの不整合の影響を受けないようにするために、クライアント アプリケーションの開発は複雑になります。  

また、開発者は、適切なコンカレンシー制御の戦略を選択する以外にも、特に複数のトランザクションで同一のオブジェクトを変更する場合に、ストレージ プラットフォームで変更がどのように分離されるかについて把握しておく必要があります。 Azure Storage サービスではスナップショット分離の手法を使用して、同一パーティション内での読み取り処理と書き込み処理が同時に発生できるようにしています。 他の分離レベルとは異なり、スナップショット分離では、更新の処理中でもすべての読み込み処理に対してデータのスナップショットを提供することにより整合性を確保します。これは基本的に、更新トランザクションの処理中に最後にコミットされた値を返すことで実現しています。  

## <a name="managing-concurrency-in-blob-storage"></a>BLOB ストレージ内でのコンカレンシーの管理
BLOB サービスでの BLOB およびコンテナーへのアクセスを管理する場合、オプティミスティック コンカレンシーとペシミスティック コンカレンシー制御のいずれかのモデルを使用できます。 明示的に戦略を指定しない場合は、最終書き込み者優先が既定となります。  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>BLOB およびコンテナーでのオプティミスティック コンカレンシー
Storage サービスでは、格納されているすべてのオブジェクトに識別子が割り当てられます。 オブジェクトで更新処理が実行されるたびに、この識別子が更新されます。 この識別子は、HTTP プロトコルで定義されている ETag (エントリ タグ) ヘッダーを使用して、HTTP GET 応答の一部としてクライアントに返されます。 該当するオブジェクトをユーザーが更新しようとすると、条件ヘッダーが付属している元の ETag が送信され、特定の条件を満たしているときにのみ更新が行われます。この条件には、"If-Match" ヘッダーが使用されます。これによって、更新要求で指定された ETag の値が Storage サービスに格納されている値と同一であることが、Storage サービスによって確認されます。  

このプロセスの概要は次のとおりです。  

1. Storage サービスから BLOB を取得します。この応答に含まれる HTTP ETag ヘッダーの値が、Storage サービスに格納されているオブジェクトの現在のバージョンを示します。
2. BLOB を更新するときに、手順 1. でサービスに送信した要求の **If-Match** 条件ヘッダーで返された ETag の値を含めます。
3. サービスによって、要求内の ETag の値と BLOB の現在の ETag の値が比較されます。
4. BLOB の現在の ETag の値が、要求の **If-Match** 条件ヘッダーの ETag の値と異なっている場合、サービスはクライアントに 412 エラーを返します。 これは、クライアントがこの BLOB を取得した後に、別のプロセスがこれを更新したことを示しています。
5. BLOB の現在の ETag の値が、要求の **If-Match** 条件ヘッダーの ETag と同じバージョンである場合、サービスは要求された処理を実行し、この BLOB の新しいバージョンが作成されたことを示すために現在の ETag の値を更新します。  

次に示す C# スニペット (Client Storage Library 4.2.0 を使用) は、事前に取得または挿入された BLOB のプロパティからアクセスされる ETag の値に基づいて **If-Match AccessCondition** を構築する簡単な例です。 BLOB を更新するときには **AccessCondition** オブジェクトを使用します。**AccessCondition** オブジェクトは **If-Match** ヘッダーを要求に追加します。 別のプロセスが BLOB を更新した場合、BLOB のサービスによって HTTP 412 (Precondition Failed) のステータス メッセージが返されます。 以下で完全なサンプルをダウンロードできます。[Azure Storage でのコンカレンシー制御の管理](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Storage サービスでは、**If-Modified-Since**、**If-Unmodified-Since**、**If-None-Match** の各条件ヘッダーとその組み合わせもサポートしています。 詳細については、MSDN の「[BLOB サービス操作の条件ヘッダーの指定](https://msdn.microsoft.com/library/azure/dd179371.aspx)」を参照してください。  

次の表は、要求に含まれている **If-Match** などの条件ヘッダーを受け取り、ETag の値を返すコンテナー操作をまとめたものです。  

| Operation | コンテナーの ETag 値を返す | 条件ヘッダーを受け取る |
|:--- |:--- |:--- |
| コンテナーの作成 |はい |いいえ |
| コンテナーのプロパティの取得 |はい |いいえ |
| Get Container Metadata |はい |いいえ |
| コンテナーのメタデータの設定 |はい |はい |
| コンテナー ACL の取得 |はい |いいえ |
| Set Container ACL |はい |あり (*) |
| Delete Container |いいえ |はい |
| Lease Container |はい |はい |
| BLOBs の一覧 |いいえ |いいえ |

(*) SetContainerACL で定義されたアクセス許可はキャッシュされます。このアクセス許可の更新の伝達には 30 秒間かかり、その間は更新の整合性は保証されません。  

次の表は、要求に含まれている **If-Match** などの条件ヘッダーを受け取り、ETag の値を返す BLOB 操作をまとめたものです。

| Operation | ETag 値を返す | 条件ヘッダーを受け取る |
|:--- |:--- |:--- |
| Put Blob |はい |はい |
| Get Blob |はい |はい |
| BLOB のプロパティの取得 |はい |はい |
| Set Blob Properties |はい |はい |
| BLOB のメタデータの取得 |はい |はい |
| Set Blob Metadata |はい |はい |
| Lease Blob (*) |はい |はい |
| Snapshot Blob |はい |はい |
| BLOB のコピー |はい |Yes (コピー元とコピー先 BLOB に対して) |
| Abort Copy Blob |いいえ |いいえ |
| Delete Blob |いいえ |はい |
| Put Block |いいえ |いいえ |
| Put Block List |はい |はい |
| Get Block List |はい |いいえ |
| Put Page |はい |はい |
| Get Page Ranges |はい |はい |

(*) Lease BLOB では、BLOB の ETag は変更されません。  

### <a name="pessimistic-concurrency-for-blobs"></a>BLOB でのペシミスティック コンカレンシー制御
BLOB をロックして排他的に使用する場合は、[リース](https://msdn.microsoft.com/library/azure/ee691972.aspx)を取得します。 リースを取得すると、必要に応じてリース期間を 15 ～ 60 秒または無制限に設定できます。この期間、BLOB が排他的にロックされます。 リース期間が有限の場合は、これを延長することができます。また、完了したリースは解放できます。 期限が切れた有限のリースは、BLOB サービスで自動的に解放されます。  

リースでは、排他的書き込みと共有読み取り、排他的書き込みと排他的読み取り、共有書き込みと排他的読み取りなど、さまざまな同期戦略がサポートされています。 リースが存在する場合、Storage サービスは排他的書き込み (put、set、delete の各操作) を強制的に実行しますが、読み込み操作の排他性を確保するために、開発者はすべてのクライアント アプリケーションがリース ID を使用し、また有効なリース ID は同時に 1 つのクライアントのみが保持するようにする必要があります。 読み込み操作にリース ID を使用しない場合、共有読み取りになります。  

次の C# スニペットの例では、ある BLOB で 30 秒間の排他的リースを取得し、BLOB の内容を更新します。その後でリースを解放します。 BLOB が既に有効なリースを保持している場合、新しいリースを取得しようとすると、BLOB サービスは "HTTP (409) Conflict" 状態を結果として返します。 また、下記のスニペットでは、Storage サービスに BLOB の更新を要求するときに、**AccessCondition** オブジェクトを使用してリースの情報をカプセル化します。  以下で完全なサンプルをダウンロードできます。[Azure Storage でのコンカレンシー制御の管理](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

リース ID を渡さずにリースを取得した BLOB に対して書き込み操作を要求すると、412 エラーで失敗します。 **UploadText** メソッドを呼び出す前にリース期間が終了したにもかかわらずリース ID を渡そうとすると、その要求も **412** エラーで失敗します。 リースの有効期限とリース ID の管理の詳細については、「[Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)」の REST ドキュメントを参照してください。  

次の BLOB 操作では、ペシミスティック コンカレンシー制御の管理にリースを使用できます。  

* Put Blob
* Get Blob
* BLOB のプロパティの取得
* Set Blob Properties
* Get Blob Metadata
* Set Blob Metadata
* Delete Blob
* Put Block
* Put Block List
* Get Block List
* Put Page
* ページ範囲の取得
* Snapshot Blob - リースが存在する場合にオプションでリース ID を使用
* Copy Blob - コピー先の BLOB でリースが存在する場合はリース ID が必要
* Abort Copy Blob - コピー先の BLOB で無制限のリースが存在する場合はリース ID が必要
* Lease Blob  

### <a name="pessimistic-concurrency-for-containers"></a>コンテナーでのペシミスティック コンカレンシー制御
コンテナーのリースでは、BLOB でサポートされるものと同じ同期戦略 (排他的書き込みと共有読み取り、排他的書き込みと排他的読み取り、共有書き込みと排他的読み取り) がサポートされます。ただし、BLOB とは異なり、Storage サービスで排他的に実行できるのは削除操作のみです。 アクティブなリースを使用してコンテナーを削除するには、クライアントが削除要求に有効なリース ID を含める必要があります。 それ以外のコンテナー操作は、リース ID を含めなくてもリースされたコンテナーに対して正常に実行されます。この場合、各操作は共有操作になります。 更新操作 (put または set) または読み込み操作を排他的に行う必要がある場合、開発者はすべてのクライアントがリース ID を使用し、有効なリース ID を持つクライアントが一度に 1 つのみになるようにする必要があります。  

次のコンテナー操作では、ペシミスティック コンカレンシー制御の管理にリースを使用できます。  

* Delete Container
* コンテナーのプロパティの取得
* Get Container Metadata
* Set Container Metadata
* コンテナー ACL の取得
* Set Container ACL
* Lease Container  

詳細については、次を参照してください。  

* [BLOB サービス操作の条件ヘッダーの指定](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Lease Container](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Table サービスでのコンカレンシー制御の管理
エンティティを扱っている場合、Table サービスではオプティミスティック コンカレンシーの確認が既定の動作として使用されます。一方、BLOB サービスの場合は、オプティミスティック コンカレンシーの確認を実行するように明示的に選択する必要があります。 これ以外の相違点としては、Table サービスではエンティティのコンカレンシー制御しか管理できませんが、BLOB サービスではコンテナーと BLOB の両方のコンカレンシー制御を管理できる点があります。  

オプティミスティック コンカレンシーを使用して、Table ストレージ サービスからエンティティを取得した後に他のプロセスがそれを更新していないか確認するには、Table サービスがエンティティを返す時に受け取った ETag の値を使用できます。 このプロセスの概要は次のとおりです。  

1. Table ストレージ サービスからエンティティを取得します。この応答に含まれている ETag の値によって、ストレージ サービスでこのエンティティに関連付けられている現在の識別子を識別できます。
2. エンティティを更新するときに、手順 1. でサービスに送信した要求の必須の **If-Match** ヘッダーで返された ETag の値を含めます。
3. サービスによって、要求内の ETag の値とエンティティの現在の ETag の値が比較されます。
4. エンティティの現在の ETag の値が、要求の必須 **If-Match** ヘッダーの ETag の値と異なっている場合、サービスはクライアントに 412 エラーを返します。 これは、クライアントがこのエンティティを取得した後に、別のプロセスがこれを更新したことを示しています。
5. エンティティの現在の ETag の値が、要求に含まれる必須の **If-Match** ヘッダーの ETag の値と等しい場合、または **If-Match** ヘッダーにワイルドカード文字 (*) が含まれている場合、サービスは要求された処理を実行し、更新されたことを示すためにエンティティの ETag の値を更新します。  

BLOB サービスとは異なり、Table サービスではクライアントが更新要求に必ず **If-Match** ヘッダーを含める必要があります。 ただし、クライアントが要求の **If-Match** ヘッダーにワイルドカード文字 (*) を設定していた場合、無条件の更新 (最終書き込み者優先戦略) を実行し、コンカレンシー制御の確認を省略することができます。  

次の C# スニペットは、前に電子メール アドレスが更新されたときに作成または取得された顧客エンティティを示しています。 最初の挿入操作または取得操作で、顧客のオブジェクトに ETag の値が格納されます。このサンプルでは置換操作を実行するときに同じオブジェクトのインスタンスを使用するため、ETag の値が自動的に Table サービスに返され、サービスがコンカレンシー制御の違反を確認できるようになっています。 別のプロセスが Table ストレージ内のエンティティを更新した場合、サービスから HTTP 412 (Precondition Failed) のステータス メッセージが返されます。  以下で完全なサンプルをダウンロードできます。[Azure Storage でのコンカレンシー制御の管理](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

コンカレンシー制御の確認を明示的に無効にするには、置換操作を実行する前に、**employee** オブジェクトの **ETag** プロパティを "*" に設定する必要があります。  

```csharp
customer.ETag = "*";  
```

次の表は、Table エンティティで ETag の値がどのように使用されるかをまとめたものです。

| Operation | ETag 値を返す | If-Match 要求ヘッダーが必須 |
|:--- |:--- |:--- |
| エンティティのクエリ |はい |いいえ |
| エンティティの挿入 |はい |いいえ |
| エンティティの更新 |はい |はい |
| エンティティの統合 |はい |はい |
| エンティティの削除 |いいえ |はい |
| エンティティの挿入または置換 |はい |いいえ |
| エンティティの挿入または統合 |はい |いいえ |

**Insert or Replace Entity** と **Insert or Merge Entity** の各操作では、ETag の値は Table サービスに送信されないため、コンカレンシー制御の確認は*行われません*。  

一般に、テーブルを利用するスケーラブルなアプリケーションを開発するときは、オプティミスティック コンカレンシーを採用する必要があります。 ペシミスティック同時実行制御のロックが必要な場合、テーブルにアクセスする際には、テーブルに対する操作が行われる前に各テーブルに指定した BLOB を関連付け、BLOB のリースの取得を試行するという手法を利用できます。 この手法では、テーブルに対する操作が行われる前にすべてのデータのアクセス パスでリースが確実に取得されるようにアプリケーションを設計する必要があります。 また、リース期間は最低 15 秒であるため、スケーラビリティについては慎重に考慮する必要があります。  

詳細については、次を参照してください。  

* [エンティティに対する操作](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>キュー サービスでのコンカレンシー制御の管理
キュー サービスでのコンカレンシー制御について注意が必要なケースとして、複数のクライアントが 1 つのキューからメッセージを取得する場合があります。 キューからメッセージを取得するときの応答には、メッセージと、メッセージを削除するときに必要な pop receipt 値が含まれます。 メッセージはキューから自動的に削除されることはありませんが、取得された後、visibilitytimeout パラメーターで指定された期間は他のクライアントに表示されなくなります。 メッセージを取得するクライアントは、メッセージの処理後、応答の TimeNextVisible 要素で指定された時刻までにメッセージを削除します。この時刻は visibilitytimeout パラメーターの値に基づいて算出されます。 visibilitytimeout の値をメッセージの取得時刻に加算することで、TimeNextVisible の値が決定されます。  

キュー サービスでは、オプティミスティックおよびペシミスティックのいずれのコンカレンシー制御もサポートしていません。このため、キューからメッセージを取得して処理するクライアントは、元の値が変わらないようにメッセージを処理する必要があります。 SetQueueServiceProperties、SetQueueMetaData、SetQueueACL、UpdateMessage などの更新操作では、最終書き込み者優先戦略が適用されます。  

詳細については、次を参照してください。  

* [キュー サービスの REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Get Messages](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>ファイル サービスでのコンカレンシー制御の管理
ファイル サービスでは、SMB と REST の 2 種類のプロトコルを使用してエンドポイントにアクセスできます。 REST サービスではオプティミスティック同時実行制御とペシミスティック同時実行制御のいずれもサポートされておらず、すべての更新操作は最終書き込み者優先戦略に従います。 ファイル共有をマウントする SMB クライアントではファイル システムのロック機構を使用して、共有ファイルへのアクセスを管理できます。ペシミスティック同時実行制御も実行できます。 SMB クライアントがファイルを開くときに、ファイルのアクセス権と共有モードの両方が指定されます。 ファイルのアクセス権のオプションが "Write" または "Read/Write" に設定され、同時にファイル共有モードが "None" に設定された場合、そのファイルは閉じられるまで SMB クライアントによってロックします。 SMB クライアントがロックしているファイルに REST 操作を実行しようとすると、REST サービスはステータス コード 409 (Conflict) およびエラー コード SharingViolation を返します。  

SMB クライアントがファイルを開いて削除する場合、そのファイルで開かれているハンドルが他のすべての SMB クライアントで閉じられるまで、ファイルは保留中の削除とマークされます。 保留中の削除であるとマークされたファイルで REST 操作を実行しようとすると、ステータス コード 409 (Conflict) とエラー コード SMBDeletePending が返されます。 SMB クライアントはファイルを閉じる前に保留中の削除を示すフラグを削除できるため、ステータス コード 404 (Not Found) は返されません。 つまり、ステータス コード 404 (Not Found) は、ファイルが削除済みである場合のみ返されることが想定されます。 ファイルが SMB 削除保留状態の間は、ファイル一覧の結果に含まれないことに注意してください。 また、REST ファイル削除および REST ディレクトリ削除操作は自動的コミットされ、削除保留中の状態が発生しないことに注意してください。  

詳細については、次を参照してください。  

* [ファイルのロックの管理](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>概要と次のステップ
Microsoft Azure Storage サービスは、非常に複雑なオンライン アプリケーションのニーズに対応するように設計されていますが、開発者は、コンカレンシー制御やデータの整合性など、当然のように想定されるようになった主要設計項目を再検討したり、妥協したりする必要がありません。  

このブログで参照したサンプル アプリケーションの完全版は、次のページからダウンロードできます。  

* [Azure Storage でのコンカレンシー制御の管理 - サンプル アプリケーション](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Azure Storage の詳細については、以下を参照してください。  

* [Microsoft Azure Storage のホーム ページ](https://azure.microsoft.com/services/storage/)
* [Azure ストレージの概要](storage-introduction.md)
* Storage Getting Started for [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [Table](../../cosmos-db/table-storage-how-to-use-dotnet.md), [Queues](../storage-dotnet-how-to-use-queues.md), and [Files](../storage-dotnet-how-to-use-files.md) (Storage の入門ガイド: .NET から BLOB ストレージを使用する方法、.NET からテーブル ストレージを使用する方法、.NET からキュー ストレージを使用する方法、.NET から ファイル ストレージを使用する方法)
* ストレージ アーキテクチャ – [Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Azure Storage: 強力な一貫性を備えた高可用性クラウド ストレージ サービス)

