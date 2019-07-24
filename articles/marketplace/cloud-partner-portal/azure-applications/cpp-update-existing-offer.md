---
title: 既存の Azure アプリケーション プランを更新する | Microsoft Docs
description: Azure Marketplace にある既存の Azure アプリケーション プランを更新する方法。
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1019c06afbdab0a6b0e172133da316aea01a5504
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216902"
---
# <a name="update-an-existing-azure-application-offer"></a>既存の Azure アプリケーション プランを更新する

オファーを発行してライブ状態にした後で、オファーに対して、さまざまな種類の更新が必要になる場合があります。 オファーの新しいバージョンに対する変更を Marketplace に反映するには、変更を保存して再発行する必要があります。 この記事では、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)でマネージド アプリケーション プランを更新する際のさまざまな側面での手順を説明します。

プランを更新する理由は以下のようにいくつか考えられます。

- 既存の SKU に新しいイメージのバージョンを追加する。
- 新しい SKU を追加する。
- プランまたは 個々の SKU のマーケットプレース メタデータを更新する。

このような変更をサポートするため、ポータルには**比較**機能と**履歴**機能が用意されています。

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Azure アプリケーション プランまたは SKU に対して許可されない変更

コンテナー プランや SKU には、Azure Marketplace でプランがライブになった後は変更できない属性があります。 次の設定は変更できません。

- プランの [プラン ID] と [Publisher ID]\(発行者 ID\)
- 既存の SKU の [SKU ID]
- バージョン タグ (例: `1.0.1`)
- 既存の SKU に対する課金/ライセンス モデルの変更

## <a name="common-update-operations"></a>共通の更新操作

次の更新操作は共通です。

### <a name="update-image-version-for-a-sku"></a>SKU に対するイメージのバージョンの更新

イメージがセキュリティ パッチ、追加機能などによって定期的に更新されるのは一般的です。 このシナリオでは、次の手順を使用して、SKU が参照するイメージを更新します。

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[すべてのプラン]** から、更新するプランを見つけます。
3. **[SKU]** タブで、更新するイメージに関連付けられている SKU を選択します。
4. **[+ New Image Version]\(+ 新しいイメージ バージョン\)** を選択して新しいイメージを追加します。
5. 新しいイメージ バージョンを指定します。 イメージ バージョンでは、以前のバージョンと同じタグのガイドラインに従う必要があります。 バージョン タグの形式は X.Y.Z で、X、Y、Z は整数です。 指定した新しいバージョンが以前のすべてのバージョンよりも大きいことを確認します。
6. **[発行]** を選択し、新しいコンテナー イメージ バージョンを Azure Marketplace に発行するワークフローを開始します。

### <a name="add-a-new-sku"></a>新しい SKU の追加

次の手順に従って、新しい SKU をプランで使用できるようにします。

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[すべてのプラン]** から、更新するプランを見つけます。
3. **[SKU]** タブで、**[Add new SKU (新しい SKU の追加)]** を選択し、ポップアップ ウィンドウに **[SKU ID]** を指定します。
4. [Azure アプリケーション プランの発行](./cpp-publish-offer.md)に関するページで説明している手順を使用して、プランを再発行します。
5. **[発行]** を選択し、新しい SKU を発行するワークフローを開始します。

### <a name="update-offer-marketplace-metadata"></a>プランの Marketplace メタデータの更新

次の手順に従って、プランに関連付けられている Marketplace メタデータを更新します。 (例: 会社名、ロゴなど)

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[すべてのオファー]** で、更新するオファーを見つけます。
3. **[Marketplace]** タブに移動します。「[Publish Azure application offer (Azure アプリケーション プランを発行する)](./cpp-publish-offer.md)」の手順に従ってメタデータの変更を行います。
4. **[発行]** を選択し、変更を発行するワークフローを開始します。
 
>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。  Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[Cloud Solution Providers (クラウド ソリューション プロバイダー)](../../cloud-solution-providers.md)」を参照してください。

## <a name="deleting-an-existing-offer"></a>既存のプランの削除

Marketplace からオファーを削除することを決定する場合があります。 オファーを削除しても、そのオファーの現時点での購入には影響しません。 それらの顧客の購入は、以前と同様に機能し続けます。 ただし、新たな購入の場合、削除の完了後はそのオファーを購入できなくなります。

プランの終了とは、既存の顧客との間で、サービスやライセンス契約を終了するプロセスのことです。
オファーの削除と終了に関するガイダンスとポリシーは、Microsoft Marketplace パブリッシャー契約 (セクション 7 を参照) および参加ポリシーに関するページ (セクション 6.2 を参照) で管理されています。

詳細については、「[Azure Marketplace からプラン/SKU を削除する](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete)」を参照してください。

## <a name="compare-feature"></a>機能の比較

発行されているプランに変更を加える場合、[比較] 機能を使用して、加えた変更を監査することができます。

比較機能を使用する方法:

1. 編集プロセスの任意の時点で、目的のオファーのために [比較] を選択します。
2. マーケティングの資産やメタデータのバージョンを横に並べて表示します。

## <a name="history-of-publishing-actions"></a>発行操作の履歴

発行操作の履歴を表示するには、Cloud パートナー ポータルの左側のナビゲーション メニュー バーにある **[履歴]** タブを選択します。 Azure Marketplace プランの有効期間中に実行されたタイムスタンプ付きの操作を表示することができます。

## <a name="next-steps"></a>次の手順

[Azure アプリケーション プラン](./cpp-azure-app-offer.md)