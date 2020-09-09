---
title: BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー)
description: BLOB インデックス タグを使用して、BLOB オブジェクトの分類、管理、および検出のためのクエリを実行する方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/01/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: ed70a05e0a6213ce00a6e0514f0741e8abbaeef9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690376"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー)

データセットのサイズが大きくなればなるほど、大量のデータから特定のオブジェクトを見つけるのが難しくなり、ストレスがたまることがあります。 BLOB インデックスは、キー値のインデックス タグ属性を使用したデータ管理と検出の機能を提供します。これにより、単一のコンテナー内またはストレージ アカウント内のすべてのコンテナー内のオブジェクトを分類および検索できます。 後でデータの要件が変化した場合は、現在のコンテナー編成を維持したまま、インデックス タグを更新することで、オブジェクトを動的に再分類することができます。 BLOB インデックスを利用すると、BLOB データと関連インデックス属性を同じサービスに統合することで、開発を簡素化でき、ネイティブ機能を使用して効率的でスケーラブルなアプリケーションを構築できます。 

BLOB インデックスを使用すると、次のことができます。

- データ管理のキー値インデックス タグを使用して BLOB を動的に分類する
- 1 つのコンテナーまたはストレージ アカウント全体で特定のタグが付いた BLOB をすばやく検索する
- インデックス タグの評価に基づいて BLOB API の条件付き動作を指定する
- [ライフサイクル管理](storage-lifecycle-management-concepts.md)などの BLOB プラットフォーム機能の高度な制御にインデックス タグを使用する

ストレージ アカウントに何百万もの BLOB があり、それらが、さまざまなアプリケーションによって書き込みおよびアクセスされるシナリオを考えてみましょう。 1 つのプロジェクトから関連するすべてのデータを検索したいが、データは BLOB の名前付け規則が異なる複数のコンテナーに分散している可能性があるため、スコープ内に何があるかがはっきりとしません。 ただし、アプリケーションは各プロジェクトおよび識別説明に基づいてタグを持つすべてのデータをアップロードする、ということはわかっています。 何百万もの BLOB を検索して名前とプロパティを比較するのではなく、単純に `Project = Contoso` を検出条件として使用できます。 BLOB インデックスは、ストレージ アカウント全体のすべてのコンテナーをフィルター処理して、`Project = Contoso` から 50 個の BLOB セットのみをすばやく検出して返します。 

BLOB インデックスの使用方法の例については、[BLOB インデックスを使用したデータの管理および検索](storage-blob-index-how-to.md)に関する記事を参照してください。

## <a name="blob-index-tags-and-data-management"></a>BLOB インデックス タグとデータ管理

コンテナーと BLOB 名のプレフィックスは、格納されているデータに対する 1 次元の分類です。 BLOB インデックスでは、属性タグが適用されたすべての [BLOB データ型 (ブロック、追加、またはページ)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) について、多次元の分類が可能になりました。 この多次元分類は、ネイティブにインデックス化され、データのクエリと検索をすばやく実行できるようになります。

ストレージ アカウントに次の 5 つの BLOB があるとします。
>
> container1/transaction.csv  
> container2/campaign.docx  
> photos/bannerphoto.png  
> archives/completed/2019review.pdf  
> logs/2020/01/01/logfile.txt  
>

これらの BLOB は現在、コンテナー/仮想フォルダー/BLOB 名のプレフィックスを使用して分離されています。 BLOB インデックスを使用すると、これらの 5 つの BLOB に対して `Project = Contoso` というインデックス タグ属性を設定して、現在のプレフィックス編成を維持しながら、それらをまとめて分類することができます。 これにより、ストレージ プラットフォームの多次元インデックスを使用してデータをフィルター処理および検索する機能が提供されるため、データを移動する必要がなくなります。

## <a name="setting-blob-index-tags"></a>BLOB インデックス タグの設定

BLOB インデックス タグは、ストレージ アカウント内の新規または既存のオブジェクトに適用できるキー値属性です。 インデックス タグは、アップロード処理中に PutBlob、PutBlockList、または CopyBlob 操作と省略可能な x-ms-tags ヘッダーを使用して指定できます。 ストレージ アカウントに BLOB が既にある場合は、要求の本文に BLOB インデックス タグ属性を指定するフォーマット済み XML ドキュメントを使用して SetBlobTags を呼び出すことができます。 

