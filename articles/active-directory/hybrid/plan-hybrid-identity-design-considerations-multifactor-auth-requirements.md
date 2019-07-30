---
title: ハイブリッド ID 設計 - 多要素認証要件 - Azure | Microsoft Docs
description: 条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。 条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4743195fc79d43571ec79a13b8518edc7e81379b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109290"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>ハイブリッド ID ソリューションの多要素認証要件の決定
ユーザーがクラウドのデータとアプリケーションにあらゆるデバイスからアクセスするこのモバイルの世界では、情報を守ることが最重要事項になりました。  毎日、セキュリティ違反に関する話題がニュースになっています。  そのような違反に対抗できる保証はありませんが、多要素認証はセキュリティの層を厚くし、違反防止に役立ちます。
最初に多要素認証の組織要件を評価します。 要件とは、つまり、組織が守ろうとするものです。  多要素認証を設定し、組織のユーザーに使用させるための技術的要件を決める際に、この評価は重要となります。

以下に回答してください。

* 会社は Microsoft アプリを保護する予定ですか。 
* そのアプリはどのような方法で公開されていますか。
* 会社は社員がリモート アクセスでオンプレミス アプリにアクセスできるようにしていますか。

アクセスできるようにしている場合、それはどのような種類のリモート アクセスですか。また、アプリにアクセスするユーザーの所在地を評価する必要があります。 この評価は、適切な多要素認証戦略を決定する際に重要なもう 1 つのステップとなります。 次のチェック項目に回答してください。

* ユーザーはどのような場所からアクセスしますか。
* どこからでもアクセスしますか。
* 会社はユーザーの所在に制約を与える予定ですか。

以上の要件を理解したら、次に行う重要な作業は多要素認証のユーザーの要件も評価することです。 この評価は、多要素認証の展開要件を決定するので重要です。 次のチェック項目に回答してください。

* ユーザーは多要素認証についてよく知っていますか。
* 一部のユーザーには追加認証が必要になりますか。  
  * 必要になる場合、それは常にですか。外部ネットワークからアクセスしたときですか。特定のアプリにアクセスしたときですか。あるいは、他に条件がありますか。
* 多要素認証の設定方法と実装方法を学習するためのトレーニングがユーザーに求められますか。
* 会社がユーザー用の多要素認証を有効にする主なシナリオはどのようなものですか。

以上の質問に答えると、多要素認証がオンプレミスで既に実装されているかどうかがわかります。 多要素認証を設定し、組織のユーザーに使用させるための技術的要件を決める際に、この評価は重要となります。 次のチェック項目に回答してください。

* 会社は MFA で特権アカウントを保護する必要がありますか。
* 会社はコンプライアンス上の理由から特定のアプリに対して MFA を有効にする必要がありますか。
* 会社はアプリのすべての有資格ユーザーに対して MFA を有効にする必要がありますか。それとも、管理者だけですか。
* MFA を常に有効にする必要がありますか。それとも、ユーザーが企業ネットワークの外からログインしたときだけですか。

## <a name="next-steps"></a>次の手順
[ハイブリッド ID 導入戦略の定義](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>関連項目
[設計上の考慮事項の概要](plan-hybrid-identity-design-considerations-overview.md)

