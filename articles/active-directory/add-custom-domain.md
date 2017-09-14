---
title: "Azure AD へのカスタム ドメインの追加 | Microsoft Docs"
description: "Azure Active Directory にカスタム ドメインを追加する方法について説明します。"
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3acdc406b5e753d407c7c6fb32fe0ad5ed6dd68c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>クイック スタート: カスタム ドメイン名を Azure Active Directory に追加する

Azure AD ディレクトリにはすべて、*domainname*.onmicrosoft.com の形式の初期ドメイン名が付いています。初期ドメイン名は変更も削除もできませんが、企業ドメイン名を Azure AD に追加することもできます。 たとえば、組織には多くの場合、ビジネスを行うために使用する別のドメイン名と、企業ドメイン名を使用してサインインするユーザーが存在します。 Azure AD にカスタム ドメイン名を追加すると、ディレクトリ内で、"alice@contoso.com" のようなユーザーにとって馴染みのあるユーザー名を、 "alice@*<domain name>*.onmicrosoft.com" の代わりに割り当てることができます。 この場合のプロセスは単純です。

1. カスタム ドメイン名をディレクトリに追加する
2. ドメイン名レジストラーでドメイン名の DNS エントリを追加する
3. Azure AD でカスタム ドメイン名を検証する

## <a name="add-your-custom-domain"></a>カスタム ドメインの追加
1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
   
   ![ユーザー管理を開く](./media/active-directory-domains-add-azure-portal/user-management.png)
3. ***[<ディレクトリ名>]*** ブレードで、**[ドメイン名]** を選択します。
4. **[<*ディレクトリ名*> - ドメイン名]** ブレードで、**[追加]** をクリックします。
   
   ![[追加] をクリックする](./media/active-directory-domains-add-azure-portal/add-command.png)
5. **[ドメイン名]** ブレードで、ボックスにカスタム ドメインの名前 ("contoso.com" など) を入力し、**[ドメインの追加]** をクリックします。 必ず、.com、.net、その他の上位拡張子を含めます。
6. ***[<ドメイン名>]*** ブレード (タイトルにカスタム ドメイン名があるブレード) で、組織がカスタム ドメイン名を所有していることを確認するために使用する DNS エントリ情報を取得します。
   
   ![DNS エントリ情報を取得する](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> オンプレミスの Windows Server AD を Azure AD とフェデレーションする予定の場合は、ディレクトリを同期するために Azure AD Connect ツールを実行する際に **[このドメインを構成して、ローカル Active Directory にシングル サインオンします]** チェックボックスをオンにする必要があります。 また、ウィザードの **[Azure AD ドメイン]** の手順で、オンプレミスのディレクトリとフェデレーションするために選択したのと同じドメイン名を登録する必要があります。 ウィザードの手順の表示内容は [こちらの手順](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)で確認できます。 Azure AD Connect ツールをお持ちでない場合は、 [こちらからダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771)できます。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>ドメインのドメイン名レジストラーで DNS エントリを追加する
Azure AD でカスタム ドメイン名を使用できるようにするために、次の手順としてドメインの DNS ゾーン ファイルを更新します。 これにより、組織がそのカスタム ドメイン名を所有していることを Azure AD で確認できます。 Azure、Office 365、および Azure 内の外部 DNS レコードを管理するシングル ポイントとして、[Azure ポータルの Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) を使用できます。

1. ドメインのドメイン名レジストラーにサインインします。 DNS エントリを更新するアクセス権がない場合は、アクセス権を持つ人物またはチームに連絡し、手順 2. の実行と、完了後の連絡を依頼します。
2. Azure AD から提供された DNS エントリを追加し、ドメインの DNS ゾーン ファイルを更新します。 この DNS エントリにより、Azure AD でドメインの所有権を検証することが可能になります。 メール ルーティングや Web ホスティングなどの動作は変更されません。

DNS エントリの追加に関するヘルプについては、 [一般的な DNS レジストラーで DNS エントリを追加する手順](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Azure AD との間でドメイン名を検証する
DNS エントリを追加すると、Azure AD でドメイン名を検証することができるようになります。

DNS レコードが反映された後でないと、ドメイン名を検証できません。 反映には数秒しかかからない場合がほとんどですが、1 時間以上かかる場合もあります。 最初から検証が機能しない場合は、後でもう一度やり直してください。

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[参照]** を選択し、テキスト ボックスに「ユーザー管理」と入力して、**Enter** キーを押します。
   
   ![ユーザー管理を開く](./media/active-directory-domains-add-azure-portal/user-management.png)
3. **[ユーザー管理 - ドメイン名]** ブレードで、検証する必要がある未確認のドメイン名を選択します。
4. ***[<ドメイン名>]*** ブレード (タイトルに新しいドメイン名がある、開いているブレード) で、**[確認]** をクリックして検証を実行します。

> [!TIP]
> 最大 900 のカスタム ドメイン名を追加できますが、新しいアカウントの作成時に既定で使用される、[Azure AD ディレクトリのプライマリ ドメイン名として設定](active-directory-domains-manage-azure-portal.md#set-the-primary-domain-name-for-your-azure-ad-directory)できるドメイン名は 1 つのみです。

これで、カスタム ドメイン名を使用して、クラウドベースのユーザー アカウントを作成したり、以前に同期したオンプレミスのユーザー アカウント情報を更新したりすることができるようになりました。 また、以前に同期したユーザー アカウントのドメイン サフィックス情報を、[Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) または [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) を使用して変更することもできます。

## <a name="troubleshooting"></a>トラブルシューティング
カスタム ドメイン名を確認できない場合は、以下のトラブルシューティング手順を試してください。

1. **1 時間待つ**。 DNS レコードは、Azure AD がドメインを検証する前に反映されている必要があります。 これに 1 時間以上かかる場合があります。
2. **DNS レコードが正しく入力されていることを確認する**。 ドメインのドメイン名レジストラーの Web サイトで、この手順を実行します。 Azure AD は、DNS エントリが DNS ゾーン ファイルに存在しない場合、または Azure AD によって提供された DNS エントリと完全に一致しない場合は、ドメイン名を検証できません。 ドメインの DNS レコードをドメイン名レジストラーで更新するアクセス権がない場合は、組織内でそのアクセス権を持つ人物またはチームと DNS エントリを共有し、DNS エントリの追加を依頼します。
3. **Azure AD の他のディレクトリから、そのドメイン名を削除する**。 ドメイン名は、1 つのディレクトリ内だけでしか検証できません。 ドメイン名が以前に他のディレクトリで検証されていた場合は、新しいディレクトリで検証する前に、前のディレクトリ内から削除されている必要があります。 ドメイン名の削除については、[カスタム ドメイン名の管理](active-directory-domains-manage-azure-portal.md)に関する記事を参照してください。    

## <a name="add-more-custom-domain-names"></a>カスタム ドメイン名を追加する
"contoso.com" や "contosobank.com" などの複数のカスタム ドメイン名を組織で使用している場合は、それぞれについてこの記事の手順を繰り返すことで、最大 900 のドメイン名を追加できます。

### <a name="learn-more"></a>詳細情報
[Azure AD でのカスタム ドメイン名の概念の概要](active-directory-add-domain-concepts.md)

[カスタム ドメイン名を管理する](active-directory-domains-manage-azure-portal.md)


## <a name="next-steps"></a>次のステップ
このクイックスタートでは、カスタム ドメインを Azure AD に追加する方法について説明しました。 

次のリンクを使用して、Azure Portal から、新しいカスタム ドメインを Azure AD に追加することができます。

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 
