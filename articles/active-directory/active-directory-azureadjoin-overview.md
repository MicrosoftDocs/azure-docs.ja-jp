---
title: "Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張 | Microsoft Docs"
description: "Windows 10 デバイスで Azure AD Join を使用して Azure Active Directory に登録する方法の概要を詳しく説明します。"
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 0cd4942f-7d54-474e-bd12-8e6764b0d42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 31574a82d190b9b157f8df3308fac298924eada5


---
# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張
## <a name="what-is-azure-active-directory-join"></a>Azure Active Directory Join とは
Azure Active Directory Join (Azure AD Join) は、会社所有のデバイスを Azure Active Directory に登録することでデバイスの一元管理を可能にする機能です。 この機能を使用すると、従業員や学生などのユーザーは Azure Active Directory を介してエンタープライズ クラウドに接続できるようになります。 これにより、Windows デプロイの簡略化に加えて、会社所有でも個人所有 (BYOD) でも任意の Windows デバイスからの組織のアプリケーションとリソースへのアクセスが可能になります。

Azure AD Join は、クラウド優先またはクラウドのみの企業 (一般に、オンプレミスの Windows Server Active Directory インフラストラクチャを所有していない中小企業) 向けです。 とは言うものの、Azure AD Join は、従来のドメイン参加に対応しないデバイス (モバイル デバイスなど) や、主に Office 365 やその他の Azure AD SaaS アプリにアクセスする必要があるユーザー向けに、大規模な組織でも使用できます。

従来のドメイン参加は、ドメイン参加に対応するデバイスで今でも最適なオンプレミス エクスペリエンスを実現できますが、Azure AD Join は、ドメイン参加に対応しないデバイスに適しています。 また、Azure AD Join はクラウドでユーザーを管理する場合にも適しています。 これを実現するには、グループ ポリシーや System Center Configuration Manager (SCCM) などの従来のドメイン管理ツールを使用する代わりに、モバイル デバイス管理機能を使用します。

![Overview of corporate devices and personal devices with on-premises Active Directory and Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)

## <a name="why-should-enterprises-adopt-azure-ad-join"></a>Azure AD Join を導入する理由
* **業務の多くでクラウドを利用している企業**: オンプレミスのフットプリントを小さくしたモデルに移行済みか移行中で、より多くの業務をクラウドで行う必要がある場合は、Azure AD Join が役に立ちます。 Azure AD アカウントは、手動で作成したか、オンプレミスの Active Directory の同期によって作成されたことが考えられます。 どちらの場合も、Azure AD のアカウントがあるため、それを使って Windows 10 にサインインできます。 ユーザーは、OOBE (Out-Of-Box Experience) または [設定] メニューを使用して、自分のコンピューターを Azure AD に参加させることができます。 参加させた後、ユーザーは、ブラウザーまたは Office アプリケーションで、Office 365 などのクラウド リソースへのシングル サインオン (SSO) アクセスを利用できます。
* **教育機関**: よく耳にするシナリオの 1 つとして、教育機関には教員と学生という 2 種類のユーザーがあることが挙げられます。 教員メンバーは、組織に長期間所属するメンバーと見なされるため、オンプレミスのアカウントを作成することが適しています。 一方、学生は、組織に所属する期間が短いため、Azure AD で管理できます。 つまり、ディレクトリ スケールをオンプレミスに保存するのではなく、クラウドにプッシュできます。 また、学生は自身の Azure AD アカウントを使用して Windows にサインインし、ブラウザーまたは Office アプリケーションで Office 365 のリソースにアクセスできます。
* **小売企業**: 顧客から聞いた別のシナリオとして、季節従業員をより簡単に管理したいという要望があります。  先ほどと同様に、長期雇用のフルタイム従業員のアカウントは、通常、ドメインに参加しているコンピューターでオンプレミスのアカウントとして作成します。 一方、季節従業員は、組織に所属する期間が短いため、ユーザー ライセンスをより簡単に移動できる方法で管理することが適しています。 Office 365 ライセンスを持つユーザー アカウントをクラウドに作成すると、ユーザーは Azure AD アカウントを使用して Windows と Office アプリケーションにサインインできるようになります。 それと同時に、ユーザーが退職した後のライセンス管理で柔軟性が向上します。
* **その他の企業**: オンプレミスの Active Directory でユーザーを管理する場合でも、ユーザーを Azure AD に参加させることでメリットを得られます。 これは、Azure AD では、簡略化された参加エクスペリエンス、効率的なデバイス管理、モバイル デバイス管理の自動登録、Azure AD とオンプレミスのリソースへのシングル サインオン機能が提供されるためです。  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Azure AD Join が提供する機能
Azure AD 参加には、次の機能があります。