設定可能な次のタグの例について考えてみましょう

データの処理が完了した日時を記述する 1 つのタグを BLOB に適用できます。
>
> "processedDate" = '2020-01-01'
>
BLOB に複数のタグを適用して、データをよりわかりやすくすることができます。
>
> "Project" = 'Contoso'  
> "Classified" = 'True'  
> "Status" = 'Unprocessed'  
> "Priority" = '01' 
>

既存のインデックス タグ属性を変更するには、最初に既存のタグ属性を取得し、そのタグ属性を変更して、SetBlobTags 操作を使用して置き換えます。 BLOB からすべてのインデックス タグを削除するには、タグ属性を指定せずに SetBlobTags 操作を呼び出します。 BLOB インデックス タグは BLOB データ コンテンツのサブリソースであるため、SetBlobTags によって基になるコンテンツが変更されることはなく、BLOB の last-modified-time や eTag (エンティティ タグ) も変更されません。 現在のすべてのベース BLOB と以前のバージョンのインデックス タグは作成または変更できますが、スナップショットまたは論理的に削除された BLOB のタグは変更できません。 

BLOB インデックス タグには次の制限が適用されます。
- 各 BLOB には、最大 10 個の BLOB インデックス タグを設定できます
- タグ キーは 1 文字から 128 文字にする必要があります
- タグ値は 0 文字から 256 文字にする必要があります
- タグ キーとタグ値では、大文字と小文字が区別されます
- タグ キーとタグ値では文字列データ型のみがサポートされ、数字、日付、時刻、または特殊文字が文字列として保存されます。
- タグ キーとタグ値は、次の名前付け規則に従う必要があります。
  - 英数字: a-z、A-Z、0-9
  - 特殊文字: スペース、プラス、マイナス、ピリオド、コロン、等号、アンダースコア、スラッシュ

## <a name="getting-and-listing-blob-index-tags"></a>BLOB インデックス タグの取得と一覧表示

BLOB インデックス タグは BLOB データと共にサブリソースとして格納され、基になる BLOB データ コンテンツとは別に取得できます。 一度設定すると、1 つの BLOB の BLOB インデックス タグを、GetBlobTags 操作ですぐに取得して確認することができます。 `include:tags` パラメーターを指定した ListBlobs 操作でも、コンテナー内のすべての BLOB が、適用された BLOB インデックス タグと共に返されます。 

少なくとも 1 つの BLOB インデックス タグを持つ BLOB については、ListBlobs、GetBlob、および GetBlobProperties の各操作で、BLOB に存在する BLOB インデックス タグの数を示す x-ms-tag-count が返されます。

## <a name="finding-data-using-blob-index-tags"></a>BLOB インデックス タグを使用したデータの検索

BLOB インデックス タグが BLOB に設定されている場合、インデックス エンジンはそれらのキー/値属性を多次元インデックスに公開します。 インデックス タグは BLOB に存在し、すぐに取得できますが、更新されたインデックス タグ属性を使用して BLOB インデックスが更新されるまでに時間がかかる場合があります。 BLOB インデックスが更新されると、BLOB ストレージによって提供されるネイティブ クエリと検出機能を利用できるようになります。

FindBlobsByTags 操作を使用すると、指定された BLOB インデックス クエリ式に一致するインデックス タグを持つ、フィルター処理して返された一連の BLOB を取得できます。 BLOB インデックスは、ストレージ アカウント内のすべてのコンテナーのフィルター処理をサポートしますが、フィルター処理のスコープを 1 つのコンテナーに限定することもできます。 すべての BLOB インデックス タグのキーと値は文字列であるため、サポートされている関係演算子は、インデックス タグの値に対して辞書式の並べ替えを使用します。

