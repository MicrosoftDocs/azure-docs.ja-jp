---
title: カスタム ドメインを追加する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用してカスタム ドメインを追加する方法について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28847ece3c08e93d14d381b35fc93fb16dad95a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973900"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Azure Active Directory ポータルを使用してカスタム ドメイン名を追加する

新しい Azure AD テナントにはすべて、 *\<domainname>.onmicrosoft.com* という初期ドメイン名が付きます。 初期ドメイン名は変更したり削除したりできませんが、お客様の組織の名前を追加することはできます。 カスタム ドメイン名を追加すると、*alain\@contoso.com* など、ユーザーになじみのあるユーザー名を作成するのに役立ちます。

## <a name="before-you-begin"></a>開始する前に

カスタム ドメイン名を追加するには、あらかじめドメイン レジストラーでドメイン名を作成しておきます。 認定ドメイン レジストラーについては、[ICANN 認定レジストラー](https://www.icann.org/registrar-reports/accredited-list.html)に関するページを参照してください。

## <a name="create-your-directory-in-azure-ad"></a>Azure AD ディレクトリを作成する

ドメイン名を取得したら、最初の Azure AD ディレクトリを作成できます。 サブスクリプションの **所有者** ロールを持つアカウントを使用して、お使いのディレクトリの Azure portal にサインインします。

[組織の新しいテナントを作成する](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization)ことに関するページの手順に従って、新しいディレクトリを作成します。

>[!IMPORTANT]
>テナントを作成する人が、自動的にそのテナントの全体管理者となります。 全体管理者は、テナントに他の管理者を追加できます。

サブスクリプション ロールの詳細については、[Azure ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)に関するページを参照してください。

>[!TIP]
> オンプレミスの Windows Server AD を Azure AD とフェデレーションする予定の場合は、ディレクトリを同期するために Azure AD Connect ツールを実行する際に **[このドメインを構成して、ローカル Active Directory にシングル サインオンします]** を選択する必要があります。
>
> また、ウィザードの **[Azure AD ドメイン]** の手順で、オンプレミスのディレクトリとフェデレーションするために選択したのと同じドメイン名を登録する必要があります。 このセットアップの表示例については、「[フェデレーション用に選択された Azure AD ドメインの検証](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)」を参照してください。 Azure AD Connect ツールをお持ちでない場合は、[こちらからダウンロード](https://go.microsoft.com/fwlink/?LinkId=615771)できます。

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Azure AD にカスタム ドメイン名を追加する

ディレクトリを作成したら、カスタム ドメイン名を追加できます。

1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

1. 任意のページから *Azure Active Directory* を検索して選択します。 次に、 **[カスタム ドメイン名]**  >  **[カスタム ドメインの追加]** の順に選択します。

    ![[カスタム ドメイン名] ページ。[カスタム ドメインの追加] が表示されている](media/add-custom-domain/add-custom-domain.png)

1. **[カスタム ドメイン名]** に、お客様の組織の新しい名前を入力します。この例では *contoso.com* です。 **[Add domain]\(ドメインの追加\)** を選択します。

    ![[カスタム ドメイン名] ページとカスタム ドメインの追加ページ](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >これが正しく機能するためには、 *.com* や *.net* などのトップレベルの拡張子を含める必要があります。

    未確認ドメインが追加されます。 **contoso.com** ページが表示され、DNS 情報が表示されます。 この情報は保存してください。 後で TXT レコードを作成して DNS を構成する際に、この情報が必要になります。

    ![DNS エントリ情報を含む [Contoso] ページ](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>ドメイン レジストラーに DNS 情報を追加する

Azure AD にカスタム ドメイン名を追加したら、ドメイン レジストラーに戻り、コピーした TXT ファイルから Azure AD の DNS 情報を追加する必要があります。 ドメインのこの TXT レコードを作成することで、ドメイン名の所有権を検証します。

ドメイン レジストラーに戻り、コピーされた DNS 情報に基づいて、ドメインの新しい TXT レコードを作成します。 Time to Live (TTL) を 3600 秒 (60 分) に設定してから、レコードを保存します。

>[!IMPORTANT]
>必要な数のドメイン名を登録できます。 ただし、各ドメインは Azure AD から独自の TXT レコードを取得します。 ドメイン レジストラーで TXT ファイルの情報を入力するときには注意してください。 間違った情報や重複する情報を誤って入力した場合、TTL のタイムアウト (60 分) まで待機しないと再試行できません。

## <a name="verify-your-custom-domain-name"></a>カスタム ドメイン名を検証する

カスタム ドメイン名を登録したら、Azure AD でその名前が有効であることを確認します。 ドメイン レジストラーから Azure AD への伝達は、即座に行われることも数日かかることもあり、ドメイン レジストラーによって異なります。

カスタム ドメイン名を検証するには、次の手順に従います。

1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

1. 任意のページから *Azure Active Directory* を検索して選択し、 **[カスタム ドメイン名]** を選択します。

1. **[カスタム ドメイン名]** から、カスタム ドメイン名を選択します。 この例では、**contoso.com** を選択します。

    ![[Fabrikam - カスタム ドメイン名] ページで [Contoso] が強調表示されている](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. **contoso.com** ページで **[確認]** を選択し、カスタム ドメインが正しく登録されていて、Azure AD で有効であることを確認します。

    ![DNS エントリ情報と [確認] ボタンが表示された [Contoso] ページ](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

カスタム ドメイン名の検証後、検証の TXT または MX ファイルを削除できます。

## <a name="common-verification-issues"></a>検証のよくある問題

Azure AD でカスタム ドメイン名を検証できない場合は、次の推奨事項を試してください。

- **少なくとも 1 時間待機してから、もう一度試します**。 DNS レコードは、Azure AD がドメインを検証する前に反映されている必要があります。 このプロセスに 1 時間以上かかることがあります。

- **DNS レコードが正しいことを確認します。** ドメイン名レジストラー サイトに戻ります。 エントリがあること、およびそれが Azure AD によって提供される DNS エントリ情報と一致していることを確認します。

  ご自分ではレジストラー サイトでレコードを更新できない場合は、エントリを追加するアクセス許可を持つ人とエントリを共有して、それが正しいことを検証してください。

- **そのドメイン名が、別のディレクトリで既に使用中でないことを確認します。** ドメイン名は、1 つのディレクトリでのみ検証できます。 ドメイン名が現在別のディレクトリで検証中の場合、それを新しいディレクトリでも検証することはできません。 この重複の問題を解決するには、古いディレクトリからそのドメイン名を削除する必要があります。 ドメイン名の削除に関する詳細については、[カスタム ドメイン名の管理](../enterprise-users/domains-manage.md)に関するページを参照してください。

- **管理されていない Power BI テナントがないことを確認します。** ユーザーがセルフサービス サインアップから Power BI をアクティブ化し、組織に対して管理されていないテナントを作成した場合、PowerShell を使用して、内部管理者または外部管理者として管理を引き継ぐ必要があります。 詳細については、「[Azure Active Directory の非管理対象ディレクトリを管理者として引き継ぐ](../enterprise-users/domains-admin-takeover.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- ディレクトリに別の全体管理者を追加します。 詳細については、[ロールと管理者を割り当てる方法](active-directory-users-assign-role-azure-portal.md)に関するページを参照してください。

- ユーザーをドメインに追加します。 詳細については、[ユーザーを追加または削除する](add-users-azure-active-directory.md)方法に関する記事をご覧ください。

- Azure AD でドメイン名の情報を管理します。 詳細については、[カスタム ドメイン名の管理](../enterprise-users/domains-manage.md)に関するページを参照してください。

- Azure Active Directory と共に使用するつもりのオンプレミス バージョンの Windows Server がある場合は、「[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)」を参照してください。