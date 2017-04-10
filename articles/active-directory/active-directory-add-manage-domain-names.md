---
title: "Azure Active Directory でのカスタム ドメイン名の管理 | Microsoft Docs"
description: "Azure Active Directory でのカスタム ドメインの管理の概念と方法"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cf3523bd-9ee0-439e-963d-ccea038867b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 77d857e8d692f8bf47e00d655071a825533290b6


---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Azure Active Directory のカスタム ドメイン名の管理
ドメイン名は、多くのディレクトリ リソースの識別子の重要な部分です。ユーザーのユーザー名または電子メール アドレスの一部であり、グループのアドレスの一部であり、アプリケーションのアプリ ID URI の一部になることもあります。 Azure Active Directory (Azure AD) のリソースには、リソースを格納するディレクトリによって所有されていることが既に確認済みのドメイン名を含めることができます。 Azure AD でドメイン管理タスクを実行できるのは、グローバル管理者のみです。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Azure AD ディレクトリのプライマリ ドメイン名の設定
ディレクトリを作成すると、contoso.onmicrosoft.com などの初期のドメイン名が、ディレクトリのプライマリ ドメイン名に設定されます。 プライマリ ドメインは、 [Azure クラシック ポータル](https://manage.windowsazure.com/)や Office 365 管理ポータルなどの他のポータルでユーザーを新規作成する際に、新しいユーザーの既定のドメイン名になります。 この措置によって、管理者がポータルでユーザーを新規作成するプロセスが効率化されています。

ディレクトリのプライマリ ドメイン名を変更するには、次の手順に従います。

1. Azure AD ディレクトリのグローバル管理者のユーザー アカウントで [Azure クラシック ポータル](https://manage.windowsazure.com/) にサインインします。
2. 左のナビゲーション バーで **[Active Directory]** を選択します。
3. ディレクトリを開きます。
4. **[ドメイン]** タブを選択します。
5. コマンド バーの **[プライマリの変更]** をクリックします。
6. ディレクトリの新しいプライマリ ドメインに設定するドメインを選択します。

ディレクトリのプライマリ ドメイン名を変更して、フェデレーションされていない検証済みカスタム ドメインを指定することができます。 ディレクトリのプライマリ ドメインを変更しても、既存のユーザーのユーザー名は変更されません。

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Azure AD へのカスタム ドメイン名の追加
1 つの Azure AD ディレクトリに最大 900 個のカスタム ドメイン名を追加できます。 [カスタム ドメイン名を追加](active-directory-add-domain.md) するプロセスは、最初のカスタム ドメイン名を設定する手順と同じです。

## <a name="add-subdomains-of-a-custom-domain"></a>カスタム ドメインのサブドメインの追加
ディレクトリに europe.contoso.com などの第 3 レベル ドメイン名を追加する場合は、最初に、contoso.com などの第 2 レベル ドメインを追加して、確認する必要があります。 サブドメインは、Azure AD によって自動的に確認されます。 追加したサブドメインが確認済みであることを確かめるには、ディレクトリ内のドメインが一覧表示されるページでブラウザーの表示を更新します。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>カスタム ドメイン名の DNS レジストラーを変更する場合にすべきこと
カスタム ドメイン名の DNS レジストラーを変更しても、中断したり追加の構成タスクを実施したりすることなく、Azure AD でそのカスタム ドメイン名を引き続き使用できます。 Office 365 や Intune など、Azure AD のカスタム ドメイン名を使用するサービスで、自社のカスタム ドメイン名を使用する場合は、各サービスのマニュアルを参照してください。

## <a name="delete-a-custom-domain-name"></a>カスタム ドメイン名を削除する
カスタム ドメイン名が使用されなくなった場合やそのドメイン名を別の Azure AD で使用する必要が生じた場合、Azure AD からドメイン名を削除することができます。

カスタム ドメイン名を削除する場合は、そのドメイン名を使用しているリソースがディレクトリ内にないことを事前に確認する必要があります。 次の状況に当てはまる場合、ディレクトリからドメイン名を削除することはできません。

* ユーザーに付与されたユーザー名、電子メール アドレス、またはプロキシ アドレスにドメイン名が含まれている。
* グループに付与された電子メール アドレスまたはプロキシ アドレスにドメイン名が含まれている。
* Azure AD 内のアプリケーションに付与されたアプリ ID URI にドメイン名が含まれている。

Azure AD ディレクトリ内にこのようなリソースがある場合は、カスタム ドメイン名を削除する前に、そのリソースを変更するか削除する必要があります。

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>PowerShell または Graph API を使用してドメイン名を管理する
Azure Active Directory のドメイン名に関する管理作業の多くは、Microsoft PowerShell を使用するか、プログラムから Azure AD Graph API (パブリック プレビュー中) を使用して行うこともできます。

* [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Graph API を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>次のステップ
* [Azure AD のドメイン名について確認する](active-directory-add-domain-concepts.md)
* [カスタム ドメイン名を管理する](active-directory-add-manage-domain-names.md)




<!--HONumber=Dec16_HO5-->