BLOB インデックスのフィルター処理には、次の条件が適用されます。
-   タグ キーは二重引用符 (") で囲む必要があります
-   タグ値とコンテナー名は、単一引用符 (') で囲む必要があります
-   @ 文字は、特定のコンテナー名でのフィルター処理にのみ使用できます (例: @container = 'ContainerName')
- フィルターは、文字列に対する辞書式の並べ替えで適用されます
-   同じキーに対する同じ側の範囲操作は無効です (例:"Rank" > '10' AND "Rank" >= '15')
- REST を使用してフィルター式を作成する場合は、文字を URI でエンコードする必要があります

次の表は、FindBlobsByTags に対して有効なすべての演算子を示しています。

|  演算子  |  説明  | 例 |
|------------|---------------|---------|
|     =      |     Equal     | "Status" = 'In Progress' | 
|     >      |  より大きい |  "Date" > '2018-06-18' |
|     >=     |  [次の値以上] | "Priority" >= '5' | 
|     <      |  より小さい    | "Age" < '32' |
|     <=     |  以下  | "Company" <= 'Contoso' |
|    AND     |  論理 AND  | "Rank" >= '010' AND "Rank" < '100' |
| @container |  特定のコンテナーに範囲指定します   | @container = 'videofiles' AND "status" = 'done' |

> [!NOTE]
> タグの設定とクエリを行うときは、辞書の順序を理解しておいてください。
> - 数字は、文字の前に並べられます。 数字は、1 桁目の数字に基づいて並べられます。
> - 大文字は、小文字の前に並べられます。
> - 記号は標準ではありません。 一部の記号は、数値の前に並べられます。 その他の記号は、文字の前または後に並べられます。
>

## <a name="conditional-blob-operations-with-blob-index-tags"></a>BLOB インデックス タグを使用した条件付き BLOB 操作
REST バージョン 2019-10-10 以降では、ほとんどの [BLOB サービス API](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) で、指定された BLOB インデックス条件が満たされた場合にのみ操作が成功するように、条件ヘッダー x-ms-if-tags がサポートされるようになりました。 条件が満たされない場合、`error 412: The condition specified using HTTP conditional header(s) is not met` が表示されます。

x-ms-if-tags ヘッダーは、他の既存の HTTP 条件ヘッダーと組み合わせることができます (If-Match、If-None-Match など)。  要求に複数の条件ヘッダーが指定されている場合、操作が成功するには、それらのすべてが true に評価される必要があります。  すべての条件ヘッダーは実質的に、論理 AND で結合されます。 

次の表は、条件付き操作に対して有効なすべての演算子を示しています。

|  演算子  |  説明  | 例 |
|------------|---------------|---------|
|     =      |     Equal     | "Status" = 'In Progress' |
|     <>     |   等しくない   | "Status" <> 'Done'  | 
|     >      |  より大きい |  "Date" > '2018-06-18' |
|     >=     |  [次の値以上] | "Priority" >= '5' | 
|     <      |  より小さい    | "Age" < '32' |
|     <=     |  以下  | "Company" <= 'Contoso' |
|    AND     |  論理 AND  | "Rank" >= '010' AND "Rank" < '100' |
|     OR     |  論理 OR   | "Status" = 'Done' OR "Priority" >= '05' |

> [!NOTE]
> BLOB 操作の条件付きヘッダー x-ms-if-tags では許可されているが、FindBlobsByTags 操作には存在しない、2 つの追加演算子「等しくない」および「論理 OR」があります。

## <a name="platform-integrations-with-blob-index-tags"></a>BLOB インデックス タグを使用したプラットフォーム統合

BLOB インデックスのタグは、BLOB データの分類、管理、および検索に役立つだけでなく、[ライフサイクル管理](storage-lifecycle-management-concepts.md)などの他の BLOB サービス機能との統合も提供します。 

### <a name="lifecycle-management"></a>ライフサイクル管理

新しい blobIndexMatch をライフサイクル管理のルール フィルターとして使用すると、BLOB に適用されるインデックス タグに基づいて、データをよりクールな層に移動したり、データを削除したりできます。 これにより、ルールをより細かく設定でき、指定したタグ条件に一致する場合にのみデータを移動または削除することができます。

BLOB インデックス一致をライフサイクル ルールのスタンドアロン フィルター セットとして設定して、タグ付きデータにアクションを適用することができます。 または、プレフィックス一致と BLOB インデックス一致の両方を組み合わせて、より具体的なデータ セットに一致させることができます。 ライフサイクル ルールの処理に複数のフィルターを適用することは、すべてのフィルター条件が一致する場合にのみアクションが適用される論理 AND 操作です。 

次のライフサイクル管理ルールの例は、データが BLOB インデックス タグ条件 ```"Status" = 'Processed' AND "Source" == 'RAW'``` に一致する場合にのみ、コンテナー 'videofiles' 内のブロック BLOB に適用され、BLOB をアーカイブ ストレージに階層化します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
![Azure portal でのライフサイクル管理における BLOB インデックス一致ルールの例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>アクセス許可と承認

BLOB インデックスへのアクセスを承認するには、次のいずれかの方法を使用します。

- ロールベースのアクセス制御 (RBAC) を使用して、Azure Active Directory (Azure AD) セキュリティ プリンシパルにアクセス許可を付与する。 セキュリティと使いやすさのために、Azure AD を使用することをお勧めします。 BLOB 操作での Azure AD の使用に関する詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../common/storage-auth-aad.md)」を参照してください。
- Shared Access Signature (SAS) を使用して BLOB インデックスへのアクセスを委任する。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)」を参照してください。
- アカウント アクセス キーを使用して、共有キーを使用した操作を承認する。 詳細については、[共有キーによる承認](/rest/api/storageservices/authorize-with-shared-key)に関するページを参照してください。

BLOB インデックス タグは、BLOB データのサブリソースです。 BLOB の読み取りまたは書き込みを行うアクセス許可または SAS トークンを持つユーザーは、BLOB インデックス タグにアクセスできない可能性があります。 

### <a name="role-based-access-control"></a>ロールベースのアクセス制御 
[AAD ID](../common/storage-auth-aad.md) を使用する呼び出し元には、BLOB インデックス タグを操作するための次のアクセス許可が付与される場合があります。 

|   BLOB 操作   |  RBAC アクション   |
|---------------------|----------------|
| タグによる BLOB の検索  | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action |
| BLOB タグの設定         | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | 
| BLOB タグの取得         | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read |

タグを操作するには、基になる BLOB データとは別に、追加のアクセス許可が必要です。 ストレージ BLOB データ共同作成者ロールには、これら 3 つのアクセス許可がすべて付与されます。 ストレージ BLOB データ閲覧者には、タグによる BLOB の検索と BLOB タグの取得のアクセス許可のみが付与されます。

### <a name="sas-permissions"></a>SAS のアクセス許可 
[Shared Access Signature (SAS)](../common/storage-sas-overview.md) を使用する呼び出し元には、BLOB タグを操作するためのスコープ付きアクセス許可が付与される場合があります。
#### <a name="blob-sas"></a>BLOB SAS
BLOB インデックス タグへのアクセスを許可するために、Blob service の SAS に次のアクセス許可が付与される場合があります。 BLOB の読み取りと書き込みのアクセス許可だけでは、インデックス タグの読み取りや書き込みを行うことはできません。

|  権限  |  URI の略記  | 許可される操作 |
|--------------|--------------|--------------------|
|  インデックス タグ  |      t         | BLOB の BLOB インデックス タグを取得および設定する |

#### <a name="container-sas"></a>コンテナー SAS
BLOB タグのフィルター処理を可能にするために、コンテナー サービス SAS に次のアクセス許可が付与される場合があります。  BLOB リストのアクセス許可では、インデックス タグによる BLOB のフィルター処理を行うことはできません。

|  権限  |  URI の略記  | 許可される操作 |
|--------------|--------------|--------------------|
| インデックス タグ     |      f       | BLOB インデックス タグを使用して BLOB を検索する | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>メタデータと BLOB インデックス タグのいずれかの選択 
BLOB インデックス タグとメタデータはどちらも、ユーザー定義の任意のキー/値プロパティを BLOB リソースと共に格納することができます。 どちらも、BLOB の内容を返したり変更したりせずに、直接取得して設定することができます。 メタデータとインデックス タグの両方を使用できます。

ただし、BLOB インデックス タグのみが自動的にインデックス付けされ、ネイティブの BLOB サービスによってクエリ可能になります。 [Azure Search](../../search/search-blob-ai-integration.md) などの別のサービスを使用する場合を除き、メタデータにネイティブでインデックスを付けてクエリを実行することはできません。 BLOB インデックス タグには、基になる BLOB データとは別の読み取り/フィルター処理および書き込みのための追加のアクセス許可もあります。 メタデータは BLOB と同じアクセス許可を利用し、GetBlob または GetBlobProperties 操作で HTTP ヘッダーとして返されます。 BLOB インデックス タグは [Microsoft が管理するキー](../common/storage-service-encryption.md)を使用して保存時に暗号化されますが、メタデータは BLOB データ用に指定されたのと同じ暗号化キーを使用して保存時に暗号化されます。 

以下の表は、メタデータと BLOB インデックス タグの違いをまとめたものです。

|              |   Metadata   |   BLOB インデックス タグ  |
|--------------|--------------|--------------------|
| **制限**         | 数値制限はありません。合計 8 KB。大文字と小文字を区別しません | BLOB あたり最大 10 個のタグ、タグあたり 768 バイト。大文字と小文字を区別します |
| **更新プログラム**      | アーカイブ層では許可されません。SetBlobMetadata は、既存のすべてのメタデータを置き換えます。SetBlobMetadata は BLOB の last-modified-time を変更します | すべてのアクセス層で許可されます。SetBlobTags は、既存のすべてのタグを置き換えます。SetBlobTags は BLOB の last-modified-time を変更しません |
| **Storage**        | BLOB データと共に格納されます |  BLOB データへのサブリソースです | 
| **インデックス作成とクエリの実行** | ネイティブで該当なし。Azure Search などの別のサービスを使用する必要があります | あり。 ネイティブのインデックス作成とクエリ実行機能が BLOB ストレージに組み込まれています |
| **暗号化** | BLOB データに使用されるのと同じ暗号化キーを使用して保存時に暗号化 |  Microsoft が管理する暗号化キーを使用して保存時に暗号化 |
| **料金**   | メタデータのサイズは BLOB のストレージ コストに含まれます |    インデックス タグあたりの固定コスト | 
| **ヘッダー応答** | メタデータは GetBlob および GetBlobProperties でヘッダーとして返されます | TagCount は GetBlob または GetBlobProperties で返されます。タグは GetBlobTags と ListBlobs でのみ返されます |
| **アクセス許可**  |    BLOB データに対する読み取りまたは書き込みのアクセス許可がメタデータに拡張されます |    タグの読み取り/フィルター処理または書き込みを行うには追加のアクセス許可が必要です |

## <a name="pricing"></a>価格
BLOB インデックスの価格は現在パブリック プレビュー段階であり、一般提供では変更される可能性があります。 お客様は、ストレージ アカウント内の BLOB インデックス タグの合計数に対して、その月の平均値で課金されます。 インデックス作成エンジンには料金はかかりません。 SetBlobTags、GetBlobTags、および FindBlobsByTags への要求は、それぞれの操作の種類に応じて課金されます。 詳しくは、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

## <a name="regional-availability-and-storage-account-support"></a>リージョンの可用性とストレージ アカウントのサポート

現時点で BLOB インデックスを使用できるのは、階層型名前空間 (HNS) が無効になっている General Purpose v2 (GPv2) アカウントだけです。 General Purpose (GPV1) アカウントはサポートされていませんが、GPv1 アカウントを GPv2 アカウントにアップグレードできます。 ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](../common/storage-account-overview.md)」をご覧ください。

