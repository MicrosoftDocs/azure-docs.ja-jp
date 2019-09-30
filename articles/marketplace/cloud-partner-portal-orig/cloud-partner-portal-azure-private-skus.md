---
title: プライベート SKU とプラン | Azure Marketplace
description: プライベート SKU を使用してプランの利用を管理する方法。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: pabutler
ms.openlocfilehash: 940b50cf4a04abacd4d7be2104dd97fb8b3db736
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883122"
---
<a name="private-skus-and-plans"></a>プライベート SKU とプラン
============

プライベート SKU を使用することで、SKU の利用を特定の顧客だけに制限することができます。 SKU がプライベートとしてマークされている場合、[Azure Marketplace](https://azuremarketplace.microsoft.com) や [Azure portal](https://portal.azure.com) などのパブリック カタログでは利用できません。 Azure portal では、SKU へのアクセス権を持つ顧客のみが閲覧できます。 また、プライベート プランへのアクセス権があることを示すメッセージが示される場合があります。

>[!NOTE]
>パブリック SKU との競合を避けるために、プライベート SKU には新しい一意の SKU/プラン ID が必要です。

プライベート SKU を使用すると、次のシナリオを扱えます。

1.  特定の顧客だけが利用できて一般的には利用できないようなソフトウェアを公開する。
2.  特定の顧客向けにカスタマイズした価格でパブリック ソフトウェアのバリエーションを公開する。
3.  カスタマイズした説明と条件 (新しいプランを利用) でパブリック ソフトウェアのバリエーションを公開する。

価格のみを変更する場合は、同じプラン内の別の SKU からディスクを再利用することができます。 プライベート SKU では、SKU 間でディスクを再送信する必要はありません。

<a name="mark-a-sku-private"></a>SKU をプライベートとしてマークする
---------------------

SKU をプライベートとしてマークするには、SKU がプライベートであるかどうかを尋ねるオプションを切り替えます。

![SKU をプライベートとしてマークする](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

別の SKU でディスクを再利用し、価格または説明を変更することができます。 ディスクを再利用するには、[Does this SKU reuse images from a public SKU (この SKU でパブリック SKU からイメージを再利用しますか?)] というメッセージに対する応答として **[はい]** を選択します。

SKU がプライベートとしてマークされ、かつプランに再利用可能なディスクを持つ他の SKU がある場合、SKU が別の SKU のディスクを再利用することを示すよう求められます。 プライベート SKU の対象ユーザーを指定するようにも求められます。

>[!NOTE]
>パブリック SKU を公開した後、プライベートに戻すことはできません。

<a name="select-an-image"></a>イメージの選択
------------------

プライベート SKU の新しいディスクを提供したり、別の SKU で既に提供されているディスクを再利用して価格や説明のみを変更したりすることができます。 ディスクを再利用するには、[Does this SKU reuse images from a public SKU (この SKU でパブリック SKU からイメージを再利用しますか?)] というメッセージに対する応答として **[はい]** を選択します。

![イメージの再利用を示す](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

SKU でイメージを再利用することを確認したら、イメージのソースである "*ベース*" SKU を選択します。

![イメージの選択](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

プランを公開すると、選択した SKU からのイメージを、プライベート SKU ID により、カスタムの料金/条件で利用できるようになります。 プライベート SKU は、対象ユーザーにのみ表示されます。

イメージを更新する場合、基になる SKU のイメージを更新するだけです。 バックグラウンド処理で、プライベート SKU のイメージも自動的に更新されます。 同様に、基になる SKU からイメージを削除すると、プライベート SKU からも削除されます。

<a name="restricting-the-audience"></a>対象ユーザーの制限
------------------------

プライベート プランを見つけたりデプロイしたりできるのは、対象ユーザーのみです。
現在、サブスクリプション ID を使用して対象ユーザーをサポートしています。

これらのサブスクリプションは、手動入力フォームでは**最大 10 件**を入力できます。CSV ファイルをアップロードする方法では**最大 20,000 件**を入力できます。

制限された対象ユーザーの手動入力:

![手動で対象ユーザーを制限する](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

制限された対象ユーザーの CSV のアップロード:

![CSV を使用して対象ユーザーを制限する](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

CSV ファイルの内容のサンプル:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

手動入力から CSV のアップロード ビューに切り替えるか、CSV から手動入力に切り替えるとき、SKU へのアクセス権があるサブスクリプション ID の以前のリストは保持されません。 プランの保存時に、警告が表示されてリストが上書きされます。

<a name="managing-private-audiences"></a>プライベート対象ユーザーを管理する
-------------------------

**プラン全体を再公開せずに対象ユーザーを更新するには、(UI または API を使用して) 対象ユーザーの変更を行い、"プライベート対象ユーザーの同期" アクションを開始します。**

対象ユーザーが 10 個以下のサブスクリプションの場合は、CPP UI を使用して完全に管理できます。

対象ユーザーが 10 を超えるサブスクリプションの場合は、CPP UI にアップロードできる CSV ファイルを使用するか、API を使用して管理できます。

API を使用し、CSV ファイルを保持したくない場合は、次の手順に従って、API を使用して直接対象ユーザーを管理できます。

> [!NOTE]
> Azure サブスクリプション ID (プランと SKU) またはテナント ID (プランのみ) を使用して、対象ユーザーをプライベート プランに追加できます。

###  <a name="managing-subscriptions-with-the-api"></a>API を使用したサブスクリプションの管理

API を使用すると、CSV のアップロードまたは対象ユーザーの直接管理ができます (CSV を使用する必要はありません)。 一般に、対象ユーザーのメンバーを追加または削除するには、プランを取得し、`restrictedAudience` オブジェクトを更新してから、これらの変更をプランに返信するだけです。

プログラムを使用して対象ユーザー リストを更新する方法を次に示します。

1. [プラン データを取得](cloud-partner-portal-api-retrieve-specific-offer.md)します。

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. 次の JPath クエリを使用して、プランの各 SKU で、制限された対象ユーザー オブジェクトを検索します。

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. プランの制限された対象ユーザー オブジェクトを更新します。

    **プライベート プランのサブスクリプションリストを最初に CSV ファイルからアップロードした場合:**

    "*restrictedAudience*" オブジェクトは次のようになります。
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    制限された対象ユーザー オブジェクトごとに、次のようにします。

    a. `restrictedAudience.uploadedCsvUri` のコンテンツをダウンロードします。 コンテンツは、単にヘッダーが含まれる CSV ファイルです。 例:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. 必要に応じて、ダウンロードした CSV ファイルのサブスクリプションを追加または削除します。

    c. 更新された CSV ファイルを [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md) や [OneDrive](https://onedrive.live.com)などの場所にアップロードし、ファイルへの読み取り専用リンクを作成します。 これが新しい "*SasUrl*" になります。

    d. `restrictedAudience.uploadedCsvUri` キーを新しい "*SasUrl*" で更新します。

    **プライベート プランのサブスクリプションの元の一覧を Cloud パートナー ポータルから手動で入力した場合:**

    "*restrictedAudience*" オブジェクトは次のようになります。

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. 制限された対象ユーザー オブジェクトごとに、必要に応じて `restrictedAudience.manualEntries` リスト内のエントリを追加または削除します。

4. プライベート プランの各 SKU のすべての "*restrictedAudience*" オブジェクトの更新が完了したら、[プランを更新](cloud-partner-portal-api-creating-offer.md)します。

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    これで、更新された対象ユーザー リストが有効になります。

<a name="previewing-private-offers"></a>プライベート プランをプレビューする
-------------------------

プレビュー/ステージングの段階では、プラン レベルのプレビュー サブスクリプションのみが SKU にアクセスできます。 このテスト ステージでは、対象の顧客に表示されるプランをプレビューできます。

ステージング プランにアクセスするプラン レベルのプレビュー サブスクリプション:

![サブスクリプション ID をプレビュー](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

プランが公開された後、制限された対象ユーザーのサブスクリプション (手動入力または CSV) だけが、プライベート SKU を表示およびデプロイできるようになります。 確認のために、プライベート SKU の**制限された対象ユーザーにご自身のサブスクリプションを常に含める**ことをお勧めします。

>[!NOTE]
>Microsoft サポート チームとエンジニアリング チームも、デバッグを行うためにそれらのプライベート プランにアクセスすることができます。
