---
title: 脅威 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Microsoft Threat Modeling Tool の脅威のカテゴリ ページには、生成された危険な全脅威のカテゴリが表示されます。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727843"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool の脅威

Threat Modeling Tool は、Microsoft セキュリティ開発ライフサイクル (SDL) の主要な要素です。 これを使用すると、ソフトウェア アーキテクトは早い段階で潜在的なセキュリティの問題を特定し、危険を軽減することができます。早い段階であれば、問題の解決は比較的容易で、コスト効率も良くなります。 そのため、開発総コストを大幅に軽減できます。 また、このツールはセキュリティの専門家ではないユーザーを想定して設計され、脅威モデルの作成と分析に関するわかりやすいガイダンスが用意されているため、すべての開発者が簡単に脅威をモデリングできます。

> **[Threat Modeling Tool](threat-modeling-tool.md)** にアクセスして今日から始めましょう。

Threat Modeling Tool を使用すると、以下のような特定の質問に答えることができます。

* 攻撃者はどのような方法で認証データを変更できるか。
* 攻撃者がユーザー プロファイル データを読み取ることができる場合、どのような影響があるか。
* ユーザー プロファイル データベースへのアクセスが拒否された場合、何が起きるか。

## <a name="stride-model"></a>STRIDE モデル

これらの種類の特定の質問をより適切に定式化できるように、Microsoft は STRIDE モデルを使用しています。このモデルではさまざまな種類の脅威を分類し、セキュリティに関する会話全体を簡単にします。

| カテゴリ | 説明 |
| -------- | ----------- |
| **なりすまし** | 他のユーザーの認証情報 (ユーザー名、パスワードなど) に不正にアクセスし、それを使用する行為などです |
| **改ざん** | 悪意のあるデータの変更などです。 例としては、データベースに保持されているような永続的なデータに対する許可されていない変更や、インターネットなどのオープン ネットワーク経由で 2 台のコンピューター間を流れるデータの変更などがあります |
| **否認** | 反証できる関係者がいない状況でアクションの実行を否定するユーザーに関連するものです。たとえば、禁止されている操作を追跡できる機能がないシステムでユーザーが不正な操作を行うような場合です。 否認防止とは、否認の脅威に対抗するシステムの機能のことです。 たとえば、商品を購入するユーザーは、受け取り時に署名をする必要があります。 販売者は、署名された受領書をユーザーが荷物を受け取ったことの証拠として使用することができます |
| **情報漏えい** | 情報へのアクセスが想定されていない個人への情報の暴露などです。たとえば、アクセスが許可されていないファイルをユーザーが読み取ることができたり、侵入者が2 台のコンピューター間で送信されるデータを読み取ることができたりする場合です |
| **サービス拒否** | サービス拒否 (DoS) 攻撃では、有効なユーザーへのサービスが拒否されます。たとえば、Web サーバーを一時的に使用できなくする行為です。 システムの可用性と信頼性を向上させるために、特定の種類の DoS 脅威からシステムを保護する必要があります |
| **特権の昇格** | 特権のないユーザーが特権的なアクセスを取得すると、システム全体を侵害したり破壊したりできるようになります。 特権の昇格の脅威には、攻撃者が効果的にすべてのシステム防御を破り、信頼されているシステム自体の一部となる、本当に危険な状況が含まれます |

## <a name="next-steps"></a>次のステップ

「**[Threat Modeling Tool Mitigations](threat-modeling-tool-mitigations.md)**」(Threat Modeling Tool の軽減策) に進み、これらの脅威 Azure で軽減する方法について学びます。