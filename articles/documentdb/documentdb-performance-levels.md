---
title: "DocumentDB のパフォーマンス レベル | Microsoft Docs"
description: "DocumentDB のパフォーマンス レベルを使用して、コレクションごとにスループットを予約できるようにする方法について説明します。"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 659d6bd63ea996af7b7b172f998884354e5d5858
ms.lasthandoff: 03/07/2017


---
# <a name="retiring-the-s1-s2-and-s3-performance-levels-in-documentdb"></a>DocumentDB での S1、S2、および S3 パフォーマンス レベルの提供の中止

> [!IMPORTANT] 
> この記事で説明する S1、S2、S3 パフォーマンス レベルは提供が中止されるため、新しい DocumentDB コレクションで使用できなくなります。
>

この記事では、S1、S2、S3 パフォーマンス レベルの概要を示し、2017 年 8 月 1 日にこれらのパフォーマンス レベルを使用するコレクションを単一パーティション コレクションに移行する方法について説明します。 この記事を読むと、次の質問に回答できるようになります。

- [S1、S2、S3 パフォーマンス レベルの提供が中止されるのはなぜですか?](#why-retired)
- [単一パーティション コレクションおよびパーティション分割コレクションと S1、S2、S3 パフォーマンス レベルはどのように異なりますか?](#compare)
- [中断されることなくデータにアクセスできるようにするには何をする必要がありますか?](#uninterrupted-access)
- [移行後のコレクションはどのように変化しますか?](#collection-change)
- [単一パーティション コレクションに移行した後の請求はどのように変化しますか?](#billing-change)
- [10 GB を超えるストレージが必要な場合はどうすればよいですか?](#more-storage-needed)
- [2017 年 8 月 1 日までは S1、S2、S3 パフォーマンス レベルの間で変更できますか?](#change-before)
- [自分のコレクションが移行されるタイミングはどうすればわかりますか?](#when-migrated)
- [どうすれば S1、S2、S3 パフォーマンス レベルから単一パーティション コレクションに自分で移行できますか?](#migrate-diy)
- [EA を使用している場合どのような影響を受けますか?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>S1、S2、S3 パフォーマンス レベルの提供が中止されるのはなぜですか?

S1、S2、S3 パフォーマンス レベルでは、DocumentDB の単一パーティション コレクションで提供される柔軟性が提供されていません。 S1、S2、S3 パフォーマンス レベルでは、スループットとストレージ容量の両方が事前設定されていました。 DocumentDB では現在、スループットとストレージをカスタマイズする機能を提供し、ニーズの変化に応じてスケーリングする能力においてはるかに高い柔軟性を提供しています。

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>単一パーティション コレクションおよびパーティション分割コレクションと S1、S2、S3 パフォーマンス レベルはどのように異なりますか?

次の表では、単一パーティション コレクション、パーティション分割コレクション、S1、S2、S3 パフォーマンス レベルで使用可能なスループットとストレージのオプションを比較しています。 この例は米国東部 2 リージョンの場合です。

|   |パーティション分割コレクション|単一パーティション コレクション|S1|S2|S3|
|---|---|---|---|---|---|
|最大スループット|無制限|10,000 RU/秒|250 RU/秒|1,000 RU/秒|2,500 RU/秒|
|最小スループット|2,500 RU/秒|400 RU/秒|250 RU/秒|1,000 RU/秒|2,500 RU/秒|
|最大ストレージ容量|無制限|10 GB|10 GB|10 GB|10 GB|
|料金|スループット: 100 RU/秒 あたり 6 ドル<br><br>ストレージ:&1; GB あたり&0;.25 ドル|スループット: 100 RU/秒 あたり 6 ドル<br><br>ストレージ:&1; GB あたり&0;.25 ドル|25 米ドル|50 米ドル|100 米ドル|

EA をご使用の場合は、 「[EA を使用している場合どのような影響を受けますか?](#ea-customer)」を参照してください。

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>中断されることなくデータにアクセスできるようにするには何をする必要がありますか?

何もする必要はありません。DocumentDB で移行が処理されます。 S1、S2、S3 コレクションを使用している場合、現在のコレクションは 2017 年 7 月 31 日に単一パーティション コレクションに移行されます。 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>移行後のコレクションはどのように変化しますか?

S1 コレクションを使用している場合、400 RU/秒のスループットの単一パーティション コレクションに移行されます。 400 RU/秒は、単一パーティション コレクションで使用できる最も低いスループットです。 ただし、単一パーティション コレクションの 400 RU/秒のコストは、S1 コレクションおよび 250 RU/秒で支払っていた金額とほぼ同じです。追加で使用可能な 150 RU/秒に支払うことはありません。

S2 コレクションを使用している場合、1,000 RU/秒の単一パーティション コレクションに移行されます。 スループット レベルの変更はありません。

S3 コレクションを使用している場合、2,500 RU/秒の単一パーティション コレクションに移行されます。 スループット レベルの変更はありません。

いずれの場合も、コレクションを移行した後に、スループット レベルをカスタマイズしたり、ユーザーが低待機時間でアクセスできるように必要に応じて拡大または縮小したりできます。 コレクションを移行した後にスループット レベルを変更するには、次のスクリーン ショットに示すように、Azure Portal で DocumentDB アカウントを開き、[スケール] をクリックしてコレクションを選択し、スループット レベルを調整します。

![Azure Portal でスループットをスケーリングする方法](./media/documentdb-performance-levels/azure-documentdb-portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>単一パーティション コレクションに移行した後の請求はどのように変化しますか?

米国東部リージョンで 10 個の S1 コレクションを使用していて、ストレージがそれぞれ 1 GB であるとすると、これらの 10 個の S1 コレクションは 400 RU/秒 (最小レベル) で 10 個の単一パーティション コレクションに移行します。 1 か月間 10 個の単一パーティション コレクションを保持した場合、請求内容は次のようになります。

![10 個の S1 コレクションの価格と、単一パーティション コレクションの価格を使用した 10 個のコレクションの比較](./media/documentdb-performance-levels/documentdb-s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>10 GB を超えるストレージが必要な場合はどうすればよいですか?

S1、S2、S3 パフォーマンス レベルのコレクションを使用している場合も、単一パーティション コレクションを使用している場合も (いずれも 10 GB のストレージを使用)、DocumentDB データ移行ツールを使用して、実質的に無制限のストレージがあるパーティション分割コレクションにデータを移行できます。 パーティション分割コレクションのメリットの詳細については、「[Azure DocumentDB でのパーティション分割とスケーリング](documentdb-partition-data.md)」を参照してください。 S1、S2、S3 または単一パーティション コレクションをパーティション分割コレクションに移行する方法の詳細については、「[単一パーティション コレクションからパーティション分割コレクションへの移行](documentdb-partition-data.md#migrating-from-single-partition)」を参照してください。 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-august-1-2017"></a>2017 年 8 月 1 日までは S1、S2、S3 パフォーマンス レベルを変更できますか?

S1、S2、S3 パフォーマンスを使用する既存のアカウントのみが、ポータルまたはプログラムを使用して、パフォーマンス レベルの階層を変更することができます。 2017 年 8 月 1 日には、S1、S2、S3 パフォーマンス レベルが使用できなくなります。 S1、S2、S3 から単一パーティション コレクションに変更すると、S1、S2、S3 パフォーマンス レベルに戻すことはできません。

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>自分のコレクションが移行されるタイミングはどうすればわかりますか?

移行は 2017 年 7 月 31 日に行われます。 S1、S2、S3 パフォーマンス レベルを使用するコレクションをご使用の場合は、移行を実行する前に DocumentDB チームから電子メールでご連絡します。 2017 年 8 月 1 日に移行が完了すると、ご使用のコレクションには Standard 価格が使用されることが Azure Portal に表示されます。

![コレクションが Standard 価格レベルに移行されたことを確認する方法](./media/documentdb-performance-levels/documentdb-portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>どうすれば S1、S2、S3 パフォーマンス レベルから単一パーティション コレクションに自分で移行できますか?

S1、S2、S3 パフォーマンス レベルから単一パーティション コレクションには、Azure Portal またはプログラムを使用して移行することができます。 8 月 1 日までに自分で移行すると、単一パーティション コレクションで使用可能な柔軟性の高いスループット オプションのメリットを享受できます。自分で移行しない場合、コレクションは 2017 年 7 月 31 日に移行されます。

**Azure Portal を使用して単一パーティション コレクションに移行するには**

1. [**Azure Portal**](https://portal.azure.com) で **[NoSQL (DocumentDB)]** をクリックし、変更する DocumentDB アカウントを選択します。 
 
    **[NoSQL (DocumentDB)]** がジャンプバーにない場合は、[>] をクリックし、**[データベース]** までスクロールします。次に、**[NoSQL (DocumentDB)]** を選択し、DocumentDB アカウントを選択します。  

2. [リソース] メニューの **[コレクション]** で **[スケール]** をクリックし、ドロップ ダウン リストから変更するコレクションを選択して、**[価格レベル]** をクリックします。 事前定義のスループットを使用するアカウントの価格レベルには、S1、S2、S3 があります。  **[価格レベルの選択]** ブレードで **[Standard]** をクリックしてユーザー定義スループットに変更し、**[選択]** をクリックして変更を保存します。

    ![Screen shot of the Settings blade showing where to change the throughput value](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

3. **[スケール]** ブレードに戻ると、**[価格レベル]** が **[Standard]** に変更され、**[スループット (RU/秒)]** ボックスには既定値の 400 が表示されています。 スループットは、400 ～ 10,000 [要求ユニット](documentdb-request-units.md)/秒 (RU/秒) の間で設定します。 ページの下部にある **[Estimated Monthly Bill (見積もり月額料金)]** が自動的に更新され、月額料金の見積もりが表示されます。 

    >[!IMPORTANT] 
    > 変更内容を保存して Standard 価格レベルに移行した後に S1、S2、S3 パフォーマンス レベルにロールバックすることはできません。

4. **[保存]** をクリックして変更を保存します。

    より優れた (10,000 RU/秒を上回る) スループットまたはさらに大きな (10 GB を超える) 記憶域が必要であると判断した場合は、パーティション分割コレクションを作成できます。 単一パーティション コレクションをパーティション分割コレクションに移行するには、「[単一パーティション コレクションからパーティション分割コレクションへの移行](documentdb-partition-data.md#migrating-from-single-partition)」を参照してください。

    > [!NOTE]
    > S1、S2、S3 から Standard に変更するには、最大 2 分かかる場合があります。
    > 
    > 

**.NET SDK を使用して単一パーティション コレクションに移行するには**

コレクションのパフォーマンス レベルを変更するもう&1; つの方法は、マイクロソフトの SDK を使用する方法です。 ここでは、Microsoft の [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) を使用してコレクションのパフォーマンス レベルを変更する手順を説明しますが、この手順は他の [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) の場合にもほぼ当てはまります。 .NET SDK を初めてご使用になる場合は、[チュートリアル入門](documentdb-get-started.md)を参照してください。

コレクションのスループットを 1 秒あたり 5,000 要求ユニットに変更するためのコード スニペットを次に示します。
    
```C#
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

## <a name="next-steps"></a>次のステップ
Azure DocumentDB の価格設定とデータ管理の詳細については、以下のリソースを参照してください。

1.    [DocumentDB でのデータのパーティション分割](documentdb-partition-data.md)。 単一パーティション コレクションとパーティション分割コレクションの差異と、シームレスにスケーリングするためのパーティション分割戦略の実装に関するヒントについて説明しています。
2.    [DocumentDB の価格](https://azure.microsoft.com/pricing/details/documentdb/)。 スループットのプロビジョニングとストレージの使用のコストについて説明しています。
3.    [要求ユニット](documentdb-request-units.md)。 読み取り、書き込み、クエリなどのさまざまな種類の操作におけるスループットの使用量について説明しています。
4.    [DocumentDB のデータのモデル化](documentdb-modeling-data.md)。 DocumentDB のデータをモデル化する方法について説明しています。
