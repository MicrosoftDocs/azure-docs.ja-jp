---
title: サービスのクォータと利用可能なリージョン
description: Azure Container Instances サービスのクォータ、制限、リージョンでの利用可能性。
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: eaaa8e0b2d72aaea546a1bc351da40932c1deb14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87384832"
---
# <a name="quotas-and-limits-for-azure-container-instances"></a>Azure Container Instances のクォータと制限

すべての Azure サービスには、リソースと機能に対する特定の既定の制限とクォータが含まれます。 この記事では、Azure Container Instances の既定のクォータと制限について説明します。

Azure Container Instances のコンピューティング、メモリ、およびストレージ リソースの可用性は、リージョンとオペレーティング システムによって異なります。 詳細については、[Azure Container Instances のリソースの可用性](container-instances-region-availability.md)に関するページを参照してください。

[List Usage](/rest/api/container-instances/location/listusage) API を使用して、サブスクリプションのリージョンの現在のクォータ使用率を確認します。

## <a name="service-quotas-and-limits"></a>サービスのクォータと制限

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="next-steps"></a>次のステップ

特定の既定の制限とクォータを増やすことができます。 このような増加に対応する 1 つまたは複数のリソースの増加を要求する場合は、[Azure サポート要求][azure-support]を送信してください ( **[問題の種類]** で "クォータ" を選択してください)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
