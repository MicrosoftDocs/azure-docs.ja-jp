---
title: Azure Active Directory を使用して回復性がある ID およびアクセス管理を構築する
description: ID システムの中断に対する回復性の強化に関する、アーキテクト、IT 管理者、および開発者向けのガイド。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454346"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Azure Active Directory を使用して ID およびアクセス管理の回復性を強化する

ID およびアクセス管理 (IAM) は、ID とそのアクセス対象の管理を容易にするプロセス、ポリシー、およびテクノロジのフレームワークです。 これには、システム内のユーザーおよびその他のアカウントの認証と認可をサポートする多くのコンポーネントが含まれます。

IAM の回復性とは、システム コンポーネントの中断に耐え、ビジネス、ユーザー、顧客、および運用への影響を最小限にして復旧する能力です。 包括的なエラー処理を確保しながら、依存関係、複雑さ、および単一点障害を減らすことで、回復性が向上します。

中断は、IAM システムのどのコンポーネントからでも発生する可能性があります。 回復性がある IAM システムを構築するには、中断が発生するものと想定し、それに備えて計画を立てる必要があります。 

IAM ソリューションの回復性を計画するときは、次の要素を考慮してください。 

* IAM システムに依存しているアプリケーション。

* 通信会社、インターネット サービス プロバイダー、公開キーのプロバイダーなど、認証呼び出しで使用される公共インフラストラクチャ。

* クラウドとオンプレミスの ID プロバイダー。

* IAM に依存する他のサービスと、それらを接続する API。

* システム内のその他のオンプレミス コンポーネント。

原因が何であれ、不測の事態を認識し、それに備えて計画を立てることが重要です。 ただし、ID システムをさらに追加し、その結果として依存関係と複雑さが増すことで、回復性は向上するのではなく、むしろ低下する可能性があります。

システムの回復性を高めるには、次の記事を確認してください。

* [IAM インフラストラクチャで回復性を強化する](resilience-in-infrastructure.md)

* [アプリケーションで IAM の回復性を強化する](resilience-app-development-overview.md)

* [顧客 ID およびアクセス管理 (CIAM) システムで回復性を強化する](resilience-b2c.md)
