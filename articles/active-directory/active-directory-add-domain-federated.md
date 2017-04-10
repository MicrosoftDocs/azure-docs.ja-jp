---
title: "Azure Active Directory へのカスタム ドメイン名の追加とフェデレーション サインオンの設定 | Microsoft Docs"
description: "企業のドメイン名を Azure Active Directory に追加し、Azure Active Directory とオンプレミスのフェデレーション ソリューションの間でのフェデレーション サインオンを設定する方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 27126c7e-e6d6-4ef3-a4fb-f5f0706e749d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/04/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 77d34a6ebce12465a15de158b9160a03db58c48e


---
# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Azure Active Directory へのカスタム ドメイン名の追加
"contoso.com" などのカスタム ドメイン名を構成することで、contoso.com 内のユーザーは企業ネットワークからフェデレーション シングル サインオンを実行できるようになります。 既に Active Directory フェデレーション サービス (AD FS) やその他のフェデレーション サーバーが企業ネットワーク上で稼働している場合は、Azure AD Connect ツールを使用して、カスタム ドメイン名を利用できるように Azure AD を構成できます。 Azure AD Connect では、新しい AD FS 環境をデプロイし、それを Azure AD へのフェデレーション シングル サインオン向けに構成することができます。

AD FS やその他のフェデレーション サーバーを所有しておらず、デプロイする予定もない場合は、「 [Azure Active Directory へのカスタム ドメイン名の追加](active-directory-add-domain.md)」の手順に従ってください。

## <a name="add-a-custom-domain-name-to-your-directory"></a>カスタム ドメイン名をディレクトリに追加する
1. Azure AD ディレクトリのグローバル管理者のユーザー アカウントで [Azure クラシック ポータル](https://manage.windowsazure.com/) にサインインします。
2. **[Active Directory]** でディレクトリを開き、**[ドメイン]** タブをクリックします。
3. コマンド バーで **[追加]**を選択し、"contoso.com" のようなカスタム ドメインの名前を入力します。 必ず、.com、.net、その他の上位拡張子を含めます。
4. **[このドメインを構成して、ローカル Active Directory にシングル サインオンします]** チェック ボックスをオンにします。
5. **[追加]**を選択します。

Azure AD Connect ツールを実行して、Azure AD がドメインの検証に使う DNS エントリを取得します。 DNS エントリは、ウィザードの **Azure AD ドメイン** の手順で表示されます。 ウィザードの手順の表示内容は [こちらの手順](connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)で確認できます。 Azure AD Connect ツールをお持ちでない場合は、 [こちらからダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771)できます。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>ドメインのドメイン名レジストラーで DNS エントリを追加する
Azure AD でカスタム ドメイン名を使用できるようにするために、次の手順としてドメインの DNS ゾーン ファイルを更新します。 これにより、組織がそのカスタム ドメイン名を所有していることを Azure AD で検証できます。

1. ドメイン名のドメイン名レジストラーの Web サイトにサインインします。 アクセス権がない場合は、組織内でアクセス権を持つ人物またはチームに連絡し、手順 2. の実行と、完了後の連絡を依頼します。
2. Azure AD から提供された DNS エントリを追加し、ドメインの DNS ゾーン ファイルを更新します。 この DNS エントリにより、Azure AD でドメインの所有権を検証することが可能になります。 メール ルーティングや Web ホスティングなどの動作は変更されません。

この手順に関するヘルプについては、 [一般的な DNS レジストラーで DNS エントリを追加する手順](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Azure AD との間でドメイン名を検証する
DNS エントリを追加すると、Azure AD でドメイン名を検証することができるようになります。

ドメインを検証するには、Azure AD Connect ウィザードの **[Azure AD ドメイン]** の手順で **[次へ]** をクリックします。 Azure AD はドメインの DNS ゾーン ファイル内の DNS エントリを探します。 DNS レコードが反映された後でないと、Azure AD はドメイン名を検証しません。 反映には数秒しかかからない場合がほとんどですが、1 時間以上かかる場合もあります。 最初から検証が機能しない場合は、後でもう一度やり直してください。

次に、Azure AD Connect ウィザードの残りの手順を進めます。 これにより、ユーザーが Windows Server AD から Azure AD に同期されます。 フェデレーション用に構成されたドメイン内の同期済みユーザーは、企業ネットワークから Azure AD へのフェデレーション シングル サインオンを実行できるようになります。

## <a name="troubleshooting"></a>トラブルシューティング
カスタム ドメイン名を検証できない場合は、次のことを試してください。 以下では、最も一般的な原因から順に列挙します。

1. **1 時間待つ**。 DNS レコードは、Azure AD がドメインを検証する前に反映されている必要があります。 これに 1 時間以上かかる場合があります。
2. **DNS レコードが正しく入力されていることを確認する**。 ドメインのドメイン名レジストラーの Web サイトで、この手順を実行します。 Azure AD は、DNS エントリが DNS ゾーン ファイルに存在しない場合、または Azure AD によって提供された DNS エントリと完全に一致しない場合は、ドメイン名を検証できません。 ドメインの DNS レコードをドメイン名レジストラーで更新するアクセス権がない場合は、組織内でそのアクセス権を持つ人物またはチームと DNS エントリを共有し、DNS エントリの追加を依頼します。
3. **Azure AD の他のディレクトリから、そのドメイン名を削除する**。 ドメイン名は、1 つのディレクトリ内だけでしか検証できません。 ドメイン名が以前に他のディレクトリで検証されていた場合は、新しいディレクトリで検証する前に、前のディレクトリ内から削除されている必要があります。 ドメイン名の削除については、[カスタム ドメイン名の管理](active-directory-add-manage-domain-names.md)に関する記事を参照してください。

## <a name="add-more-custom-domain-names"></a>カスタム ドメイン名を追加する
複数のカスタム ドメイン名 (たとえば "contoso.com" と "contosobank.com" など) を使用する場合、ドメイン名は最大 900 個まで使用することができます。 この記事の同じ手順に従って各ドメイン名を追加してください。

## <a name="next-steps"></a>次のステップ
* [カスタム ドメイン名を管理する](active-directory-add-manage-domain-names.md)
* [Azure AD のドメイン管理の概念を理解する](active-directory-add-domain-concepts.md)
* [ユーザーがサインインしたときに会社のブランドを表示する](active-directory-add-company-branding.md)
* [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)




<!--HONumber=Dec16_HO2-->