パブリック プレビューでは、BLOB インデックスは現在、次の一部のリージョンでのみご利用いただけます。
- カナダ中部
- カナダ東部
- フランス中部
- フランス南部

開始するには、[BLOB インデックスを使用したデータの管理および検索](storage-blob-index-how-to.md)に関する記事を参照してください。

> [!IMPORTANT]
> この記事の条件に関するセクションを参照してください。 プレビューに登録する場合は、この記事のサブスクリプションの登録に関するセクションを参照してください。 ストレージ アカウントで BLOB インデックスを使用する前に、サブスクリプションを登録する必要があります。

### <a name="register-your-subscription-preview"></a>サブスクリプションを登録する (プレビュー)
BLOB インデックスはパブリックプ プレビュー段階にすぎないため、この機能を使用する前にサブスクリプションを登録する必要があります。 要求を送信するには、次の PowerShell または CLI コマンドを実行します。

#### <a name="register-by-using-powershell"></a>PowerShell を使用して登録する
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Azure CLI を使用して登録する
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>条件と既知の問題 (プレビュー)
このセクションでは、BLOB インデックスの現在のパブリック プレビューにおける既知の問題と条件について説明します。 ほとんどのプレビューと同様、動作が変更される可能性があるため、この機能は GA に達するまで実稼働ワークロードに使用しないでください。

