---
title: 提供が中止された Azure Cosmos DB のパフォーマンス レベル
description: Azure Cosmos DB で以前に提供されていた、S1、S2、S3 パフォーマンス レベルについて説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 06fa98ae4acc2252d8866858ed0e2194ed84ff79
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034388"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>S1、S2、および S3 パフォーマンス レベルの提供の中止

> [!IMPORTANT] 
> この記事で説明する S1、S2、S3 パフォーマンス レベルは提供が中止されるため、新しい Azure Cosmos DB アカウントで使用できなくなります。
>

この記事では、S1、S2、S3 パフォーマンス レベルの概要を示し、これらのパフォーマンス レベルを使用するコレクションを単一パーティション コレクションに移行する方法について説明します。 この記事を読むと、次の質問に回答できるようになります。

- [S1、S2、S3 パフォーマンス レベルの提供が中止されるのはなぜですか?](#why-retired)
- [単一パーティション コレクションおよびパーティション分割コレクションと S1、S2、S3 パフォーマンス レベルはどのように異なりますか?](#compare)
- [中断されることなくデータにアクセスできるようにするには何をする必要がありますか?](#uninterrupted-access)
- [移行後のコレクションはどのように変化しますか?](#collection-change)
- [単一パーティション コレクションに移行した後の請求はどのように変化しますか?](#billing-change)
- [10 GB を超えるストレージが必要な場合はどうすればよいですか?](#more-storage-needed)
- [計画移行時期までに S1、S2、S3 パフォーマンス レベルを変更できますか?](#change-before)
- [どうすれば S1、S2、S3 パフォーマンス レベルから単一パーティション コレクションに自分で移行できますか?](#migrate-diy)
- [EA を使用している場合どのような影響を受けますか?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>S1、S2、S3 パフォーマンス レベルの提供が中止されるのはなぜですか?

S1、S2、S3 パフォーマンス レベルでは、標準の Azure Cosmos DB で提供される柔軟性が提供されていません。 S1、S2、S3 パフォーマンス レベルでは、スループットとストレージ容量の両方が事前設定され、弾力性がありませんでした。 Azure Cosmos DB では現在、スループットとストレージをカスタマイズする機能を提供し、ニーズの変化に応じてスケーリングする能力においてはるかに高い柔軟性を提供しています。

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>単一パーティション コレクションおよびパーティション分割コレクションと S1、S2、S3 パフォーマンス レベルはどのように異なりますか?

次の表では、単一パーティション コレクション、パーティション分割コレクション、S1、S2、S3 パフォーマンス レベルで使用可能なスループットとストレージのオプションを比較しています。 この例は米国東部 2 リージョンの場合です。

|   |パーティション分割コレクション|単一パーティション コレクション|S1|S2|S3|
|---|---|---|---|---|---|
|最大スループット|無制限|10,000 RU/秒|250 RU/秒|1,000 RU/秒|2,500 RU/秒|
|最小スループット|2,500 RU/秒|400 RU/秒|250 RU/秒|1,000 RU/秒|2,500 RU/秒|
|最大ストレージ容量|無制限|10 GB|10 GB|10 GB|10 GB|
|料金 (月額)|スループット: 100 RU/秒 あたり 6 ドル<br><br>ストレージ: 1 GB あたり 0.25 ドル|スループット: 100 RU/秒 あたり 6 ドル<br><br>ストレージ: 1 GB あたり 0.25 ドル|25 米ドル|50 米ドル|100 米ドル|

EA をご使用の場合は、 「[EA を使用している場合どのような影響を受けますか?](#ea-customer)」を参照してください。

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>中断されることなくデータにアクセスできるようにするには何をする必要がありますか?

S1、S2、または S3 コレクションがある場合は、[.NET SDK を使用](#migrate-diy)して、プログラムでコレクションを 1 つのパーティション コレクションに移行する必要があります。 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>移行後のコレクションはどのように変化しますか?

S1 コレクションを使用している場合、400 RU/秒のスループットの単一パーティション コレクションに移行できます。 400 RU/秒は、単一パーティション コレクションで使用できる最も低いスループットです。 ただし、単一パーティション コレクションの 400 RU/秒のコストは、ご使用の S1 コレクションおよび 250 RU/秒で支払っていた金額とほぼ同じです。追加で使用可能な 150 RU/秒に支払うことはありません。

S2 コレクションを使用している場合、1000 RU/秒の単一パーティション コレクションに移行できます。 スループット レベルの変更はありません。

S3 コレクションを使用している場合、2500 RU/秒の単一パーティション コレクションに移行できます。 スループット レベルの変更はありません。

いずれの場合も、コレクションを移行した後に、スループット レベルをカスタマイズしたり、ユーザーが低待機時間でアクセスできるように必要に応じて拡大または縮小したりできます。 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>単一パーティション コレクションに移行した後の請求はどのように変化しますか?

米国東部リージョンで 10 個の S1 コレクションを使用していて、ストレージがそれぞれ 1 GB であるとすると、これらの 10 個の S1 コレクションは 400 RU/秒 (最小レベル) で 10 個の単一パーティション コレクションに移行します。 1 か月間 10 個の単一パーティション コレクションを保持した場合、請求内容は次のようになります。

![10 個の S1 コレクションの価格と、単一パーティション コレクションの価格を使用した 10 個のコレクションの比較](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>10 GB を超えるストレージが必要な場合はどうすればよいですか?

S1、S2、S3 パフォーマンス レベルのコレクションを使用している場合も、単一パーティション コレクションを使用している場合も (いずれも 10 GB のストレージを使用)、Azure Cosmos DB データ移行ツールを使用して、実質的に無制限のストレージがあるパーティション分割コレクションにデータを移行できます。 パーティション分割コレクションのメリットの詳細については、[Azure Cosmos DB でのパーティション分割とスケーリング](sql-api-partition-data.md)に関するページをご覧ください。 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>計画移行時期までに S1、S2、S3 パフォーマンス レベルを変更できますか?

S1、S2、S3 パフォーマンスを使用する既存のアカウントのみが、[.NET SDK を使用して](#migrate-diy)プログラムでパフォーマンス レベルの階層を変更することができます。 S1、S2、S3 から単一パーティション コレクションに変更すると、S1、S2、S3 パフォーマンス レベルに戻すことはできません。

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>どうすれば S1、S2、S3 パフォーマンス レベルから単一パーティション コレクションに自分で移行できますか?

S1、S2、S3 パフォーマンス レベルから単一パーティション コレクションには、[.NET SDK を使用して](#migrate-diy)プログラムで移行することができます。 計画移行時期までにお客様自身で移行すると、単一パーティション コレクションで使用可能な柔軟性の高いスループット オプションのメリットを享受できます。

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>.NET SDK を使用して単一パーティション コレクションに移行する

ここでは、[SQL .NET API](sql-api-sdk-dotnet.md) を使用してコレクションのパフォーマンス レベルを変更する手順を説明しますが、この手順は他の SDK の場合にもほぼ当てはまります。

コレクションのスループットを 1 秒あたり 5,000 要求ユニットに変更するためのコード スニペットを次に示します。
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

他の例やマイクロソフトのプランの各種メソッドの詳細については、 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) を参照してください。

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>EA を使用している場合どのような影響を受けますか?

EA をご使用の場合は、現在の契約が終了するまで価格は保護されます。

## <a name="next-steps"></a>次の手順
Azure Cosmos DB の価格設定とデータ管理の詳細については、以下のリソースを参照してください。

1.  [Cosmos DB でのデータのパーティション分割](sql-api-partition-data.md)。 単一パーティション コンテナーとパーティション分割コンテナーの差異と、シームレスにスケーリングするためのパーティション分割戦略の実装に関するヒントについて説明しています。
2.  [Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)。 スループットのプロビジョニングとストレージの使用のコストについて説明しています。
3.  [要求ユニット](request-units.md)。 読み取り、書き込み、クエリなどのさまざまな種類の操作におけるスループットの使用量について説明しています。
