---
title: "Azure Security Center におけるアクセス許可 | Microsoft Docs"
description: "この記事では、Azure Security Center でロールベースのアクセス制御を使用してアクセス許可をユーザーに割り当て、ロールごとに許可するアクションを特定する方法について説明します。"
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5c4a08d96175d431e0a29dfc5927b64567c40117
ms.openlocfilehash: d1c8a39bbcda7d22fdce08d122098e994ca87451


---

# <a name="permissions-in-azure-security-center"></a>Azure Security Center におけるアクセス許可

Azure Security Center では、[ロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) が使用されています。RBAC が提供する[組み込みのロール](../active-directory/role-based-access-built-in-roles.md)は、Azure でユーザー、グループ、サービスに割り当てることができます。

Security Center は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。

## <a name="roles-and-allowed-actions"></a>ルールと許可されているアクション

次の表では、Security Center のロールと許可されているアクションが表示します。 ○ は、アクションがそのロールに許可されていることを示します。

| ロール | セキュリティ ポリシーを編集する | セキュリティに関する推奨事項をリソースに適用する | アラートを修復または解除する | サブスクリプション全体でアラートを表示する | 特定のリソースに関するアラートを表示する |
|:--- |:---:|:---:|:---:|:---:|:---:|
| サブスクリプションの所有者 | ○ | ○ | ○ | ○ | ○ |
| サブスクリプションの共同作成者 | ○ | ○ | ○ | ○ | ○ |
| リソース グループの所有者 | -- | ○ | -- | -- | ○ |
| リソース グループの共同作成者 | -- | ○ | -- | -- | ○ |
| 閲覧者 | -- | -- | -- | ○ | ○ |

> [!NOTE]
> タスクを実行するために必要となる最小限の権限ロールをユーザーに割り当てることをお勧めします。 たとえば、リソースのセキュリティ正常性に関する情報の表示のみが必要で、推奨事項の適用やポリシーの編集などの操作を行う必要がないユーザーには、閲覧者ロールを割り当ててください。
>
>

## <a name="next-steps"></a>次のステップ
この記事では、Security Center で RBAC を使用してユーザーにアクセス許可を割り当てる方法について説明し、各ロールに許可されているアクションを示しました。 サブスクリプションのセキュリティ状態を監視するために必要なロールの割り当てについて理解したら、セキュリティ ポリシーを編集し、推奨事項を適用して、次の方法を学習してください。

- [Security Center でセキュリティ ポリシーを設定する](security-center-policies.md)
- [Security Center でセキュリティに関する推奨事項を管理する](security-center-recommendations.md)
- [Azure リソースのセキュリティ正常性を監視する](security-center-monitoring.md)
- [Security Center でセキュリティのアラートの管理と対応を行う](security-center-managing-and-responding-alerts.md)
- [パートナー セキュリティ ソリューションを監視する](security-center-partner-solutions.md)



<!--HONumber=Dec16_HO2-->


