---

title: "Azure Active Directory のグループベースのライセンスとは | Microsoft Docs"
description: "Azure Active Directory のグループベース ライセンスと、その仕組み、使用方法、およびベスト プラクティスに関する説明"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: b5153d2339f688b22796789d74cb9117985a43d2
ms.lasthandoff: 03/09/2017


---

# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Azure Active Directory のグループベースのライセンスの基礎

マイクロソフトの Office 365、Enterprise Mobility + Security、Dynamics CRM などのクラウド サービスやその他の類似製品では、ライセンスが必要です。 これらのライセンスは、各サービスにアクセスする必要があるユーザーにそれぞれ割り当てられます。 ライセンス管理は、管理者が管理ポータル (Office、Azure) と PowerShell コマンドレットのどちらかを使用して行います。 マイクロソフトのすべてのクラウド サービスの ID を管理する基盤インフラストラクチャは、Azure Active Directory (Azure AD) です。 ユーザーのライセンスの割り当て状態に関する情報は Azure AD に格納されます。

従来、ライセンスは個々のユーザー レベルでしか割り当てることができず、大規模な管理を行いづらい場合がありました。 たとえば、組織や部署でのユーザーの異動などの組織の変化に応じてユーザー ライセンスの追加または削除を行うには、管理者は多くの場合、複雑な PowerShell スクリプトを記述する必要がありました。 このスクリプトは、クラウド サービスを一つ一つ呼び出します。

これらの課題を受けて、Azure AD はグループベース ライセンス機能が搭載されました。 この機能により、1 つのグループに&1; つ以上の製品ライセンスを割り当てることができます。 グループに含まれるメンバー全員に、Azure AD からライセンスが割り当てられるようになります。 新しいメンバーがグループに参加すると、適切なライセンスが割り当てられます。 グループから抜けると、割り当てられていたライセンスが削除されます。 これにより、組織や部門の構造の変化を反映するように PowerShell でユーザーごとのライセンス管理を自動化する必要はなくなりました。

## <a name="features"></a>Features (機能)

グループベースのライセンスの主な機能は次のとおりです。

- ライセンスを、Azure AD 内の任意のセキュリティ グループに割り当てられます。 セキュリティ グループは、Azure AD Connect を使用してオンプレミスから同期できます。 また、セキュリティ グループは Azure AD 内で直接作成する (クラウド専用グループとも呼ばれます) ことも、Azure AD の動的グループ機能で自動作成することもできます。

- 製品ライセンスをグループに割り当てる場合、管理者はその製品の&1; つまたは複数のサービス プランを無効にできます。 通常、これは製品に含まれているサービスを使用する準備が整っていない組織が行います。 たとえば、管理者は Office 365 を部署に割り当てる一方で、Yammer のサービスを一時的に無効にできます。

- ユーザーレベルのライセンスが必要なすべての Microsoft クラウド サービスがサポートされています。 サポート対象は、全 Office 365 製品、Enterprise Mobility + Security、Dynamics CRM などです。

- グループベースのライセンスは、現時点では [Azure Portal](https://portal.azure.com) を介してのみ使用できます。 ユーザーとグループの管理に主に別の管理ポータル (Office 365 ポータルなど) を使用する場合は、引き続き使用できます。 ただし、グループ レベルでライセンスを管理するには Azure Portal の使用をお勧めします。

- グループのメンバー変更に伴うライセンスの変更は、Azure AD により自動で管理されます。 通常、ライセンスの変更はメンバーシップを変更した数分以内に有効になります。

- ユーザーは、ライセンス ポリシーが指定された複数のグループのメンバーになることができます。 また、グループ外で直接割り当てられたライセンスを使用することもできます。 ユーザーの状態は、割り当て済みのすべての製品ライセンスとサービス ライセンスの組み合わせたものになります。

- 場合によっては、ユーザーにライセンスを割り当てることができません。 たとえば、テナントに十分な数のライセンスがない場合や、同時に競合するサービスが割り当てられている場合などがあります。 管理者は、グループ ライセンスを Azure AD で完全には処理できないユーザーに関する情報へアクセスできます。 こうした情報に基づいて是正措置を取ることができます。

- パブリック プレビュー中にグループベース ライセンス管理を使用するには、テナントに Azure AD の Basic または Premium Edition の有料サブスクリプション、または試用版サブスクリプションが必要になります。 また、グループからライセンスを継承するユーザー全員に、有料の Azure AD エディション ライセンスが割り当てられている必要があります。

## <a name="next-steps"></a>次のステップ

グループベースのライセンスを通じたライセンス管理の他のシナリオについては、以下をご覧ください

* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory グループベース ライセンスのその他のシナリオ](active-directory-licensing-group-advanced.md)

