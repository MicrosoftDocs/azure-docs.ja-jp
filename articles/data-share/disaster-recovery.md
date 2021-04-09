---
title: Azure Data Share のディザスター リカバリー
description: Azure Data Share のディザスター リカバリー
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218699"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure Data Share のディザスター リカバリー

この記事では、Azure Data Share のディザスター リカバリー環境を構成する方法について説明します。 Azure データ センターの停止はほとんど発生しませんが、数分から数時間かかる可能性があります。 データ センターの停止により、データ プロバイダーによって共有されているデータに依存する環境が中断する可能性があります。 データ プロバイダーは、この記事で詳しく説明する手順に従うことで、データ共有をホストしているプライマリ リージョンでデータ センターが停止した場合でも、データ コンシューマーとデータを引き続き共有できます。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Azure Data Share のビジネス継続性の実現

データ プロバイダーは、データ センターの停止に備えるために、セカンダリ リージョンにプロビジョニングされたデータ共有環境を持つことができます。 データ センターの停止が発生した場合に、円滑なフェールオーバーを行うための対策を実行できます。 

データ プロバイダーは、追加のリージョンにセカンダリ Azure Data Share リソースをプロビジョニングできます。 これらの Data Share リソースは、プライマリの Azure Data Share リソースに存在する共有およびデータセットを含めるように構成できます。 これらはデータ コンシューマーをセカンダリ共有に対して、DR 環境を構成するとき、または後で (つまり、 手動フェールオーバー手順の一環として) 追加することができます。

データ コンシューマーが、DR のためにプロビジョニングされたセカンダリ環境にアクティブな共有サブスクリプションを持っている場合、これらはフェールオーバーの一環としてスナップショット スケジュールを有効にできます。 DR のためにセカンダリ リージョンをサブスクライブすることをデータ コンシューマーが望まない場合は、後でセカンダリ データ共有に招待できます。 

データ コンシューマーは、DR のためにアイドル状態のアクティブな共有サブスクリプションを持つことができます。または、データ プロバイダーが手動フェールオーバーの手順の一環として後で招待できます。 

## <a name="related-information"></a>関連情報

- [事業継続とディザスター リカバリー](../best-practices-availability-paired-regions.md)
- [BCDR 戦略に高可用性を組み込む](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。