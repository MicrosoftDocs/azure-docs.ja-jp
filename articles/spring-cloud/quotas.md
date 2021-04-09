---
title: Azure Spring Cloud のサービス プランとクォータ
description: Azure Spring Cloud のサービス クォータとサービス プランについて説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877661"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud のクォータとサービス プラン

**この記事の適用対象:** ✔️ Java ✔️ C#

すべての Azure サービスには、リソースと機能に対する既定の制限とクォータが設定されています。   Azure Spring Cloud には、2 つの価格レベルがあります。Basic と Standard です。 この記事では、両方のレベルの制限について詳しく説明します。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure Spring Cloud のサービス レベルと制限

| リソース | Scope | Basic | Standard
------- | ------- | -------
vCPU | アプリ インスタンスごと | 1 | 4
メモリ | アプリ インスタンスごと | 2 GB | 8 GB
Azure Spring Cloud サービス インスタンス | サブスクリプションあたりのリージョンごと | 10 | 10
アプリ インスタンスの合計数 | Azure Spring Cloud サービス インスタンスあたり | 25 | 500
カスタム ドメイン | Azure Spring Cloud サービス インスタンスあたり | 0 | 25 
永続ボリューム | Azure Spring Cloud サービス インスタンスあたり | 1 GB/アプリ x 10 個のアプリ | 50 GB/アプリ x 10 個のアプリ

> [!TIP]
> サービス インスタンスあたりのアプリ インスタンスの合計数について記載されている制限は、停止状態を含むすべての状態のアプリとデプロイに適用されます。 使用されていないアプリやデプロイは削除するようにしてください。

## <a name="next-steps"></a>次のステップ

一部の既定の制限は引き上げることができます。 設定を増やす必要がある場合は、[サポート リクエストを作成してください](../azure-portal/supportability/how-to-create-azure-support-request.md)。
