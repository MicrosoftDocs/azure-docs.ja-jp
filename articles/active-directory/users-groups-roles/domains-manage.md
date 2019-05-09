---
title: カスタム ドメイン名を追加して確認する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory でのドメイン名の管理の概念と方法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ae768ae471ab6f49452c99323c31d34772d45
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201850"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Azure Active Directory のカスタム ドメイン名の管理

ドメイン名は、多くのディレクトリ リソースの識別子の重要な部分です。ユーザーのユーザー名または電子メール アドレスの一部であり、グループのアドレスの一部であり、アプリケーションのアプリ ID URI の一部になることもあります。 Azure Active Directory (Azure AD) のリソースには、リソースを格納するディレクトリによって所有されているドメイン名を含めることができます。 グローバル管理者だけが、Azure AD でドメインを管理できます。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Azure AD ディレクトリのプライマリ ドメイン名の設定

ディレクトリを作成すると、"contoso.onmicrosoft.com" などの初期ドメイン名がプライマリ ドメイン名に設定されます。 プライマリ ドメインは、新しいユーザーを作成したときにそのユーザーの既定のドメイン名になります。 プライマリ ドメイン名の設定によって、管理者がポータルでユーザーを新規作成するプロセスが効率化されます。 プライマリ ドメイン名を変更するには、次の手順に従います。

