---
title: Azure Spring Cloud のサービス プランとクォータ
description: Azure Spring Cloud のサービス クォータとサービス プランについて説明します
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038686"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud のクォータとサービス プラン

すべての Azure サービスには、リソースと機能に対する既定の制限とクォータが設定されています。  プレビュー期間中は、Azure Spring Cloud で提供されるサービス プランは 1 つだけです。

この記事では、現在のプレビュー期間中に提供されるサービス クォータについて詳しく説明します。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure Spring Cloud のサービス レベルとクォータ

プレビュー期間中は、Azure Spring Cloud で提供されるサービス レベルは 1 つだけです。

リソース | 金額
------- | -------
vCPU | 4
メモリ | 8 GB
Azure Spring Cloud サブスクリプション | 1
サブスクリプション 1 件、1 リージョンあたりの Azure Spring Cloud サービス インスタンスの数 | 2
Azure Spring Cloud のサービス インスタンスあたりのアプリ インスタンスの合計数 | 50
Spring アプリケーションあたりのアプリ インスタンスの合計数 | 20
永続ボリューム | 10 x 50 GB

クォータに達すると、次のような 400 エラーを受け取ります: "Quota exceeds limit for subscription *your subscription* in region *region where your Azure Spring Cloud service is created* (クォータがサブスクリプション <サブスクリプション> のリージョン <Azure Spring Cloud サービスが作成されたリージョン> での制限を超えています)。

## <a name="next-steps"></a>次の手順

特定の既定の制限とクォータを増やすことができます。 リソースを増やす必要がある場合は、azure-spring-cloud@service.microsoft.com に要求をお送りください。
