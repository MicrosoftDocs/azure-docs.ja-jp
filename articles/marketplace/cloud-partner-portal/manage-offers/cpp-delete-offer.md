---
title: マーケットプレースのオファーを削除する - Azure Marketplace | Microsoft Docs
description: Cloud パートナー ポータルを使用して Azure Marketplace および AppSource Marketplace でオファーを削除します
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 1d5d02d65dd3dcf5978639818fba4ebe36ffaaff
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540008"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Azure Marketplace および AppSource のオファーまたは SKU を削除する

さまざまな理由で、Microsoft Marketplace からオファーを取り下げることがあります。2 つの形式で行うことができます。

- "*オファーの削除*" を行うと、新しい顧客はそのオファーを購入またはデプロイできなくなりますが、ライセンス契約と関連法規に従ってサポートする必要のある既存の顧客への影響はありません。 
- "*オファーの終了*" とは、既存の顧客との間で、サービスやライセンス契約を終了するプロセスのことです。 

オファーの削除と終了に関するガイダンスとポリシーは、[Microsoft Marketplace パブリッシャー契約](https://go.microsoft.com/fwlink/?LinkID=699560)および[参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (「[Offering suspension and removal (オファリングの一時中断と削除)](https://docs.microsoft.com/en-us/legal/marketplace/participation-policy#offering-suspension-and-removal)」セクション) によって管理されています。 

この記事では、サポートされている異なる削除シナリオと、それぞれを実行するために必要な手順について説明します。  

> [!NOTE]
> 公開されていないオファーは、**[Editor]\(エディター\)** タブのツール バーの **[Delete]\(削除\)** ボタンを選択するだけで削除することができます。


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>公開済みの SKU を Azure Marketplace から削除する

次の手順を使用して、公開済みの SKU を Azure Marketplace から削除できます。

1.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2.  **[All offers]\(すべてのプラン\)** ページで、対象のオファーを選択します。  オファーが **[Editor]\(エディター\)** タブに表示されます。
3.  左側のツールバーで、**[SKUs]\(SKU\)** タブを選択します。 
4.  削除する SKU を選択し、**[Delete]\(削除\)** ボタンをクリックします。
5.  Azure Marketplace のプランを[再発行](./cpp-publish-offer.md)します。

変更したオファーを Azure Marketplace に公開した後、選択した SKU は Azure Marketplace と Azure portal のリストに表示されなくなります。


## <a name="roll-back-to-a-previous-sku-version"></a>以前のバージョンの SKU にロールバックする

以下の手順を使用して、公開済み SKU の現在のバージョンを Azure Marketplace から削除できます。 プロセスが完了すると、SKU は前のバージョンにロールバックされます。

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインします。
2. **[All offers]\(すべてのプラン\)** ページで、対象のオファーを選択します。  オファーが **[Editor]\(エディター\)** タブに表示されます。
3. 左側のツールバーで、**[SKUs]\(SKU\)** タブを選択します。 
4. 関連付けられているソリューション資産の最新バージョンを、ディスク バージョンのリストから削除します。  オファーの種類により、このフィールドは **[Disk Version]\(ディスク バージョン\)**、**[Package Versions]\(パッケージ バージョン\)**、または同様の資産と表示されることがあります。 
5. Azure Marketplace のプランを[再発行](./cpp-publish-offer.md)します。

変更したオファーを Azure Marketplace に公開した後、リストに表示されている SKU の現在のバージョンは、 Azure Marketplace および Azure portal のリストに表示されなくなります。  SKU は、前のバージョンにロールバックされます。


## <a name="delete-a-live-offer"></a>ライブのプランを削除する

公開されているオファーの削除には、手続き、ビジネス、法律に関してさまざまな側面があります。 公開されているオファーの Azure Marketplace からの削除については、次の手順に従ってサポート チームからガイダンスを入手してください。

1.  「[Create an incident](https://go.microsoft.com/fwlink/?linkid=844975)」(インシデントの作成) ページを使用してサポート チケットを提出するか、または [Cloud パートナー ポータル](https://cloudpartner.azure.com/)の右上隅にある **[Support]\(サポート\)** をクリックします。

2.  **[Problem type]\(問題の種類\)** の一覧で特定のオファーの種類を選択し、**[Category]\(カテゴリ\)** の一覧で **[Remove a published offer]\(公開したオファーの削除\)** を選択します。

3.  要求を送信します。

サポート チームからオファーの削除手順に関する説明があります。

> [!NOTE]
> オファー (または SKU) を削除しても、そのオファー (または SKU) の現時点での購入には影響しません。 それらの購入は、以前と同様に機能し続けます。 ただし、削除されたオファーまたは SKU をそれ以降に購入することはできなくなります。


## <a name="next-steps"></a>次の手順

オファーの管理に使用する基本的な操作に慣れたら、Microsoft [Marketplace オファー](../cpp-marketplace-offers.md)のインスタンスを作成できます。
