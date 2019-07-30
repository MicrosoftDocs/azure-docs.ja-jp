---
title: Azure Storage での Shared Access Signatures (SAS) の使用 | Microsoft Docs
description: Shared Access Signature (SAS) を使用して、BLOB、キュー、テーブル、ファイルを含む Azure Storage リソースへのアクセスを委任する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2b3c2ed7f2914374ac94783511f2992ae5755967
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302331"
---
# <a name="using-shared-access-signatures-sas"></a>Shared Access Signatures (SAS) の使用

Shared Access Signature (SAS) は、アカウント キーを知らせずに、自分のストレージ アカウントのオブジェクトへの制限付きアクセスを他のクライアントに許可する方法を提供します。 この記事では、SAS モデルの概要を説明し、SAS のベスト プラクティスを確認して、いくつかの例を紹介します。

ここで紹介する以外の SAS を使用したコード例については、[.NET を使用して Azure Blob Storage を使用する](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)と [Azure のコード サンプル](https://azure.microsoft.com/documentation/samples/?service=storage) ライブラリにある他のサンプルをご覧ください。 サンプル アプリケーションをダウンロードして実行することも、GitHub でコードを参照することもできます。

## <a name="what-is-a-shared-access-signature"></a>Shared Access Signature とは

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 SAS を使用すると、アカウント キーを共有することなく、ストレージ アカウントのリソースへのアクセス権をクライアントに付与できます。 これは、アプリケーションで Shared Access Signature を使用する際の重要な点になります。SAS は、アカウント キーを損なうことなく、ストレージ リソースを共有する安全な方法です。

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

SAS では、SAS を使用しているクライアントに付与する次のようなアクセスの種類をきめ細かく制御できます。

* SAS が有効な間隔 (開始時刻と有効期限など)。
* SAS によって付与されるアクセス許可。 たとえば、BLOB の SAS では BLOB への読み取りと書き込み許可が付与されますが、アクセス許可は削除されません。
* SAS を受け入れる Azure Storage の省略可能な IP アドレスまたは範囲の IP アドレス。 たとえば、組織に属する IP アドレスの範囲を指定できます。
* SAS を受け入れる Azure Storage のプロトコル。 この省略可能なパラメーターを使用すると、HTTPS を使用したクライアントのアクセスを制限できます。

## <a name="when-should-you-use-a-shared-access-signature"></a>Shared Access Signature を使用するタイミング

SAS は、自分のストレージ アカウントのアクセス キーを持たないクライアントに、自分のストレージ アカウント内のリソースへのアクセスを許可する場合に使用できます。 ストレージ アカウントには、プライマリ アクセス キーとセカンダリ アクセス キーの両方が含まれており、これらによって、アカウントとその中のすべてのリソースへの管理アクセスが付与されます。 これらのキーのいずれかを知らせると、悪意で、または誤ってアカウントが使用される可能性が生じます。 Shared Access Signature は、アカウント キーが不要で安全な代替方法です。この方法で、クライアントは、明示的に付与されたアクセス許可に従ってストレージ アカウント内のデータの読み取り、書き込み、削除を実行できます。

SAS が役立つ一般的なシナリオは、ストレージ アカウント内でユーザーが自分のデータの読み取りや書き込みを行うサービスです。 ストレージ アカウントにユーザー データが格納されるシナリオには、2 種類の典型的な設計パターンがあります。

1. 認証を実行するフロントエンド プロキシ サービス経由で、クライアントがデータのアップロードとダウンロードを行います。 このフロントエンド プロキシ サービスには、ビジネス ルールの検証が可能であるという利点がありますが、データやトランザクションが大量である場合は、需要に応じて拡張可能なサービスの作成にコストがかかったり、困難が生じたりする可能性があります。

   ![シナリオ図:フロント エンド プロキシ サービス](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. 軽量サービスが、必要に応じてクライアントを認証してから、SAS を生成します。 クライアントは、SAS を受信すると、SAS で定義されたアクセス許可と SAS で許可された期間で、ストレージ アカウントのリソースに直接アクセスできるようになります。 SAS によって、すべてのデータをフロントエンド プロキシ サービス経由でルーティングする必要性が減少します。

   ![シナリオ図:SAS プロバイダー サービス](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

多くの実際のサービスでは、これら 2 つのアプローチを組み合わせて使用している場合があります。 たとえば、一部のデータはフロントエンド プロキシで処理して検証し、その他のデータは SAS を使用して直接保存または読み取ります。

また、特定のシナリオにおけるコピー操作でソース オブジェクトへのアクセスを承認するには、SAS を使用する必要があります。

* BLOB を別のストレージ アカウント内にある他の BLOB にコピーする場合、コピー元 BLOB へのアクセスの承認には SAS を使用する必要があります。 コピー先 BLOB へのアクセスの承認にも、任意で SAS を使用できます。
* ファイルを別のストレージ アカウント内にある他のファイルにコピーする場合、コピー元ファイルへのアクセスの承認には SAS を使用する必要があります。 コピー先 BLOB へのアクセスの承認にも、任意で SAS を使用できます。コピー先ファイルへのアクセスの承認にも、任意で SAS を使用できます。
* BLOB をファイルにコピーしたり、ファイルを BLOB にコピーしたりする場合、同じストレージ アカウント内にコピー先とコピー元のオブジェクトがある場合でも、SAS を使用してソース オブジェクトへのアクセスを承認する必要があります。

## <a name="types-of-shared-access-signatures"></a>共有アクセス署名の種類

次の 2 種類の共有アクセス署名を作成できます。

* **サービス SAS。** サービス SAS は、1 つのストレージ サービス (BLOB、Queue、Table、または File サービスのいずれか) のリソースへのアクセスを委任します。 サービス SAS トークンの作成の詳細については、[サービス SAS の作成](https://msdn.microsoft.com/library/dn140255.aspx) に関するページおよび[ サービス SAS の例](https://msdn.microsoft.com/library/dn140256.aspx) に関するページを参照してください。
* **アカウント SAS。** アカウント SAS では、1 つ以上のストレージ サービスのリソースへのアクセスを委任できます。 サービス SAS を使用して実行できるすべての操作は、アカウント SAS でも実行できます。 アカウント SAS では、**Get/Set Service Properties** や **Get Service Stats**など、特定のサービスに適用される操作へのアクセスも委任できます。サービス SAS で許可されていない BLOB コンテナー、テーブル、キューおよびファイル共有の読み取り、書き込みおよび削除操作へのアクセスも委任できます。 アカウント SAS トークンの作成の詳細については、「[アカウント SAS の作成](https://msdn.microsoft.com/library/mt584140.aspx)」をご覧ください。

## <a name="how-a-shared-access-signature-works"></a>Shared Access Signature の機能

Shared Access Signature とは、特殊なクエリ パラメーターのセットを含むトークンを含む、1 つ以上のストレージ リソースをポイントする署名済み URI です。 このトークンは、リソースへのクライアントのアクセス方法を示します。 このクエリ パラメーターの 1 つである署名は、SAS パラメーターで作成されており、アカウント キーで署名されています。 この署名は、ストレージ リソースへのアクセスを承認するために、Azure Storage によって使用されます。

たとえば、リソース URI と そのSAS トークンを示す SAS URI の例は以下のようになります。

![SAS URI のコンポーネント](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

SAS トークンは、*クライアント*側で生成される文字列です (コードの例については [SAS の例](#sas-examples)のセクションをご覧ください)。 たとえば、ストレージ クライアント ライブラリによって生成された SAS トークンは、Azure Storage によって追跡されません。 クライアント側では、数に制限なく SAS トークンを作成できます。

クライアントが要求の一部として Azure Storage に SAS URI を提供するときに、サービスでは、SAS パラメーターと、要求の認証に対して有効であることを確認する署名が確認されます。 サービスによって署名が有効であることが確認されると、要求が承認されます。 それ以外の場合、要求はエラー コード 403 (Forbidden) で拒否されます。

## <a name="shared-access-signature-parameters"></a>Shared Access Signature パラメーター

アカウント SAS とサービス SAS のトークンには共通のパラメーターがいくつかあります。また別のパラメーターをいくつか取ります。

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>アカウント SAS とサービス SAS のトークンに共通するパラメーター

* **API のバージョン。** 要求の実行に使用するストレージ サービスのバージョンを指定する省略可能なパラメーターです。
* **サービスのバージョン。** 要求の承認に使用するストレージ サービスのバージョンを指定する必須パラメーターです。
* **開始時刻。** この時刻に SAS が有効になります。 Shared Access Signature の開始時刻は省略できます。 開始時刻を省略した場合に、SAS はすぐに有効になります。 開始時刻は、`1994-11-05T13:15:30Z` など、特別な UTC 指定子 ("Z") を使用して、UTC (世界協定時刻) で表す必要があります。
* **有効期限。** この時刻の後、SAS が有効ではなくなります。 ベスト プラクティスでは、SAS の有効期限を指定するか、保存されているアクセス ポリシーに SAS を関連付けることを推奨しています。 終了時刻は、`1994-11-05T13:15:30Z` など、特別な UTC 指定子 ("Z") を使用して、UTC (世界協定時刻) で表す必要があります (下記を参照)。
* **アクセス許可。** SAS に指定されたアクセス許可は、クライアントが SAS を使用して、ストレージ リソースに対して実行できる操作を示します。 使用可能なアクセス許可は、アカウント SAS とサービス SAS で異なります。
* **IP。** 要求の送信元である Azure 外部の IP アドレスまたは IP アドレスの範囲を指定する省略可能なパラメーター (Express Route については、「 [ルーティング セッション構成の状態](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) 」を参照してください)。
* **プロトコル。** 要求に許可されているプロトコルを指定する省略可能なパラメーターです。 指定できる値は、既定値の HTTPS と HTTP (`https,http`) か、HTTPS のみ (`https`) です。 HTTP のみの値は許可されていないことに注意してください。
* **署名。** 署名は、トークンの一部として指定されたその他のパラメーターから構成され、その後に暗号化されます。 署名は、指定されたストレージ リソースへのアクセスを承認するために使用されます。

### <a name="parameters-for-a-service-sas-token"></a>サービス SAS トークンのパラメーター

* **ストレージ リソース。** サービス SAS を使用してアクセスを委任できるストレージ リソースには次があります。
  * コンテナーと BLOB
  * ファイル共有とファイル
  * キュー
  * テーブルとテーブル エンティティの範囲

### <a name="parameters-for-an-account-sas-token"></a>アカウント SAS トークンのパラメーター

* **単一または複数のサービス。** アカウント SAS では、1 つ以上のストレージ サービスにアクセスを委任できます。 たとえば、BLOB およびファイル サービスにアクセスを委任するアカウント SAS を作成できます。 または、(BLOB、キュー、テーブル、およびファイルの) 4 つのすべてのサービスにアクセスを委任する SAS を作成できます。
* **ストレージ リソースの種類。** アカウント SAS は、特定のリソースではなく、ストレージ リソースの 1 つ以上のクラスに適用されます。 アカウント SAS を作成して、次へのアクセスを委任できます。
  * ストレージ アカウントのリソースを呼び出すサービスレベル API。 たとえば、**Get/Set Service Properties**、**Get Service Stats**、**List Containers/Queues/Tables/Shares** です。
  * BLOB コンテナー、キュー、テーブル、およびファイル共有の各サービスのコンテナー オブジェクトを呼び出すコンテナーレベルの API。 たとえば、**Create/Delete Container**、**Create/Delete Queue**、**Create/Delete Table**、**Create/Delete Share**、 **List Blobs/Files and Directories** です。
  * BLOB、キュー メッセージ、テーブル エンティティ、およびファイルを呼び出すオブジェクトレベルの API。 たとえば、**Put Blob**、**Query Entity**、**Get Messages**、**Create File** です。

## <a name="examples-of-sas-uris"></a>SAS の URI の例

### <a name="service-sas-uri-example"></a>サービス SAS URI の例

BLOB に読み書きアクセス許可を付与するサービス SAS URI の例を、次に示します。 表では、SAS での機能がわかりやすいように、URI の部分ごとに説明しています。

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Name | SAS の部分 | 説明 |
| --- | --- | --- |
| Blob URI |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |BLOB のアドレス。 HTTPS の使用を強くお勧めします。 |
| ストレージ サービスのバージョン |`sv=2015-04-05` |ストレージ サービス バージョン 2012-02-12 以降では、このパラメーターは、使用するバージョンを示します。 |
| 開始時刻 |`st=2015-04-29T22%3A18%3A26Z` |UTC 時間で指定。 SAS をすぐに有効にする場合は、開始時刻を省略します。 |
| 有効期限 |`se=2015-04-30T02%3A23%3A26Z` |UTC 時間で指定。 |
| Resource |`sr=b` |リソースは BLOB です。 |
| アクセス許可 |`sp=rw` |SAS で付与されるアクセス許可には、読み取り (r) および書き込み (w) が含まれます。 |
| IP 範囲 |`sip=168.1.5.60-168.1.5.70` |要求が受け入れられる IP アドレスの範囲です。 |
| Protocol |`spr=https` |HTTPS を使用する要求のみが許可されます。 |
| 署名 |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |BLOB へのアクセスを承認するために使用します。 署名は、SHA256 アルゴリズムを使用して署名対象文字列とキーを計算した後に、Base 64 エンコードを使用してエンコードした HMAC 値です。 |

### <a name="account-sas-uri-example"></a>アカウント SAS URI の例

トークンで同じ共通のパラメーターを使用するアカウント SAS の例を次に示します。 これらのパラメーターは上で説明済みのため、ここでは説明はしません。 アカウント SAS に固有のパラメーターのみを次の表に示します。

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Name | SAS の部分 | 説明 |
| --- | --- | --- |
| リソース URI |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Blob service エンドポイントと、(GET を使用して呼び出された場合は) サービスのプロパティを取得するパラメーターまたは (SET を使用して呼び出された場合は) サービスのプロパティを設定するパラメーターです。 |
| サービス |`ss=bf` |SAS は BLOB およびファイル サービスに適用されます。 |
| リソースの種類 |`srt=s` |SAS は、サービスレベルの操作に適用されます。 |
| アクセス許可 |`sp=rw` |この許可は、読み書きの操作へのアクセスを付与します。 |

許可はサービス レベルに制限されているため、この SAS を使用してアクセスできる操作は、**Get Blob Service Properties** (読み取り) および **Set Blob Service Properties** (書き込み) になります。 ただし、別のリソース URI に同じ SAS トークンを使用し、 **Get BLOB Service Stats** (読み取り) へのアクセスを委任することもできます。

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>保存されているアクセス ポリシーを使用した SAS の制御

Shared Access Signature の形式は、次の 2 つのいずれかです。

* **アドホック SAS:** アドホック SAS を作成すると、開始時刻、有効期限、および SAS へのアクセス許可がすべて、SAS URI で指定されます (または、開始時刻を省略した場合は、暗黙で指定されます)。 この種類の SAS はアカウント SAS またはサービス SAS として作成できます。
* **保存されているアクセス ポリシーによる SAS:** 保存されているアクセス ポリシーは、リソース コンテナー (BLOB コンテナー、テーブル、キュー、ファイル共有) で定義されており、これを使用して、1 つ以上の Shared Access Signature のコンテナーを管理できます。 保存されているアクセス ポリシーに SAS を関連付けると、SAS は、保存されているアクセス ポリシーに定義されている制約 (開始時刻、有効期限、およびアクセス許可) を継承します。

> [!NOTE]
> 現時点では、アカウント SAS はアドホック SAS である必要があります。 保存されているアクセス ポリシーは、まだアカウント SAS では使用できません。

1 つの重要なシナリオ、失効では、この 2 つの形式の相違点が重要です。 SAS URI は URL であるため、最初に作成したユーザーに関係なく、SAS を取得した誰でもそれを使用できます。 SAS が一般ユーザーに発行された場合は、世界中のだれでも使用できます。 SAS では、次の 4 つのいずれかが発生するまで、SAS を所有するすべてのユーザーにリソースへのアクセス権が付与されます。

1. SAS に指定された有効期限に達した。
2. 保存されているアクセス ポリシーに指定された、SAS が参照する有効期限に達した (保存されているアクセス ポリシーが参照される場合、かつ有効期限が指定されている場合)。 これは、期間が経過したため、または保存されているアクセス ポリシーの有効期限を過去の日時に変更したために発生します。このような有効期限の変更は、SAS を失効させる方法の 1 つです。
3. SAS の参照先である保存されているアクセス ポリシーが削除されている。これは、SAS を失効させる、もう 1 つの方法です。 保存されているアクセス ポリシーを、完全に同じ名前で再作成すると、そのアクセス ポリシーに関連付けられたアクセス許可に従って、すべての既存の SAS トークンが再び有効になります (SAS の有効期限が過ぎていないことが前提です)。 SAS を失効させるつもりで、将来の時間を有効期限に指定してアクセス ポリシーを再作成する場合は必ず、異なる名前を使用してください。
4. SAS の作成に使用したアカウント キーが再度生成された。 アカウント キーを再生成すると、そのキーを使用するすべてのアプリケーション コンポーネントが、別の有効なアカウント キー、または新しく再生成されたアカウント キーを使用するよう更新されるまで、承認に失敗します。

> [!IMPORTANT]
> Shared Access Signature URI は、署名の作成に使用されたアカウント キーと、保存済みのアクセス ポリシー (存在する場合) に関連付けられます。 保存済みのアクセス ポリシーが指定されていない場合、Shared Access Signature を取り消すにはアカウント キーを変更する以外に方法はありません。

## <a name="authenticating-from-a-client-application-with-a-sas"></a>SAS を使用するクライアント アプリケーションからの認証

SAS を所有しているクライアントは、アカウント キーのないストレージ アカウントに対する要求の承認に SAS を使用できます。 SAS は、接続文字列に含めるか、適切なコンストラクターまたはメソッドから直接使用できます。

### <a name="using-a-sas-in-a-connection-string"></a>接続文字列で SAS を使用する

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>コンストラクターまたはメソッドで SAS を使用する

複数の Azure Storage クライアント ライブラリ コンストラクターとメソッドのオーバー ロードでは SAS パラメーターが提供されるため、SAS でサービスへの要求を承認できるようになります。

たとえば、ここでは SAS URI を使用して、ブロック BLOB への参照を作成します。 SAS は、要求に必要な資格情報のみを提供します。 書き込み操作では、ブロック BLOB の参照が使用されます。

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>SAS を使用する際のベスト プラクティス

アプリケーションで Shared Access Signature を使用する場合は、次の 2 つの潜在的なリスクに注意する必要があります。

* SAS が漏えいすると、取得した人はだれでも使用できるため、ストレージ アカウントの安全性が損なわれるおそれがあります。
* クライアント アプリケーションに提供された SAS が期限切れになり、アプリケーションがサービスから新しい SAS を取得できない場合は、アプリケーションの機能が損なわれる可能性があります。

Shared Access Signature の使用に関する次の推奨事項に従うと、これらのリスクの軽減に役立ちます。

1. **常に HTTPS を使用**して SAS を作成または配布します。 SAS が HTTP で渡され、傍受された場合、中間者攻撃を実行している攻撃者は、SAS を読み取って、宛先のユーザーと同様に使用することができます。このため、機密データの安全性が損なわれたり、悪意のあるユーザーによるデータ破損が発生したりする可能性があります。
2. **可能な場合は、保存されているアクセス ポリシーを参照します。** 保存されているアクセス ポリシーを使用すると、ストレージ アカウント キーを再生成せずに、アクセス許可を失効させることが可能になります。 これらの有効期限をきわめて遠い将来 (または無期限) に設定し、それがさらに将来へ移動するように、定期的に更新されるようにします。
3. **アドホック SAS には、短期間の有効期限を使用します。** こうすると、SAS が侵害された場合でも、有効である期間はほんの短い期間になります。 この方法は、保存されているアクセス ポリシーを参照できない場合に特に重要です。 また、短期間の有効期限は、BLOB にアップロード可能な時間が制限されるので、BLOB に書き込むことのできるデータの量も制限します。
4. **必要に応じて、クライアントに SAS を自動更新させます。** クライアントは、SAS を提供するサービスが利用不可である場合に再試行する時間を考慮して、有効期限までに余裕を持って SAS を更新する必要があります。 使用する SAS が、すぐに実行する短期間の少数の操作用であり、操作が、指定された有効期限の前に完了する予定である場合は、SAS が更新されないため、この方法は必要ありません。 ただし、クライアントが SAS 経由で日常的に要求を実行する場合は、有効期限に注意が必要になる可能性があります。 重要な考慮事項は、SAS の有効期限を短くする必要性 (前述のように) と、更新の完了前に SAS の期限が切れることによる中断を避けるためにクライアントが早めに更新を要求する必要性とのバランスです。
5. **SAS の開始時刻に注意します。** SAS の開始時刻を **[現在]** に設定すると、クロック スキュー (さまざまなコンピューター間での現在時刻の差) により、最初の数分間にエラーが断続的に表示される場合があります。 一般に、開始時刻は 15 分以上前になるように設定します。 または、まったく設定せず、すべての場合ですぐに有効になるようにします。 同じことが、一般的には有効期限にも適用されます。どの要求でも、前後 15 分以内のクロック スキューが発生する可能性があることを憶えておいてください。 2012-02-12 より前の REST バージョンを使用するクライアントの場合、保存されているアクセス ポリシーを参照しない SAS の最長期間は 1 時間であり、それより長い期間を指定するポリシーはすべて失敗します。
6. **アクセス先のリソースを具体的に指定します。** セキュリティのベスト プラクティスは、必要最小限の権限をユーザーに付与することです。 ユーザーに必要なのは、1 つのエンティティへの読み取りアクセスだけの場合は、すべてのエンティティへの読み取り/書き込み/削除アクセスではなく、その 1 つのエンティティへの読み取りアクセスだけをユーザーに許可します。 これは、攻撃者の管理下での SAS の機能を低下させるため、SAS が侵害された場合に損害を抑えるためにも役立ちます。
7. **アカウントは、SAS によるものも含め、すべての使用について課金されます。** BLOB への書き込みアクセスを許可した場合は、ユーザーが 200 GB の BLOB をアップロードする可能性があります。 ユーザーに読み取りアクセスも許可すると、この BLOB を 10 回ダウンロードする可能性があり、2 TB (テラバイト) の送信料金が発生します。 したがって、悪意のあるユーザーによるリスクが軽減されるように、制限付きアクセス許可を付与してください。 このような脅威が軽減されるように、短期間の SAS を使用してください (ただし、終了時刻のクロック スキューには注意してください)。
8. **SAS を使用して書き込まれたデータを検証します。** クライアント アプリケーションがストレージ アカウントにデータを書き込む場合は、そのデータに問題がある可能性に注意してください。 データが検証後または認証後に使用可能になることをアプリケーションが要求する場合は、書き込まれたデータをアプリケーションが使用する前に、この検証を実行する必要があります。 これを実行すると、ユーザーが SAS を正当に入手している場合でも、漏えいした SAS を利用している場合でも、破損データまたは悪意によるデータの書き込みからアカウントが保護されます。
9. **場合によっては SAS を使用しないようにします。** ストレージ アカウントに対する特定の操作に関連するリスクが、SAS の利点より重大である場合もあります。 このような操作については、ビジネス ルールの検証、認証、および監査を実行した後にストレージ アカウントに書き込む中間層サービスを作成します。 また、別の方法でアクセスを管理した方が容易である場合もあります。 たとえば、コンテナー内のすべての BLOB が一般ユーザーに読み取り可能である場合は、すべてのクライアントにアクセス用の SAS を提供するのではなく、コンテナーをパブリックにします。
10. **Storage Analytics を使用してアプリケーションを管理します。** SAS プロバイダー サービスが中断したり、保存されているアクセス ポリシーを不注意で削除したりしたために発生する認証失敗の急増を、ログやメトリックを使用して監視できます。 詳細については、 [Microsoft Azure Storage チームのブログ](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) を参照してください。

## <a name="sas-examples"></a>SAS の例

次に、アカウント SAS とサービス SAS の 2 種類の Shared Access Signature の例をいくつか示します。

これらの C# の例を実行するには、プロジェクト内の次の NuGet パッケージを参照する必要があります。

* [.NET 用 Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage)、バージョン 6.x 以降 (アカウント SAS を使用)。
* [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager)

SAS の作成とテストの例については、[Azure のコード サンプル](https://azure.microsoft.com/documentation/samples/?service=storage)を参照してください。

### <a name="example-create-and-use-an-account-sas"></a>例:アカウント SAS を作成して使用する

次のコード例では、BLOB およびファイル サービスに有効なアカウント SAS を作成します。これでは、クライアントに、サービスレベルの API にアクセスするための、読み取り、書き込み、および一覧表示のアクセス許可を付与します。 アカウント SAS では、プロトコルを HTTPS に制限しているため、要求は HTTPS で行う必要があります。

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Blob service 用にサービスレベルの API にアクセスするためにアカウント SAS を使用するには、SAS を使用して BLOB クライアントを構築し、ストレージ アカウント用に BLOB ストレージ エンドポイントを構築します。

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>例:保存されているアクセス ポリシーを作成する

次のコードでは、保存されているアクセス ポリシーをコンテナーに作成します。 アクセス ポリシーを使用して、コンテナーまたは blob のサービス SAS に制約を指定します。

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>例:サービス SAS をコンテナーに作成する

次のコードでは、SAS をコンテナーに作成します。 既存の保存されているアクセス ポリシーの名前が指定されている場合、そのポリシーは、SAS に関連付けられます。 保存されているアクセス ポリシーがない場合、コードは、アドホック SAS をコンテナーに作成します。

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>例:サービス SAS を Blob に作成する

次のコードでは、 SAS を Blob に作成します。 既存の保存されているアクセス ポリシーの名前が指定されている場合、そのポリシーは、SAS に関連付けられます。 保存されているアクセス ポリシーがない場合、コードは、アドホック SAS を BLOB に作成します。

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>まとめ

Shared Access Signature は、アカウント キーを知らせずに、ストレージ アカウントへの制限付きアクセス許可をクライアントに付与する場合に便利です。 したがって、Azure Storage を使用するあらゆるアプリケーションのセキュリティ モデルの重要な部分となります。 ここに示すベスト プラクティスに従うと、アプリケーションのセキュリティを損なうことなく、SAS を使用して、ストレージ アカウントのリソースへのアクセスの柔軟性を高めることができます。

## <a name="next-steps"></a>次の手順

* [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/storage-manage-access-to-resources.md)
* [Shared Access Signature を使用したアクセスの委任](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [テーブルおよびキュー SAS についての MSDN ブログ](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
