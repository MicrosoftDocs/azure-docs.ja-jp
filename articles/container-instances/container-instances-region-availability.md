---
title: "Azure Container Instances リージョンとリソースの可用性 | Azure Docs"
description: "Azure リージョンがどのコンテナー インスタンスのデプロイと、インスタンスの CPU およびメモリ制限をサポートしているかを検出します。"
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 2b9b1b864bbfd73383759212dd7d91f8e4941544
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Azure Container Instances のリージョンの可用性

プレビュー時は、指定された CPU とメモリ制限を備えた以下のリージョンで、Azure Container Instances を利用できます。

| 場所 | OS | CPU | メモリ (GB) |
| -------- | -- | :---: | :-----------: |
| 西ヨーロッパ、米国西部、米国東部 | Linux | 2 | 7 |
| 西ヨーロッパ、米国西部、米国東部 | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>リソースの可用性

これらのリソース制限内で作成されたコンテナー インスタンスは、デプロイ リージョン内での利用の対象になります。 リージョンでの負荷が高い場合、インスタンスのデプロイ時に失敗することがあります。

このようなデプロイの失敗を減らすために、より低い CPU とメモリの設定でインスタンスをデプロイしてみるか、デプロイを後で試してみてください。

## <a name="next-steps"></a>次のステップ

コンテナー インスタンスのデプロイに関するトラブルシューティングの詳細については、「[Azure Container Instances でのデプロイに関する問題をトラブルシューティングする](container-instances-troubleshooting.md)」をご覧ください。