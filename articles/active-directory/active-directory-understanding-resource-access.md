---
title: "Azure でのリソース アクセスについて | Microsoft Docs"
description: "このトピックでは、完全な Azure Portal でのサブスクリプション管理者によるリソース アクセス制御の概念について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2d428e0e3aaf8fd4a2138648411da644ccd308f6
ms.openlocfilehash: 9906aa7ac4296089fd2e41897323e757b181a022
ms.lasthandoff: 02/08/2017


---
# <a name="understanding-resource-access-in-azure"></a>Azure でのリソース アクセスについて
> [!NOTE]
> このトピックでは、完全な Azure Portal でのサブスクリプション管理者によるリソース アクセス制御の概念について説明します。 Azure プレビュー ポータルで [ロール ベースのアクセス制御](role-based-access-control-configure.md) を使用して、Azure リソースをさらに細かく管理することもできます。
> 
> 

2013 年 10 月、Azure クラシック ポータルと Service Management API は Azure Active Directory に統合されました。これは、Azure リソースへのアクセスを管理する際のユーザー エクスペリエンスの向上を目指し、その基盤を構築することを目的としています。 Azure Active Directory には、ユーザー管理、オンプレミスのディレクトリ同期、多要素認証、アプリケーション アクセス制御など、優れた機能が用意されています。 当然、こうした機能も、Azure リソースの管理に伴う作業すべてで使用できます。

Azure のアクセス制御では、最初に課金に注目します。 [Azure アカウント センター](https://account.windowsazure.com/subscriptions)がアクセスする Azure アカウントの所有者は、アカウント管理者 (AA) です。 サブスクリプションは課金用のコンテナーですが、セキュリティの境界としても機能します。 各サブスクリプションはサービス管理者 (SA) を有し、SA は[Azure クラシック ポータル](https://manage.windowsazure.com/)を使ってそのサブスクリプションの Azure リソースを追加、削除、変更することができます。 新しいサブスクリプションの既定の SA は AA ですが、AA は Azure アカウント センターで SA を変更できます。

<br><br>![Azure アカウント][1]

サブスクリプションは、ディレクトリとも関連付けられています。 ディレクトリでは、一連のユーザーを定義します。 たとえば、ディレクトリを作成した職場や学校のユーザーや、外部ユーザー (つまり、Microsoft アカウント) として定義できます。 サブスクリプションは、サービス管理者 (SA) または共同管理者 (CA) のいずれかとして割り当てられているディレクトリ ユーザーのサブセットからアクセス可能です。唯一の例外は、従来版との兼ね合いから、Microsoft アカウント (旧 Windows Live ID) はディレクトリに存在しなくても SA または CA として割り当てることができる、という点です 。

<br><br>![Azure でのアクセス制御][2]

Azure クラシック ポータル内では、Microsoft アカウントを使用してサインインしている SA は、**[設定]** の **[サブスクリプション]** ページから **[ディレクトリの編集]** コマンドを使って、サブスクリプションと関連付けられているディレクトリを変更することができます。 この操作が、サブスクリプションのアクセス制御に対して影響を及ぼすことに注意してください。

> [!NOTE]
> Azure クラシック ポータルの **[ディレクトリの編集]** コマンドは、職場または学校アカウントを使用してサインインしたユーザーは利用できません。こうしたアカウントでは、自分が属しているディレクトリにしかサインインできないからです。
> 
> 

<br><br>![簡単なユーザー ログイン フロー][3]

簡単な例では、組織 (Contoso など) による課金とアクセス制御が、同じサブスクリプション セットに対して適用されます。 つまり、ディレクトリは、1 つの Azure アカウントが所有するサブスクリプションに関連付けられます。 Azure クラシック ポータルに正常にログインすると、2 つのリソース コレクションが表示されます (前の図ではオレンジ色で表示)。

* ユーザー アカウントが存在しているディレクトリ (内部で提供、または外部プリンシパルとして追加)。 ログインに使用するディレクトリがこの計算に関連付けられないこと、したがって、ログインした場所にかかわりなくディレクトリは常に表示されることに注意してください。
* ログインに使用されるディレクトリに関連付けられているサブスクリプションの一部であると同時に、ユーザー (ここでは SA または CA) がアクセスできるリソース。

<br><br>![複数のサブスクリプションおよびディレクトリを持つユーザー][4]

複数のディレクトリにまたがるサブスクリプションを使用しているユーザーは、サブスクリプション フィルターを使用して、Azure クラシック ポータルの現在のコンテキストを切り替えることができます。 内部的には、ディレクトリごとに異なるログインという結果になっていますが、この操作は、シングル サインオン (SSO) によってシームレスに実行されます。

サブスクリプションのディレクトリ ビューが&1; つであるため、複数のサブスクリプション間でリソースを移動する、といった操作はさらに難しくなる可能性があります。 リソースの転送を実行するには、まず最初に **[設定]** の [サブスクリプション] ページから **[ディレクトリの編集]** コマンドを使って、同じディレクトリにサブスクリプションを関連付けます。

## <a name="next-steps"></a>次のステップ
* Azure サブスクリプションの管理者を変更する方法の詳細については、「 [Azure 管理者ロールを追加または変更する方法](../billing/billing-add-change-azure-subscription-administrator.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)
* Azure AD でロールを割り当てる方法の詳細については、「 [Azure Active Directory (Azure AD) の管理者ロールの割り当て](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

