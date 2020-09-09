---
title: Azure Resource Manager の vCPU クォータを増やす要求
description: Azure Resource Manager の vCPU クォータを増やす要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7fd04428edc28315e714b9e81af0bea0e9344e52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763807"
---
# <a name="quota-increase-requests"></a>クォータの引き上げのリクエスト

仮想マシンと仮想マシン スケール セットに対する Resource Manager の vCPU クォータは、各リージョンでサブスクリプションごとに 2 つのレベルで適用されます。

1 つ目のレベルは、すべての VM シリーズにわたる、リージョンの vCPU の合計の制限です。 2 つ目のレベルは、D シリーズの vCPU などの、VM シリーズあたりの vCPU の制限です。 新しい仮想マシンをデプロイするときはいつでも、その VM シリーズに対する新規および既存の vCPU 使用量の合計が、その特定の VM シリーズに対して承認されている vCPU クォータを超えてはなりません。 さらに、すべての VM シリーズにわたってデプロイされる新規および既存の vCPU 数の合計が、サブスクリプションに対して承認されているリージョンの vCPU の合計のクォータを超えてはなりません。 いずれかのクォータを超えている場合、VM のデプロイは許可されません。
Azure Portal から VM シリーズの vCPU クォータ制限の増加を要求できます。 VM シリーズのクォータを引き上げると、リージョンの vCPU の合計の制限が同じ量だけ自動的に引き上げられます。

新しいサブスクリプションが作成されるとき、リージョンの vCPU の合計の既定値が、すべての個別 VM シリーズに対する既定の vCPU クォータの合計と等しくない場合があります。 その結果、デプロイする個々の VM シリーズごとに十分なクォータを持つサブスクリプションが作成される可能性があります。 これにより、すべてのデプロイに対するリージョンの vCPU の合計のクォータが不足する可能性があります。 この場合、リージョンの vCPU の合計の制限を明示的に引き上げるための要求を送信する必要があります。 リージョンの vCPU の合計の制限が、そのリージョンのすべての VM シリーズにわたって承認されたクォータの合計を超えることはできません。

クォータの詳細については、「[仮想マシンの vCPU クォータ](../../virtual-machines/windows/quotas.md)」および「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。

