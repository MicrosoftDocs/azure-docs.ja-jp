---
title: "Azure Active Directory の PoC 戦略の概要 |Microsoft ドキュメント"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: bca54013e765315d2bbf2691503872f5e9ca859a
ms.contentlocale: ja-jp
ms.lasthandoff: 04/12/2017

---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Azure Active Directory の概念実証戦略: 概要

この記事では、Azure AD の概念実証 (PoC) に関するさまざまな機能を検索するためのガイドラインを提供します。 このドキュメントの対象読者は、ID アーキテクト、IT プロフェッショナル、およびシステム インテグレーターです

## <a name="how-to-use-this-playbook"></a>この戦略の使用方法

1. [[テーマ]](active-directory-playbook-ingredients.md#theme) セクションを使用して、ニーズに合わせて関心のある領域を選択します。  
2. ビジネス目標に合致するシナリオを選択して、PoC の範囲を決定します。 短い方が良くなります。 実現するための困難を最小限にしながら、関係者に有益になるようにするために、範囲は簡潔で正確に設定することをお勧めします。  
3. シナリオを理解し、自身の環境にどのような意味があるか把握するために、[[実装]](active-directory-playbook-implementation.md) セクションを使用します。 シナリオごとに、設定方法 ([構成ブロック](active-directory-playbook-building-blocks.md)と呼びます) とシナリオ間の移動の仕方を説明します。 
4. 構成ブロックごとに、おおよその所要時間と、必要な前提条件について説明します。 これによって計画プロセスが実行しやすくなります。 
5. 上記の 1～3 に基づいて、実行する[環境](active-directory-playbook-ingredients.md#environment)を定義します。 ユーザーが良好な体験感覚を得られるような運用環境になるように努めていただくことをお願いします。 
6. 競合する要件がある場合は、次の便利なトレードオフ マトリックスを使用してください。 
   * テーマを中心とした値を表示する  
   * シナリオを円滑に準備、設定、および実行する 
   * 対象となるシナリオの実行時間をできるだけ短くする 
   * 制約内での運用環境の実現可能性をできるだけ高める 

>[!NOTE]
> この記事全体では、便宜上、特定のサード パーティ製の数種類のアプリケーションと製品をサンプルとして取り挙げています。 Azure AD では、[アプリケーション ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)で、ニーズや環境に応じて使用できる多数のアプリケーションをサポートしています。 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