-   プレビューでは、プレビュー リージョンでストレージ アカウントの BLOB インデックスを使用にする前に、まずサブスクリプションを登録する必要があります。
-   プレビューでは、GPv2 アカウントのみが現在サポートされています。 Blob、BlockBlobStorage、および HNS 対応 DataLake Gen2 アカウントは、BLOB インデックスでは現在サポートされていません。 GPv1 アカウントはサポートされません。
-   現在、インデックス タグがあるページ BLOB をアップロードしても、タグは保持されません。 タグは、ページ BLOB をアップロードした後に設定する必要があります。
-   フィルター処理が単一のコンテナーにスコープされている場合、フィルター式のすべてのインデックス タグが等値チェック (キー = 値) である場合にのみ @container を渡すことができます。 
-   AND 条件で範囲演算子を使用する場合は、同じインデックス タグ キー名のみを指定できます (Age > ‘013’ AND Age < ‘100’)。
-   バージョン管理と BLOB インデックスは現在サポートされていません。 BLOB インデックス タグはバージョンに対して保持されますが、現時点では BLOB インデックス エンジンに渡されません。
-   アカウント フェールオーバーは現在サポートされていません。 フェールオーバー後に BLOB インデックスが正しく更新されない可能性があります。
-   ライフサイクル管理では、BLOB インデックス一致による等値チェックのみが現在サポートされています。
-   CopyBlob では、BLOB インデックス タグはコピー元 BLOB から新しいコピー先 BLOB にコピーされません。 コピー操作中にコピー先 BLOB に適用するタグを指定できます。 
- コピー先 BLOB にタグが適用されている別のストレージ アカウントから CopyBlob (非同期コピー) を使用すると、現在、これは BLOB インデックス エンジンからフィルター セット内の BLOB とそのタグが返されなくなる原因となっています。 一時的に、URL (Sync copy) から CopyBlob を使用することをお勧めします。
-   タグはスナップショット作成時に保持されます。ただし、スナップショットの昇格は現在サポートされておらず、空のタグ セットが生成される可能性があります。

