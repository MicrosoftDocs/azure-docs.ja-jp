---
title: "Azure Active Directory でのカスタム ドメイン名の概念の概要 | Microsoft Docs"
description: "シングル サインオン用のフェデレーションを含む、Azure Active Directory でカスタム ドメイン名を使用するための概念的なフレームワークについて説明します"
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: fd0c5def-0da2-43af-81bc-76f4cfe86afd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 12d41a77655c4892a9dc20979ef98b1bd39f94c5


---
# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Azure Active Directory でのカスタム ドメイン名の概念の概要
ドメイン名は、多くのディレクトリ リソースの識別子の重要な部分です。ユーザーのユーザー名または電子メール アドレス、およびグループのアドレスの一部であり、アプリケーションのアプリ ID URI の一部になることもあります。 Azure Active Directory (Azure AD) のリソースには、リソースを格納するディレクトリによって所有されていることが既に確認済みのドメイン名を含めることができます。 Azure AD でドメイン管理タスクを実行できるのは、グローバル管理者のみです。

Azure AD でのドメイン名は、グローバルに一意です。 ドメイン名は、1 つの Azure AD で使用できます。 1 つの Azure AD ディレクトリでドメイン名が確認済みの場合、他の Azure AD ディレクトリではその同じドメイン名を確認または使用できません。

## <a name="initial-and-custom-domain-names"></a>初期ドメイン名およびカスタム ドメイン名
Azure AD のすべてのドメイン名は、初期ドメイン名またはカスタム ドメイン名です。

すべての Azure AD には、フォーム contoso.onmicrosoft.com の初期ドメイン名が付属します。 この例の "contoso.onmicrosoft.com" では、3 番目のレベルのドメイン名が、ディレクトリの作成時に確立されました。これは通常、ディレクトリを作成した管理者によって確立されます。 ディレクトリの初期ドメイン名は、変更または削除できません。 初期ドメイン名は完全に機能しますが、カスタム ドメイン名が確認されるまでは、主にブートストラップ メカニズムとしての使用を目的としています。

ほとんどの運用環境のディレクトリには、"contoso.com" など、少なくとも 1 つの確認済みカスタム ドメインがあり、これがエンドユーザーから参照可能なカスタム ドメインになります。 カスタム ドメイン名は、"contoso.com" など、自社の Web サイトのホストなどの目的で、組織が使用し、所有するドメイン名です。 このドメイン名は、企業ネットワークへのサインインや電子メールの送受信に使用するユーザー名の一部であるため、従業員にとってなじみがあります。

カスタム ドメイン名は、Azure AD で使用する前にディレクトリに追加し、確認する必要があります。

## <a name="verified-and-unverified-domain-names"></a>確認済みドメイン名および未確認ドメイン名
ディレクトリの初期ドメイン名は、Azure AD により確認済みと暗黙的に評価されます。 管理者が Azure AD にカスタム ドメイン名を追加する場合、その初期状態は未確認です。 Azure AD では、どのディレクトリ リソースでも未確認のドメイン名を使用することはできません。 これにより、特定のドメイン名を使用可能なディレクトリが 1 つだけになり、そのドメイン名を使用する組織が、実際にそのドメイン名を所有することが保証されます。

Azure AD では、ドメイン名のドメイン ネーム サービス (DNS) ゾーン ファイルの特定のエントリを検索することによって、ドメイン名の所有権が確認されます。 ドメイン名の所有権を確認するには、管理者が Azure AD から、Azure AD が検索する DNS エントリを取得し、そのエントリをドメイン名の DNS ゾーン ファイルに追加します。 DNS ゾーン ファイルは、そのドメインのドメイン名登録業者によって保持されます。 ドメインを確認する手順については、 [Azure AD ディレクトリへのカスタム ドメインの追加](active-directory-add-domain.md)に関する記事を参照してください。

ドメイン名のゾーン ファイルに DNS エントリを追加しても、電子メールや Web ホスティングなどの他のドメイン サービスには影響しません。

## <a name="federated-and-managed-domain-names"></a>フェデレーション ドメイン名および管理対象ドメイン名
Azure AD のカスタム ドメイン名を構成することによって、オンプレミスの Active Directory と Azure AD 間のフェデレーション サインイン エクスペリエンスをユーザーに提供できます。 フェデレーションのドメインを構成するには、Azure AD の特権リソースおよび Windows Server Active Directory への更新が必要です。 フェデレーション ドメインの構成は、Azure AD Connect から、または PowerShell を使用して実行する必要があります。 Azure クラシック ポータルからカスタム ドメインのフェデレーションを開始することはできません。 [Azure AD Connect を使用して、ユーザー サインイン用の AD FS を構成する方法については、こちらのビデオをご覧ください](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)。

フェデレーションされていないドメインは、管理対象ドメインとも呼ばれます。 Azure AD ディレクトリの初期ドメインは、暗黙的に管理対象ドメインと評価されます。

## <a name="primary-domain-names"></a>プライマリ ドメイン名
ディレクトリのプライマリ ドメイン名は、管理者が [Azure クラシック ポータル](https://manage.windowsazure.com/) または Office 365 管理ポータルなどの別のポータルで新しいユーザーを作成する場合にユーザー名の "ドメイン" 部分の既定値として事前に選択されているドメイン名です。 ディレクトリで設定できるプライマリ ドメイン名は 1 つだけです。 管理者は、プライマリ ドメイン名をフェデレーションされていない任意の確認済みカスタム ドメイン、または初期ドメインに変更できます。

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Azure AD および他の Microsoft Online Services でのドメイン名
ドメイン名は、Exchange Online、SharePoint Online、Intune など別の Microsoft Online Services で使用する前に、Azure AD で確認する必要があります。 これらの他のサービスでは通常、管理者がサービスに固有の DNS エントリを 1 つ以上追加する必要する必要があります。

Azure Web アプリでは、独自のメカニズムを使用して、ドメインの所有権を確認します。 ドメイン名は、Azure AD を使用しているサブスクリプションで Azure Web アプリによる使用について以前確認したことがあった場合でも、その Azure AD での使用について確認する必要があります。 Azure Web アプリでは、Web アプリをセキュリティで保護するディレクトリとは別のディレクトリで確認済みのドメイン名を使用できます。

## <a name="managing-domain-names"></a>ドメイン名を管理する
ドメイン管理タスクは、Azure クラシック ポータルおよび PowerShell から実行できます。 多くのタスクは、Azure AD Graph API を (パブリック プレビューで) 使用して実行できます。

* [カスタム ドメイン名を追加および確認する](active-directory-add-domain.md)
* [Azure クラシック ポータルでドメインを管理する](active-directory-add-manage-domain-names.md)
* [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Azure AD Graph API を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)




<!--HONumber=Nov16_HO3-->


