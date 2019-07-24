---
title: Azure SaaS アプリケーション オファー を発行する | Microsoft Docs
description: Azure Marketplace で SaaS アプリケーション オファーを発行するプロセスと手順です。
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
ms.openlocfilehash: 25c9ab0008e7f056789536d8cc737b69e83d6db5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196091"
---
# <a name="publish-a-saas-application-offer"></a>SaaS アプリケーション オファーを発行する

**[新しいプラン]** ページに情報を指定して新しいプランを作成した後、プランを発行できます。 **[発行]** を選択して、プロセスの発行を開始します。

次の図は、新しい SaaS アプリケーション オファー発行の概要手順を示しています。

![オファー発行の手順](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>発行手順の詳しい説明

次の表に、各手順を完了するための推定所要時間 (最大) と共に、各発行手順を示しています。

|     **Step**       |     **Time**      |  **説明**  |
|  ---------------   |  ---------------  |  ---------------  |
|         認定           |       2 週間            |          プランが Azure 認定チームによって分析されます。 この手順では、ウイルス、マルウェア、安全性のコンプライアンス、およびセキュリティ問題のスキャンを実行します。 また、このオファーが、すべての適性条件を満たしていることも確認します ([前提条件](./cpp-prerequisites.md)に関するページをご覧ください)。 問題が見つかった場合、フィードバックが提供されます。         |
|           梱包         |       1 時間            |       プランの技術資産が顧客の使用のためにパッケージ化され、リード システムが構成され設定されます。            |
|        発行元のサインオフ            |         -          |        プランがライブ状態になる前の、最終的な発行元のレビューと確認。 (プラン情報の手順で) 選択されたサブスクリプション内にプランをデプロイして、すべての要件を満たしていることを確認できます。 プランが次の手順に進めるように、**[Go Live]\(ライブにする\)** を選択します。           |
|        梱包            |        1 時間           |        マーケットプレースの実稼働システムとリージョンに、完成したプランがレプリケートされます。           |
|        ライブ            |       4 日            |         プランが、必要なリージョンにリリース、レプリケートされて、一般公開されます。          |

発行プロセスが完了するまで最大 10 営業日の期間が許可され、プランがリリースされます。 発行プロセスが完了した後、ご自身の SaaS オファーが [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) に一覧表示されます。

## <a name="next-steps"></a>次の手順

[既存のオファーを更新する](./cpp-update-existing-offer.md)
