---
title: Azure AD Domain Services の範囲指定された同期 | Microsoft Docs
description: Azure portal を使用して、Azure AD から Azure Active Directory Domain Services マネージド ドメインへの範囲指定された同期を構成する方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34692f5e563e4931a27ea59db84d9c88f27817da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660900"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure portal を使用して、Azure AD から Azure Active Directory Domain Services への範囲指定された同期を構成する

Azure Active Directory Domain Services (Azure AD DS) は、認証サービスを提供するため、Azure AD からユーザーとグループを同期します。 ハイブリッド環境では、最初にオンプレミスの Active Directory Domain Services (AD DS) 環境のユーザーとグループが Azure AD Connect を使用して Azure AD に同期された後、Azure AD DS マネージド ドメインに同期されます。

既定では、Azure AD ディレクトリのすべてのユーザーとグループがマネージド ドメインに同期されます。 特定のニーズがある場合は、代わりに、定義したユーザー セットのみを同期することを選択できます。

この記事では、Azure portal 使用して、範囲指定された同期を構成した後、範囲指定されたユーザー セットを変更したり、無効にしたりする方法について説明します。 [これらの手順は、PowerShell を使用して行う][scoped-sync-powershell]こともできます。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメインを作成して構成する][tutorial-create-instance]チュートリアルを完了します。
* Azure AD DS 同期スコープを変更するには、Azure AD テナントでの "*全体管理者*" 特権が必要です。

## <a name="scoped-synchronization-overview"></a>範囲指定された同期の概要

既定では、Azure AD ディレクトリのすべてのユーザーとグループがマネージド ドメインに同期されます。 マネージド ドメインにアクセスする必要のあるユーザーが少数しかいない場合は、それらのユーザー アカウントのみを同期することができます。 この範囲指定された同期はグループベースです。 グループベースの範囲指定された同期を構成した場合、指定したグループに属するユーザー アカウントのみがマネージド ドメインに同期されます。 入れ子になったグループは同期されません。選択した特定のグループのみが同期されます。

同期スコープは、マネージド ドメインを作成する前または後に変更できます。 同期のスコープは、アプリケーション識別子 2565bd9d-da50-47d4-8b85-4c97f669dc36 を使用してサービス プリンシパルによって定義されます。 スコープが失われないようにするには、サービス プリンシパルを削除または変更しないでください。 誤って削除された場合、同期スコープを復旧できません。 

同期スコープを変更する場合は、次の点にご注意ください。

- 完全同期が行われます。
- マネージド ドメインで不要になったオブジェクトは削除されます。 新しいオブジェクトは、マネージド ドメインに作成されます。

同期プロセスの詳細については、[Azure AD Domain Services での同期の理解][concepts-sync]に関する記事を参照してください。

## <a name="enable-scoped-synchronization"></a>範囲指定された同期を有効にする

Azure portal で範囲指定された同期を有効にするには、次の手順を行います。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. 左側のメニューで、 **[同期]** を選択します。
1. *[同期の種類]* で、 **[範囲指定]** を選択します。
1. **[グループの選択]** を選択し、追加するグループを検索して選択します。
1. すべての変更が行われたら、 **[同期スコープを保存します]** を選択します。

同期のスコープを変更すると、マネージド ドメインですべてのデータが再同期されます。 マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでにはしばらく時間がかかる場合があります。

## <a name="modify-scoped-synchronization"></a>範囲指定された同期を変更する

マネージド ドメインにユーザーが同期されるグループの一覧を変更するには、次の手順を行います。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. 左側のメニューで、 **[同期]** を選択します。
1. グループを追加するには、上部にある **[+ グループの選択]** を選択し、追加するグループを選択します。
1. 同期スコープからグループを削除するには、現在同期されているグループの一覧からそのグループを選択し、 **[グループの削除]** を選択します。
1. すべての変更が行われたら、 **[同期スコープを保存します]** を選択します。

同期のスコープを変更すると、マネージド ドメインですべてのデータが再同期されます。 マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでにはしばらく時間がかかる場合があります。

## <a name="disable-scoped-synchronization"></a>範囲指定された同期を無効にする

マネージド ドメインのグループベースの範囲指定された同期を無効にするには、次の手順を行います。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. 左側のメニューで、 **[同期]** を選択します。
1. *[同期の種類]* を **[範囲指定]** から **[すべて]** に変更し、 **[同期スコープを保存します]** を選択します。

同期のスコープを変更すると、マネージド ドメインですべてのデータが再同期されます。 マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでにはしばらく時間がかかる場合があります。

## <a name="next-steps"></a>次のステップ

同期プロセスの詳細については、[Azure AD Domain Services での同期の理解][concepts-sync]に関する記事を参照してください。

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
