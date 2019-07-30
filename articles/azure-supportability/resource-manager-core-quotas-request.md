---
title: Azure Resource Manager の vCPU クォータを増やす要求 | Microsoft Docs
description: Azure Resource Manager の vCPU クォータを増やす要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076810"
---
# <a name="quota-increase-requests"></a>クォータの引き上げのリクエスト

仮想マシンと仮想マシン スケール セットに対する Resource Manager の vCPU クォータは、各リージョンでサブスクリプションごとに 2 つのレベルで適用されます。 

最初のレベルは (すべての VM シリーズにわたる) リージョンの vCPU の合計の制限であり、2 つ目のレベルは VM シリーズあたりの vCPU の制限 (D シリーズの vCPU など) です。 新しい VM をデプロイするときはいつでも、その VM シリーズに対する新規および既存の vCPU 使用量の合計が、その特定の VM シリーズに対して承認されている vCPU クォータを超えてはなりません。 さらに、すべての VM シリーズにわたってデプロイされる新規および既存の vCPU 数の合計が、サブスクリプションに対して承認されているリージョンの vCPU の合計のクォータを超えてはなりません。 いずれかのクォータを超えている場合、VM のデプロイは許可されません。
Azure Portal から VM シリーズの vCPU クォータ制限の増加を要求できます。 VM シリーズのクォータを増やすと、リージョンの vCPU の合計の制限が同じ量だけ自動的に増えます。 

新しいサブスクリプションが作成されるとき、リージョンの vCPU の合計の既定値が、すべての個別 VM シリーズに対する既定の vCPU クォータの合計と等しくない場合があります。 この結果、サブスクリプションで、デプロイしたい個別の VM シリーズごとのクォータは十分な一方、すべてのデプロイに対するリージョンの vCPU の合計のクォータは十分でない可能性があります。 この場合、リージョンの vCPU の合計の制限を明示的に増やすための要求を送信する必要があります。 リージョンの vCPU の合計の制限が、そのリージョンのすべての VM シリーズにわたって承認されたクォータの合計を超えることはできません。

クォータの詳細については、「[仮想マシンの vCPU クォータ](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)」のページおよび [Azure サブスクリプションとサービスの制限](https://aka.ms/quotalimits)に関するページを参照してください。 