## <a name="faq"></a>よく寄せられる質問

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>BLOB インデックスは、BLOB 内のコンテンツをフィルター処理したりクエリを実行したりするのに役立ちますか? 
いいえ。 BLOB インデックス タグは、探している BLOB を見つけるのに役立ちます。 BLOB 内で検索する必要がある場合は、クエリ アクセラレーションまたは Azure Search を使用してください。

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>BLOB インデックスのタグ値に関して、特別な注意事項はありますか?
BLOB インデックス タグでは文字列データ型のみがサポートされ、クエリでは辞書の順序で結果が返されます。 数字については、ゼロ パディングすることをお勧めします。 日付と時刻については、ISO 8601 準拠の形式で格納することをお勧めします。

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>BLOB インデックス タグと Azure Resource Manager タグは関連していますか?
いいえ。Azure Resource Manager タグは、サブスクリプション、リソース グループ、ストレージ アカウントなどのコントロール プレーン リソースを整理するのに役立ちます。 BLOB インデックス タグは、ストレージ アカウント内の BLOB などのデータ プレーン リソースでのオブジェクト管理と検出を提供します。

## <a name="next-steps"></a>次のステップ

BLOB インデックスを使用する方法の例については、[BLOB インデックスを使用してデータを管理して検索する](storage-blob-index-how-to.md)方法に関する記事を参照してください。

[ライフサイクル管理](storage-lifecycle-management-concepts.md)について確認し、BLOB インデックスの一致でルールを設定します。

