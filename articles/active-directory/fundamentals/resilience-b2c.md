---
title: Azure AD B2C を使用して顧客 ID およびアクセス管理で回復性を強化する | Microsoft Docs
description: Azure AD B2C を使用して顧客 ID およびアクセス管理で回復性を強化する方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724893"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して顧客 ID およびアクセス管理で回復性を強化する

[Azure Active Directory (AD) B2C](../../active-directory-b2c/overview.md) は、顧客向けの重要なアプリケーションを正常に起動できるように設計された顧客 ID およびアクセス管理 (CIAM) プラットフォームです。 お客様のニーズに合わせてサービスをスケーリングし、潜在的停止状況に直面したときに回復性を向上させるように設計された、[回復性](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)のための多くの組み込み機能を備えています。 さらに、ミッション クリティカルなアプリケーションを起動するときは、アプリケーションのさまざまな設計と構成の要素、および停止や障害シナリオに応じて回復性のある動作を確保するために、Azure AD B2C 内でのアプリケーションの構成方法についても考慮することが重要です。 この記事では、回復性の向上に役立ついくつかのベスト プラクティスについて説明します。

回復性があるサービスとは、中断が発生しても機能し続けるものです。 サービスの回復性は、次のようにして向上させることができます。

- すべてのコンポーネントを理解する

- 単一障害点をなくす

- 障害の発生したコンポーネントを分離して、影響を抑える

- 高速フェールオーバー メカニズムと復旧パスを使用して冗長性を提供する

アプリケーションを開発する際は、ソリューションの ID コンポーネントを使用して、[アプリケーションで認証と認可の回復性を向上させる](resilience-app-development-overview.md)方法を検討することをお勧めします。 この記事では、Azure AD B2C アプリケーションに固有の回復性の強化について説明します。 推奨事項は、CIAM の機能別にグループ化されています。

![CIAM コンポーネントを示す図。](media/resilience-b2c/high-level-components.png) この後のセクションでは、次の領域での回復性の強化について説明します。

- [エンドユーザー エクスペリエンス](resilient-end-user-experience.md): 認証フローのフォールバック計画を有効にし、Azure AD B2C 認証サービスの中断による潜在的な影響を軽減します。

- [外部プロセスとのインターフェイス](resilient-external-processes.md): エラーから復旧することによって、アプリケーションとインターフェイスでの回復性を強化します。  

- [開発者のベスト プラクティス](resilience-b2c-developer-best-practices.md): 一般的なカスタム ポリシー問題が原因の脆弱性を回避し、要求検証ツール、サードパーティ製アプリケーション、REST API の操作などの領域でのエラー処理を改善します。

- [監視と分析](resilience-with-monitoring-alerting.md): 重要な指標を監視してサービスの正常性を評価し、アラートを使用して障害とパフォーマンスの中断を検出します。

- [認証インフラストラクチャで回復性を強化する](resilience-in-infrastructure.md)

- [アプリケーションで認証と認可の回復性を向上させる](resilience-app-development-overview.md)

Azure AD B2C を使用して回復性があるスケーラブルなフローを構築する方法については、こちらのビデオをご覧ください。
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]
