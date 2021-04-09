---
title: Azure Analysis Services の高可用性 | Microsoft Docs
description: この記事では、Azure Analysis Services で、サービスの中断中に高可用性を実現する方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: abfcc38601887cd14509ac436e0344b681e3577e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "86506755"
---
# <a name="analysis-services-high-availability"></a>Analysis Services の高可用性

この記事では、Azure Analysis Services サーバーの高可用性の確保について説明します。 

## <a name="assuring-high-availability-during-a-service-disruption"></a>サービス中断時の高可用性

まれではありますが、Azure データ センターが停止することもあります。 停止が発生すると、ビジネスが中断します。この中断はわずか数分で解消されることもありますが、数時間に及ぶ場合もあります。 高可用性を実現するには、多くの場合、サーバーの冗長性を確保します。 Azure Analysis Services では、1 つ以上のリージョンに追加のセカンダリ サーバーを作成することで、冗長性が実現します。 冗長サーバーを作成するとき、そのサーバー上のデータおよびメタデータが、オフラインになったリージョンのサーバーと確実に同期されるようにするには、次の操作を行います。

* モデルを他のリージョンの冗長サーバーにデプロイします。 この方法を使用する場合、プライマリ サーバーと冗長サーバーの両方のデータを並行して処理し、すべてのサーバーを確実に同期させる必要があります。

* プライマリ サーバーのデータベースを[バックアップ](analysis-services-backup.md)して、冗長サーバーで復元します。 たとえば、Azure Storage への夜間バックアップを自動化し、他のリージョンの他の冗長サーバーに復元できます。 

いずれの場合も、プライマリ サーバーで障害が発生したら、レポート クライアントの接続文字列を変更して、別のリージョンのデータセンターのサーバーに接続する必要があります。 この変更は、致命的なデータ センターの停止が発生した場合の最終手段にしてください。 プライマリ サーバーをホストしているデータ センターが停止した場合、そのデータ センターの復旧は、すべてのクライアントの接続更新よりも先に完了することはよくあります。 

レポート クライアント上の接続文字列の変更を回避するには、プライマリ サーバーに対してサーバー [エイリアス](analysis-services-server-alias.md)を作成します。 プライマリ サーバーで障害が発生した場合は、別のリージョンの冗長サーバーを示すようにエイリアスを変更できます。 プライマリ サーバーのエンドポイントの正常性チェックをコーディングすることで、サーバー名のエイリアスを自動化できます。 正常性チェックが失敗した場合、そのエンドポイントを別のリージョンの冗長サーバーにリダイレクトできます。 

## <a name="related-information"></a>関連情報

[バックアップと復元](analysis-services-backup.md)   
[Azure Analysis Services を管理する](analysis-services-manage.md)   
[サーバー名のエイリアス](analysis-services-server-alias.md) 

