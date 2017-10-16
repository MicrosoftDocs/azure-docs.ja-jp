---
title: "Azure Active Directory の PoC 戦略の成分 |Microsoft ドキュメント"
description: "ID とアクセスを管理するシナリオを探索して迅速に実装します"
services: active-directory
keywords: "Azure Acitve Directory、戦略、概念実証、PoC"
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 7d43be3cbfd63b6bc5f06426e9810a37bbf3d071
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Azure Active Directory の概念実証戦略の成分 

## <a name="theme"></a>テーマ
Azure AD は、エンタープライズ内の複数の領域を通じて ID およびアクセス ソリューションを提供します。 当社では、次の領域でシナリオを分類しています。 

* [多数のアプリ、1 つの ID](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [セキュリティの強化](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [セルフサービスでスケーリングする](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

テーマを定義して PoC を組み立てることで、ビジネス目標の達成に共感を呼ぶ努力に集中しやすくなり、これが最初の段階で概念実証に関心を持つトリガーになることがよくあります。 

## <a name="environment"></a>環境

PoC を実施する環境について詳細を決定するのは重要です。 PoC が完了してから詳細を構築できることが理想です。 ターゲット環境は非常に重要であり、制約のオーバーヘッドや追加の考慮事項との間でできるだけ現実的で適切なバランスを見つける必要があります。 PoC の一般的な環境は次のとおりです。
* **運用環境:** Microsoft クラウド サービス (運用 AD、Office 365、Azure AD のテナント/SSO ソリューション) が既にデプロイされている、実際の環境にシナリオが実装されます 
* **ユーザー受け入れテスト (UAT)/開発環境:** 運用環境に類似するテスト データを備えたテスト インフラストラクチャ (並列 AD と潜在的な Azure AD テナント/SSO ソリューション) がある環境です。 通常、テスト環境は、企業内の複数のプロジェクトで共有されます。

このガイドのほとんどのシナリオは、本質的に、追加によって実施されます。 その結果、PoC 外のユーザーに影響を与えずに、運用環境のテナントにデプロイできます。 このドキュメント全体を通じ、どのシナリオがテナント全体に影響するかを決定します。 影響がある場合は、非運用環境を検討することがあります。 


## <a name="target-users"></a>対象ユーザー

環境が運用環境またはテストの場合は特に、シナリオを実行するターゲットのユーザー セットを決定することが重要です。 PoC の対象ユーザーとしては、次のようなカテゴリがあります。
* **パイロット ユーザー:** 日常のジョブ機能に使用するアカウントでソリューションを使用する、環境内の実際のユーザー
* **テスト ユーザー:** 環境で作成されたテスト アカウント 

このガイドのほとんどのシナリオは、パイロット ユーザーで実行することができます。 このドキュメント全体を通じ、必要に応じてターゲット ユーザーを決定します。


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]