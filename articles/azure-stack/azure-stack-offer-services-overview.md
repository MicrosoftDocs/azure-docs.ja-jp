---
title: "Azure Stack でのサービスの提供 | Microsoft Docs"
description: "クラウド オペレーターの場合は、ユーザーにサービスを提供できます。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.openlocfilehash: 7a54771d99f2719fcc345496b152a5d3acc02121
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Azure Stack でのサービスの提供の概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

[Microsoft Azure Stack](azure-stack-poc.md) は、データセンターからのサービスの提供を可能にするハイブリッド クラウド プラットフォームです。 サービス プロバイダーの場合は、テナントにサービスを提供できます。 企業や政府機関では、従業員にオンプレミスのサービスを提供できます。 提供できるサービスとして次のようなものが挙げられます。

- App Services、Mobile Apps、API Apps、API 関数、SQL、MySQL などの、サービスとしてのプラットフォーム (PaaS) サービス。

サービスを組み合わせて、さまざまなユーザー向けに複雑なソリューションを統合して作成することもできます。

ユーザーにこれらのサービスを提供するには、[プラン、オファー、およびクォータ](azure-stack-plan-offer-quota-overview.md)を作成する必要があります。 それによってユーザーが、オファーにサブスクライブしてサービスを使用することができます。

## <a name="plan-your-service-offers"></a>サービスのオファーの計画

オファーを計画するときは、次の点に留意してください。

**試用版**: 試用版を使用して、後で追加のサービスにアップグレードする可能性のある新しいユーザーを引き込むことができます。 試用版を作成するには、オプションの大きめのアドオン プランを含む小さい[基本プラン](azure-stack-plan-offer-quota-overview.md#base-plan)を作成します。

**キャパシティ プランニング**: 大量のリソースを取り込んだり、すべてのユーザーに対してシステムを浪費させたりするユーザーのことが懸念される場合があります。 パフォーマンスの助けとなるよう、[クォータを使ってプランを構成](azure-stack-plan-offer-quota-overview.md#plans)して使用量を制限できます。

**委任されたプロバイダー**: 他のユーザーにご利用の環境でオファーを作成する権限を与えることができます。 たとえば、サービス プロバイダーの場合は、この権限を再販業者に[委任](azure-stack-delegated-provider.md)できます。 また、組織の場合は、他の部門/子会社に委任できます。

## <a name="next-steps"></a>次のステップ
[オファー、プラン、クォータ、およびサブスクリプションの詳細](azure-stack-plan-offer-quota-overview.md)