1. ディレクトリのグローバル管理者であるアカウントで [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を選択します。
3. **[カスタム ドメイン名]** を選択します。
  
   ![ユーザー管理ページを開く](./media/domains-manage/add-custom-domain.png)
4. プライマリ ドメインにするドメインの名前を選びます。
5. **[プライマリにする]** コマンドを選びます。 メッセージが表示されたら、選択を確定します。
  
   ![ドメイン名をプライマリにする](./media/domains-manage/make-primary-domain.png)

ディレクトリのプライマリ ドメイン名を変更して、フェデレーションされていない検証済みカスタム ドメインを指定することができます。 ディレクトリのプライマリ ドメインを変更しても、既存のユーザーのユーザー名は変更されません。

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Azure AD テナントへのカスタム ドメイン名の追加

マネージド ドメインの名前は最大 900 件追加できます。 オンプレミス Active Directory とのフェデレーションをすべてのドメインに構成する場合、各ディレクトリに最大 450 件のドメイン名を追加できます。

## <a name="add-subdomains-of-a-custom-domain"></a>カスタム ドメインのサブドメインの追加

ディレクトリに europe.contoso.com などの第 3 レベル ドメイン名を追加する場合は、最初に、contoso.com などの第 2 レベル ドメインを追加して、確認する必要があります。 サブドメインは、Azure AD によって自動的に検証されます。 追加したサブドメインが検証されたことを確認するには、ブラウザーでドメインの一覧を更新します。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>カスタム ドメイン名の DNS レジストラーを変更する場合にすべきこと

DNS レジストラーを変更する場合、Azure AD で追加の構成タスクはありません。 中断することなく、Azure AD でドメイン名の使用を続けることができます。 Office 365 や Intune など、Azure AD のカスタム ドメイン名を使用するサービスで、自社のカスタム ドメイン名を使用する場合は、各サービスのマニュアルを参照してください。

## <a name="delete-a-custom-domain-name"></a>カスタム ドメイン名を削除する

カスタム ドメイン名が使用されなくなった場合やそのドメイン名を別の Azure AD で使用する必要が生じた場合、Azure AD からドメイン名を削除することができます。

カスタム ドメイン名を削除する場合は、そのドメイン名を使用しているリソースがディレクトリ内にないことを事前に確認する必要があります。 次の状況に当てはまる場合、ディレクトリからドメイン名を削除することはできません。

* ユーザーのユーザー名、電子メール アドレス、またはプロキシ アドレスにドメイン名が含まれている。
* グループに付与された電子メール アドレスまたはプロキシ アドレスにドメイン名が含まれている。
* Azure AD 内のアプリケーションに付与されたアプリ ID URI にドメイン名が含まれている。

Azure AD ディレクトリ内にこのようなリソースがある場合は、カスタム ドメイン名を削除する前に、そのリソースを変更するか削除する必要があります。

### <a name="forcedelete-option"></a>ForceDelete オプション

[Azure AD 管理センター](https://aad.portal.azure.com)で、または [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta) を使用しで、ドメイン名を **ForceDelete** (強制削除) できます。 これらのオプションでは、非同期操作が使用され、"user@contoso.com" のようなカスタム ドメイン名からのすべての参照が、"user@contoso.onmicrosoft.com" などの既定の初期ドメイン名に更新されます。 

Azure portal で **ForceDelete** を呼び出すには、ドメイン名に対する参照が 1000 個未満であることを確認し、Exchange がプロビジョニング サービスであるすべての参照を、[Exchange 管理センター](https://outlook.office365.com/ecp/)で更新または削除する必要があります。 これには、Exchange のメールが有効なセキュリティ グループと配布リストが含まれます。詳しくは、「[Removing mail-enabled security groups](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)」(メールが有効なセキュリティ グループの削除) に関する記事をご覧ください。 また、次のいずれかの場合、**ForceDelete** 操作は成功しません。

* Office 365 ドメイン サブスクリプション サービスを使用してドメインを購入した
* 別の顧客テナントの代わりに管理を行っているパートナーである

**ForceDelete** 操作の一部として、次のアクションが実行されます。

* カスタム ドメイン名を参照しているユーザーの UPN、EmailAddress、ProxyAddress の名前が既定の初期ドメイン名に変更されます。
* カスタム ドメイン名を参照しているグループの EmailAddress の名前が既定の初期ドメイン名に変更されます。
* カスタム ドメイン名を参照しているアプリケーションの identifierUris の名前が既定の初期ドメイン名に変更されます。

次の場合はエラーが返されます。

* 名前を変更されるオブジェクトの数が 1,000 を超える
* 名前を変更されるアプリケーションの 1 つが、マルチテナント アプリである

### <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:このドメイン名に Exchange マスター グループがあることを示すエラーでドメインの削除が失敗するのはなぜですか。** <br>
**A:** 現在、Exchange のメールが有効なセキュリティ グループや配布リストなどの特定のグループは、Exchange によってプロビジョニングされており、[Exchange 管理センター (EAC)](https://outlook.office365.com/ecp/) で手動によりクリーンアップする必要があります。 カスタム ドメイン名に依存していて、手動で別のドメイン名に更新する必要がある、残留 ProxyAddresses が存在する可能性があります。 

**Q:admin\@contoso.com としてログインしても、ドメイン名 "contoso.com" を削除できません。**<br>
**A:** ユーザー アカウント名で削除しようとしているカスタム ドメイン名を参照することはできません。 グローバル管理者アカウントで admin@contoso.onmicrosoft.com のような既定の初期ドメイン名 (. onmicrosoft.com) が使用されていることを確認してください。 admin@contoso.onmicrosoft.com などの別のグローバル管理者アカウント、またはアカウントが admin@fabrikam.com である "fabrikam.com" のような別のカスタム ドメイン名で、サインインしてください。

**Q:[ドメインの削除] ボタンをクリックすると、`In Progress` という削除操作の状態が表示されます。どのくらいの時間がかかりますか?失敗するとどうなりますか。**<br>
**A:** ドメインの削除操作は、ドメイン名に対するすべての参照の名前を変更する非同期のバックグラウンド タスクです。 1 ～ 2 分で完了するはずです。 ドメインの削除が失敗する場合は、次のものがないことを確認してください。

* appIdentifierURI のドメイン名で構成されているアプリ
* カスタム ドメイン名を参照しているメールが有効なグループ
* ドメイン名に対する 1,000 個を超える参照

どの条件も当てはまらないことがわかった場合は、手動で参照をクリーンアップし、もう一度ドメインの削除を試みてください。

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>PowerShell または Graph API を使用してドメイン名を管理する

Azure Active Directory のドメイン名に関する管理作業の多くは、Microsoft PowerShell を使用するか、プログラムから Azure AD Graph API を使用して行うこともできます。

* [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Graph API を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>次の手順

* [カスタム ドメイン名を追加する](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Exchange 管理センターで Exchange のメールが有効なセキュリティ グループを削除する、または Azure AD でカスタム ドメイン名を削除する](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [Microsoft Graph API でカスタム ドメイン名を強制削除する](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
