---
title: Azure Security Center での ID とアクセスの監視 | Microsoft Docs
description: Azure Security Center の ID とアクセス機能を使用して、ユーザーのアクセス アクティビティと ID 関連の問題を監視する方法を説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: v-mohabe
ms.openlocfilehash: 73480c6e24ab13b7764c5b72280c19971eb3d039
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296486"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Azure Security Center での ID とアクセスの監視 (プレビュー)
この記事は、Azure Security Center を使用してユーザーの ID とアクセス アクティビティを監視する際に役立ちます。

> [!NOTE]
> [*クラシック* ID とアクセスの表示] リンクは、2019 年 7 月 31 日をもって廃止されます。 代わりのサービスについては、[ここ](security-center-features-retirement-july2019.md#menu_classicidentity)をクリックしてください。

> [!NOTE]
> ID とアクセスの監視はプレビュー段階であり、Security Center の Standard レベルでのみ利用できます。 Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>

ID は企業のコントロール プレーンであり、ID の保護は最優先事項です。 セキュリティ境界は、ネットワーク境界から ID 境界へと発展してきました。 セキュリティにおいては、ネットワークの保護よりもデータの保護、アプリとユーザーのセキュリティ管理が重要になってきています。 最近では、クラウドに移行するデータやアプリが増加しているため、ID が新たな境界線となっています。

ID アクティビティを監視することにより、インシデントが発生する前に事前対応型のアクションを実行するか、攻撃が試みられた場合にそれを阻止する事後対応型のアクションを実行することができます。 [Identity & Access]\(ID およびアクセス\) ダッシュボードには、次のような推奨事項が表示されます。

- サブスクリプションで特権アカウントの MFA を有効にする
- 書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する
- 外部の特権アカウントをサブスクリプションから削除する

> [!NOTE]
> サブスクリプションのアカウント数が 600 を超える場合、Security Center はサブスクリプションに対して ID の推奨事項を実行できません。 実行されない推奨事項は、後述する [利用できない評価] に一覧表示されます。
Security Center は、クラウド ソリューション プロバイダー (CSP) パートナーの管理者エージェントに対して ID の推奨事項を実行できません。
>

Security Center が提供する ID およびアクセスの推奨事項一覧については、「[推奨事項](security-center-identity-access.md#recommendations)」を参照してください。

## <a name="monitoring-security-health"></a>セキュリティ ヘルスの監視
リソースのセキュリティ状態は、 **[Security Center - 概要]** ダッシュボードで監視できます。 **[リソース]** セクションは、各リソースの種類の重大度を示す正常性のインジケーターです。

**[推奨事項]** を選択すると、すべての問題の一覧が表示されます。 **[リソース]** には、計算とアプリ、データ セキュリティ、ネットワーク、ID と アクセスに固有の問題の一覧が表示されます。 レコメンデーションの適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。

ID とアクセスの推奨事項の詳細な一覧については、「[推奨事項](security-center-identity-access.md#recommendations)」を参照してください。

続行するには、 **[リソース]** または Security Center のメイン メニューで **[Identity & Access]\(ID およびアクセス\)** を選択します。

![Security Center ダッシュボード][1]

## <a name="monitor-identity-and-access"></a>ID とアクセスを監視する
**[Identity & Access]\(ID およびアクセス\)** には 2 つのタブがあります。

- **概要**: Security Center によって特定された推奨事項。
- **サブスクリプション**: サブスクリプションと各サブスクリプションの現在のセキュリティ状態の一覧。

![[Identity & Access]\(ID およびアクセス\)][2]

### <a name="overview-section"></a>[概要] セクション
**[概要]** には、推奨事項の一覧が表示されます。 最初の列には、推奨事項が一覧表示されます。 2 列目には、その推奨事項の影響を受けるサブスクリプションの合計数が表示されます。 3 列目には、問題の重大度が表示されます。

1. 推奨事項を選択します。 推奨事項のウィンドウが開き、次の内容が表示されます。

   - 推奨事項の説明
   - 異常な状態と正常な状態のサブスクリプションの一覧
   - 評価が失敗したためにスキャンされなかったリソース、またはリソースが無料レベルで実行されているサブスクリプションのリソースで評価対象ではないリソースの一覧

   ![推奨事項のウィンドウ][3]

1. さらに詳細を確認するには、一覧のサブスクリプションを選択します。

### <a name="subscriptions-section"></a>[サブスクリプション] セクション
**[サブスクリプション]** には、サブスクリプションの一覧が表示されます。 最初の列には、サブスクリプションが一覧表示されます。 2 列目には、各サブスクリプションの推奨事項の合計数が表示されます。 3 列目には、問題の重大度が表示されます。

![サブスクリプションのタブ][4]

1. サブスクリプションを選択します。 3 つのタブがある概要ビューが開きます。

   - **推奨事項**: Security Center によって実行され、失敗した評価に基づいて表示されます。
   - **評価に合格しました**: Security Center によって実行され、合格した評価の一覧。
   - **利用できない評価**: エラーのため、またはアカウント数が 600 を超えるサブスクリプションのため、実行に失敗した評価の一覧。

   **[推奨事項]** には、選択したサブスクリプションの推奨事項と各推奨事項の重大度の一覧が表示されます。

   ![選択したサブスクリプションの推奨事項][5]

1. 推奨事項を選択すると、推奨事項の説明、異常な状態と正常な状態のサブスクリプションの一覧、スキャンされていないリソースの一覧が表示されます。

   ![推奨事項の説明][6]

   **[評価に合格しました]** には、合格した評価の一覧が表示されます。  これらの評価の重大度は常に緑色です。

   ![評価に合格しました][7]

1. 一覧から合格した評価を選択すると、評価の説明と正常な状態のサブスクリプションの一覧が表示されます。 異常な状態のサブスクリプションのタブがあり、失敗したすべてのサブスクリプションの一覧が表示されます。

   ![評価に合格しました][8]

## <a name="recommendations"></a>Recommendations
次の表を参考にすると、利用できる [Identity & Access]\(ID およびアクセス\) の推奨事項と、それを適用した場合のそれぞれの結果が理解しやすくなります。

|リソースの種類|セキュリティ スコア|推奨|説明|
|----|----|----|----|
|Subscription|50|サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある|アカウントまたはリソースの侵害を防止するために、管理者特権を持つすべてのサブスクリプション アカウントに対して Multi-Factor Authentication (MFA) を有効にします。|
|Subscription|40|書き込みアクセス許可を持つサブスクリプション アカウントに対して MFA を有効にする必要がある|アカウントまたはリソースの侵害を防止するために、書き込み特権を持つすべてのサブスクリプション アカウントに対して Multi-Factor Authentication (MFA) を有効にします。|
|Subscription|30|所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|監視されていないアクセスを防止するために、所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除します。|
|Subscription|30|読み取りアクセス許可を持つサブスクリプション アカウントに対して MFA を有効にする必要がある|アカウントまたはリソースの侵害を防止するために、読み取り特権を持つすべてのサブスクリプション アカウントに対して Multi-Factor Authentication (MFA) を有効にします。|
|Subscription|25|書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|監視されていないアクセスを防止するために、書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除します。 |
|Subscription|20|所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある|所有者アクセス許可を持つ非推奨のアカウントをサブスクリプションから削除します。|
|Subscription|5|非推奨のアカウントをサブスクリプションから削除する必要がある|現在のユーザーのみへのアクセスを有効にするために、非推奨のアカウントをサブスクリプションから削除します。 |
|Subscription|5|複数の所有者がサブスクリプションに割り当てられている必要がある|管理者アクセスの冗長性を確保するために、複数のサブスクリプション所有者を指定します。|
|Subscription|5|最大 3 人の所有者をサブスクリプションに対して指定する必要がある|セキュリティ侵害を受けた所有者による侵害の可能性を軽減するために、3 名未満のサブスクリプション所有者を指定します。|
|Key Vault|5|Key Vault で診断ログを有効にする必要がある|ログを有効にし、それらを最大 1 年間保持します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |
|Subscription|15|読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある|監視されていないアクセスを防止するために、読み取り特権を持つ外部アカウントをサブスクリプションから削除します。| 

> [!NOTE]
> MFA を必要とするが、除外が設定された条件付きアクセス ポリシーを作成している場合、そのポリシーは一部のユーザーを MFA なしで Azure にサインインできるようにするため、Security Center MFA の推奨事項の評価では非準拠と見なされます。

## <a name="next-steps"></a>次の手順
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

- [Azure Security Center でのマシンとアプリケーションの保護](security-center-virtual-machine-recommendations.md)
- [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)
- [Azure Security Center での Azure SQL サービスとデータの保護](security-center-sql-service-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。
* [Azure Security Center でのセキュリティ アラートの管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 Security Center の使用に関してよく寄せられる質問とその回答を紹介しています。


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
