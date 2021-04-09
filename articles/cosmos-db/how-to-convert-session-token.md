---
title: .NET SDK でセッション トークン形式を変換する方法 - Azure Cosmos DB
description: 異なる .NET SDK バージョン間での互換性を保証するためにセッション トークン形式を変換する方法について説明します。
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 7ed06eeb7330989b4a251dc77e516eb8ac578bff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342047"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>.NET SDK でセッション トークン形式を変換する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、SDK バージョン間での互換性を保証するために、異なるセッション トークン形式間で変換を行う方法について説明します。

> [!NOTE]
> 既定では、SDK はセッション トークンを自動的に追跡し、最新のセッション トークンを使用します。  詳細については、「[セッション トークンを利用する](how-to-manage-consistency.md#utilize-session-tokens)」を参照してください。 この記事の手順は、次の条件に当てはまる場合にのみ適用されます。
> * Azure Cosmos DB アカウントでセッションの整合性が使用される。
> * セッション トークンを手動で管理する。
> * 複数のバージョンの SDK を同時に使用する。

## <a name="session-token-formats"></a>セッション トークン形式

セッション トークン形式には、**シンプル** と **ベクター** の 2 つがあります。  これらの 2 つの形式は互換性がないため、バージョンが異なるクライアント アプリケーションに渡すときに形式を変換する必要があります。
- **シンプル** セッション トークン形式は、.NET SDK V1 (Microsoft.Azure.DocumentDB バージョン 1.x) で使用されます。
- **ベクター** セッション トークン形式は、.NET SDK V2 (Microsoft.Azure.DocumentDB バージョン 2.x) で使用されます。

### <a name="simple-session-token"></a>シンプル セッション トークン

シンプル セッション トークンは次の形式です: `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>ベクター セッション トークン

ベクター セッション トークンは次の形式です: `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>シンプル セッション トークンに変換する

.NET SDK V1 を使用してセッション トークンをクライアントに渡すには、**シンプル** セッション トークン形式を使用します。  たとえば、次のサンプル コードを使用して変換します。

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>ベクター セッション トークンに変換する

.NET SDK V2 を使用してセッション トークンをクライアントに渡すには、**ベクター** セッション トークン形式を使用します。  たとえば、次のサンプル コードを使用して変換します。

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>次のステップ

次の記事を参照してください。

* [セッション トークンを使用して Azure Cosmos DB で一貫性を管理する](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB で適切な一貫性レベルを選択する](./consistency-levels.md)
* [Azure Cosmos DB の一貫性、可用性、パフォーマンスのトレードオフ](./consistency-levels.md)
* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](./consistency-levels.md)