* **会社所有デバイスのセルフプロビジョニング**: Windows 10 では、ユーザーは、IT スタッフの手を借りることなく、新しい市販のデバイスを構成できます。
* **最新のフォーム ファクターのサポート**: Azure AD Join は、従来のドメイン参加機能を持たないデバイスに対応しています。  
* **既存の組織アカウントのサポート**: ユーザーは、(Windows 8 の場合と同様に) 会社支給のデバイスで最適なエクスペリエンスを得るために個人の Microsoft アカウントを作成して管理する必要がなくなりました。 代わりに、Azure AD では既存の職場アカウントを使用できます。 つまり、多くの組織では、基本的に、ユーザーは Office 365 へのアクセスに使用するのと同じ資格情報を使用して Windows のセットアップとサインインを実行できます。
* **モバイル デバイス管理の自動登録**: デバイスは、Azure AD に接続したときに自動的にモバイル デバイス管理に登録されます。 このプロセスは、Microsoft Intune とパートナーのモバイル デバイス管理ソリューションで動作します。 Intune でデバイス管理が行われている場合、IT 管理者は、Azure AD 参加済みデバイスを、ドメイン参加済みデバイスと一緒に SCCM 管理コンソールで監視および管理できます。
* **会社リソースへのシングル サインオン**: ユーザーは、Windows デスクトップからクラウドのアプリやリソース (Office 365 や、[Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md) を介して認証に Azure AD を使用する多数のビジネス アプリケーション) にシングル サインオンすることができます。 Azure AD に参加している会社所有のデバイスは、そのデバイスが企業ネットワーク上にある場合はオンプレミスのリソースへの SSO も行うことができます。また、これらのリソースが [Azure AD アプリケーション プロキシ](https://msdn.microsoft.com/library/azure/Dn768219.aspx)で公開されている場合は、どこからでもこれらのリソースへの SSO を行うことができます。
* **OS の状態のローミング**: アクセシビリティ設定、Web サイト、Wi-Fi パスワードなどの設定は、会社所有のデバイス間で同期されます。その際、個人の Microsoft アカウントは必要ありません。
* **企業向けの Windows ストア**: Windows ストアでは、Azure AD アカウントを使用したアプリの取得とライセンスをサポートしています。 組織は、アプリのボリューム ライセンスを取得して、組織内のユーザーがそのアプリを利用できるようにすることができます。

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>Azure AD Join を使用した場合の各種デバイスの対応
| 会社のデバイス (オンプレミスのドメインに参加) | 会社のデバイス (クラウドに参加) | 個人デバイス |
| --- | --- | --- |
| ユーザーは、仕事用の資格情報を使って Windows にサインインできます (従来の方法)。 |ユーザーは、Azure AD で管理されている仕事用の資格情報を使って Windows にサインインできる。 これは、次の 3 つのケースで会社のデバイスに該当します。1) オンプレミスの Active Directory を所有していない組織 (小規模企業など)。 2) すべてのユーザー アカウントを Active Directory に作成するとは限らない組織 (たとえば、学生、コンサルタント、季節従業員のアカウントを Active Directory に作成しない組織)。 3) モバイル SKU を実行している電話やタブレットなど、(オンプレミスの) ドメインに参加できない会社のデバイス (たとえば、工場や小売部門で使用されるセカンダリ デバイス) を保有している組織。 Azure AD Join は、管理された組織とフェデレーションされた組織の両方で会社のデバイスの参加をサポートしています。 |ユーザーは、個人の Microsoft アカウントの資格情報を使って Windows にサインインします (変更なし)。 |
| ユーザーは、ローミング設定と企業向けの Windows ストアにアクセスできます。 これらのサービスは職場アカウントに対応しているため、個人の Microsoft アカウントは必要ありません。 この場合、組織はオンプレミスの Active Directory を Azure AD に接続する必要があります。 |ユーザーは、セルフサービスによる設定を実行できます。 ユーザーは、IT スタッフにデバイスをプロビジョニングしてもらう代わりに、職場アカウントを使って最初の実行エクスペリエンス (FRX) を実現できますが、どちらの方法もサポートされています。 |ユーザーは、Active Directory または Azure AD で管理される職場アカウントを簡単に追加できます。 |
| ユーザーは、デスクトップから職場のアプリ、Web サイト、リソースへの SSO を実行できます。これには、オンプレミスのリソースも、認証に Azure AD を使用するクラウド アプリも含まれます。 |デバイスはエンタープライズ ディレクトリ (Azure AD) に自動的に登録され、モバイル デバイス管理に自動的に登録されます  (Azure AD Premium の機能)。 |ユーザーは、この職場アカウントを使って、アプリ全体と Web サイトやリソースへの SSO を実行できます。 |
| ユーザーは、個人の Microsoft アカウンを追加して、会社のデータに影響することなく個人の画像とファイルにアクセスできます  (ローミング設定は、職場アカウントで引き続き利用できます)。Microsoft アカウントを使用すると、SSO が可能になり、設定のローミングは行われなくなります。 |ユーザーは、winlogon でセルフサービスによるパスワードのリセット (SSPR) を実行できます。つまり、忘れたパスワードをリセットできます  つまり、忘れたパスワードをリセットできます (Azure AD Premium の機能)。 |ユーザーは、企業向けの Windows ストアにアクセスできるため、個人のデバイスで基幹業務アプリを取得して使用できます。 |

## <a name="additional-information"></a>追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)




<!--HONumber=Feb17_HO2-->


