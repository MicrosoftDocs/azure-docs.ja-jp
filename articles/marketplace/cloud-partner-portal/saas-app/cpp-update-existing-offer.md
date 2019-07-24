---
title: 既存の Azure SaaS アプリケーション オファーを更新する | Microsoft Docs
description: Azure Marketplace にある既存の SaaS アプリケーション オファーを更新する方法です。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d490a0ec09e351e4cee00e7c30c9161bdfac3e6
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196174"
---
# <a name="update-an-existing-saas-application-offer"></a>既存の SaaS アプリケーション オファーを更新する

オファーを発行してライブ状態にした後で、オファーに対して、さまざまな種類の更新が必要になる場合があります。 オファーの新しいバージョンに対する変更を Marketplace に反映するには、変更を保存して再発行する必要があります。 この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)で SaaS オファーを更新するときのさまざまな側面での手順を説明します。

プランを更新する理由は以下のようにいくつか考えられます。

- 既存のアプリに新しいバージョンを追加する。
- アプリを更新する。
- アプリに新しい機能を追加する。
- オファーのマーケットプレース メタデータを更新する。

このような変更をサポートするため、ポータルには**比較**機能と**履歴**機能が用意されています。

## <a name="unpermitted-changes-to-a-saas-offer"></a>SaaS オファーに対する許可されない変更

SaaS オファーには、Azure Marketplace でオファーが運用状態になった後は変更できない属性があります。 次の設定は変更できません。

- オファーのオファー ID と発行元 ID
- バージョン タグ (例: `1.0.1`)
- 既存のオファーに対する課金/ライセンス モデルの変更

## <a name="common-update-operations"></a>共通の更新操作
 
次の更新操作は共通です。

### <a name="update-offer-contacts"></a>オファーの連絡先を更新する

オファーのサポート連絡先を更新するには、次の手順を使用します。

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[All offers](すべてのオファー)** で、更新するオファーを見つけます。
3. **[連絡先]** タブに移動します。連絡先を更新します
4. **[発行]** を選択し、変更を発行するワークフローを開始します。


### <a name="update-offer-marketplace-metadata"></a>プランの Marketplace メタデータの更新

次の手順に従って、プランに関連付けられている Marketplace メタデータを更新します。 (例: 会社名、ロゴなど)

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[All offers](すべてのオファー)** で、更新するオファーを見つけます。
3. **[Storefront Details]\(ネットショップの詳細\)** タブに移動します。[SaaS オファーの発行](./cpp-publish-offer.md)に関す記事の指示に従って、メタデータの変更を行います。
4. **[発行]** を選択し、変更を発行するワークフローを開始します。

## <a name="compare-feature"></a>機能の比較

発行されているオファーを変更する場合、比較機能を使用して、行った変更を監査することができます。 次の画面キャプチャは、発行済みオファーの比較オプションを示したものです。

![比較を使用して、Cloud パートナー ポータルでのオファーの変更を確認する](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>比較機能を使用する方法:

1. 編集プロセスの任意の時点で、目的のオファーのために [比較] を選択します。
2. マーケティングの資産やメタデータのバージョンを横に並べて表示します。

## <a name="publishing-history"></a>発行履歴

発行操作の履歴を表示するには、Cloud パートナー ポータルの左側のナビゲーション メニュー バーにある **[履歴]** タブを選択します。 Azure Marketplace プランの有効期間中に実行されたタイムスタンプ付きの操作を表示することができます。

![Cloud パートナー ポータルでオファーの履歴を表示する](./media/saas-offer-history.png)

監査履歴ページを使用して、特定のオファーを検索し、発行元、オファー、イベントの種類 (たとえば、OfferGoLiveRequested) などでフィルターを適用することができます。監査履歴を csv ファイルとしてダウンロードすることもできます。


## <a name="next-steps"></a>次の手順

[SaaS アプリケーション プラン](./cpp-saas-offer.md)