---
title: グループベースのライセンスとは - Azure Active Directory | Microsoft Docs
description: Azure Active Directory のグループベースのライセンスについて、使用方法やベスト プラクティスなどを説明します。
services: active-directory
keywords: Azure AD のライセンス
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0490334c759da6ef7ba7ff2535f5f561cdb7a9bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369813"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Azure Active Directory のグループベースのライセンスとは

マイクロソフトの Microsoft 365、Enterprise Mobility + Security、Dynamics 365 などの有料クラウド サービスやその他の類似製品では、ライセンスが必要です。 これらのライセンスは、各サービスにアクセスする必要があるユーザーにそれぞれ割り当てられます。 ライセンス管理は、管理者が管理ポータル (Office、Azure) と PowerShell コマンドレットのどちらかを使用して行います。 マイクロソフトのすべてのクラウド サービスの ID を管理する基盤インフラストラクチャは、Azure Active Directory (Azure AD) です。 ユーザーのライセンスの割り当て状態に関する情報は Azure AD に格納されます。

従来、ライセンスは個々のユーザー レベルでしか割り当てることができず、大規模な管理を行いづらい場合がありました。 たとえば、組織や部署でのユーザーの異動などの組織の変化に応じてユーザー ライセンスの追加または削除を行うには、管理者は多くの場合、複雑な PowerShell スクリプトを記述する必要がありました。 このスクリプトは、クラウド サービスを一つ一つ呼び出します。

これらの課題を受けて、Azure AD はグループベース ライセンス機能が搭載されました。 この機能により、1 つのグループに 1 つ以上の製品ライセンスを割り当てることができます。 グループに含まれるメンバー全員に、Azure AD からライセンスが割り当てられるようになります。 新しいメンバーがグループに参加すると、適切なライセンスが割り当てられます。 グループから抜けると、割り当てられていたライセンスが削除されます。 このライセンス管理により、組織や部門の構造におけるユーザー単位の変化を反映するように、PowerShell でライセンス管理を自動化する必要はなくなります。

## <a name="licensing-requirements"></a>ライセンスの要件
グループベースのライセンスを使用するには、次のいずれかのライセンスが必要です。

- Azure AD Premium P1 以上の有料または試用版のサブスクリプション

- Office 365 Enterprise E3、Office 365 A3、Office 365 GCC G3、Office 365 E3 for GCCH、Office 365 E3 for DOD 以上の有料または試用版のエディション

### <a name="required-number-of-licenses"></a>必要なライセンスの数
ライセンスが割り当てられるすべてのグループについて、個々のメンバーに対するライセンスも必要です。 グループの各メンバーにライセンスを割り当てる必要はありませんが、すべてのメンバーが含まれる十分な数のライセンスが少なくとも必要です。 たとえば、テナントのライセンス グループの一部として 1,000 人の個別メンバーがいる場合、ライセンス契約を満たすには、少なくとも 1,000 ライセンスが必要です。

## <a name="features"></a>[機能]

グループベースのライセンスの主な機能は次のとおりです。

- ライセンスを、Azure AD 内の任意のセキュリティ グループに割り当てられます。 セキュリティ グループは、Azure AD Connect を使用してオンプレミスから同期できます。 また、セキュリティ グループは Azure AD 内で直接作成する (クラウド専用グループとも呼ばれます) ことも、Azure AD の動的グループ機能で自動作成することもできます。

- 製品ライセンスをグループに割り当てる場合、管理者はその製品の 1 つまたは複数のサービス プランを無効にできます。 通常、この割り当ては、製品に含まれるサービスを組織で使用する準備が整っていないときに行います。 たとえば、管理者は Microsoft 365 を部署に割り当てる一方で、Yammer のサービスを一時的に無効にできます。

- ユーザーレベルのライセンスが必要なすべての Microsoft クラウド サービスがサポートされています。 このサポートには、すべての Microsoft 365 製品、Enterprise Mobility + Security、Dynamics 365 が含まれます。

- グループベースのライセンスは、現時点では [Azure portal](https://portal.azure.com) を介してのみ使用できます。 ユーザーとグループの管理に主に別の管理ポータル ([Microsoft 365 管理センター](https://admin.microsoft.com)など) を使用する場合は、引き続き使用できます。 ただし、グループ レベルでライセンスを管理するには Azure Portal の使用をお勧めします。

- グループのメンバー変更に伴うライセンスの変更は、Azure AD により自動で管理されます。 通常、ライセンスの変更はメンバーシップを変更した数分以内に有効になります。

- ユーザーは、ライセンス ポリシーが指定された複数のグループのメンバーになることができます。 また、グループ外で直接割り当てられたライセンスを使用することもできます。 ユーザーの状態は、割り当て済みのすべての製品ライセンスとサービス ライセンスの組み合わせたものになります。 ユーザーに複数のソースから同じライセンスが割り当てられている場合、ライセンスは 1 回だけ使用されます。

- 場合によっては、ユーザーにライセンスを割り当てることができません。 たとえば、テナントに十分な数のライセンスがない場合や、同時に競合するサービスが割り当てられている場合などがあります。 管理者は、グループ ライセンスを Azure AD で完全には処理できないユーザーに関する情報へアクセスできます。 こうした情報に基づいて是正措置を取ることができます。

## <a name="your-feedback-is-welcome"></a>ご意見をお待ちしております。

ご意見や機能に関するご要望がありましたら、[Azure AD 管理フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510)をご利用ください。

## <a name="next-steps"></a>次のステップ

グループベースのライセンスを通じたライセンス管理の他のシナリオについては、以下をご覧ください

* [Assigning licenses to a group in Azure Active Directory](../enterprise-users/licensing-groups-assign.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](../enterprise-users/licensing-groups-resolve-problems.md)
* [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](../enterprise-users/licensing-groups-migrate-users.md)
* [Azure Active Directory のグループベースのライセンスを使用して製品ライセンス間でユーザーを移行する方法](../enterprise-users/licensing-groups-change-licenses.md)
* [Azure Active Directory グループベース ライセンスのその他のシナリオ](../enterprise-users/licensing-group-advanced.md)
* [Azure Active Directory のグループベースのライセンスの PowerShell の例](../enterprise-users/licensing-ps-examples.md)
