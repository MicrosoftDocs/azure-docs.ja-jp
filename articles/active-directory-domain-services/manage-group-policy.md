---
title: Azure AD Domain Services でのグループ ポリシーの作成と管理 | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインで組み込みのグループ ポリシー オブジェクト (GPO) を編集し、独自のカスタム ポリシーを作成する方法について説明します。
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f1f2499c49c4adf16b632bc75c246a28330ad27b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619387"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services のマネージド ドメインのグループ ポリシーを管理する

Azure Active Directory Domain Services (Azure AD DS) のユーザー オブジェクトとコンピューター オブジェクトの設定は、多くの場合、グループ ポリシー オブジェクト (GPO) を使用して管理されます。 Azure AD DS には、*AADDC Users* コンテナーと *AADDC Computers* コンテナー用の組み込みの GPO が用意されています。 これらの組み込みの GPO をカスタマイズして、環境のニーズに合わせてグループ ポリシーを構成できます。 *Azure AD DC 管理者* グループのメンバーは、Azure AD DS ドメイン内でグループ ポリシー管理特権を持っています。また、カスタムの GPO と組織単位 (OU) を作成することもできます。 グループ ポリシーとそのしくみの詳細については、「[グループ ポリシーの概要][group-policy-overview]」を参照してください。

ハイブリッド環境では、オンプレミスの AD DS 環境内で構成されているグループ ポリシーは、Azure AD DS に同期されません。 Azure AD DS のユーザーまたはコンピューターの構成設定を定義するには、既定のいずれかの Gpo を編集するか、カスタム GPO を作成します。

この記事では、グループ ポリシーの管理ツールをインストールし、組み込みの GPO を編集して、カスタム GPO を作成する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメインを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了します。
* Azure AD DS マネージド ドメインに参加している Windows Server 管理 VM。
    * 必要に応じて、[Windows Server VM を作成してマネージド ドメインに参加させる][create-join-windows-vm]チュートリアルを完了します。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

> [!NOTE]
> グループ ポリシー管理用テンプレートを使用するには、新しいテンプレートを管理ワークステーションにコピーします。 *.admx* ファイルを `%SYSTEMROOT%\PolicyDefinitions` にコピーし、ロケール固有の *.adml* ファイルを `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]` にコピーします。この `Language-CountryRegion` は *.adml* の言語とリージョンに一致します。
>
> たとえば、英国、米国バージョンの *.adml* ファイルを `\en-us` フォルダーにコピーします。
>
> また、マネージド ドメインの一部であるドメイン コントローラーに、グループ ポリシー管理用テンプレートを一元的に格納することもできます。 詳細については、「[Windows でグループ ポリシー管理用テンプレート用のセントラル ストアを作成および管理する方法](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)」を参照してください。

## <a name="install-group-policy-management-tools"></a>グループ ポリシーの管理ツールをインストールする

グループ ポリシー オブジェクト (GPO) を作成して構成するには、グループ ポリシーの管理ツールをインストールする必要があります。 これらのツールは、Windows Server の機能としてインストールできます。 Windows クライアントに管理ツールをインストールする方法の詳細については、[リモート サーバー管理ツール (RSAT)][install-rsat] のインストールに関するページを参照してください。

1. 管理 VM にサインインします。 Azure portal を使用した接続方法については、「[Windows Server VM に接続する][connect-windows-server-vm]」を参照してください。
1. VM にサインインすると、既定で **サーバー マネージャー** が表示されるはずです。 そうならない場合は、 **[スタート]** メニューの **[サーバー マネージャー]** を選択します。
1. **[サーバー マネージャー]** ウィンドウの *[ダッシュボード]* ウィンドウで **[役割と機能の追加]** を選択します。
1. *[役割と機能の追加]* ウィザードの **[開始する前に]** ページで **[次へ]** を選択します。
1. *[インストールの種類]* で、 **[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** を選択します。
1. **[サーバーの選択]** ページで、サーバー プールから現在の VM (例: *myvm.aaddscontoso.com*) を選択し、 **[次へ]** を選択します。
1. **[サーバーの役割]** ページで、 **[次へ]** をクリックします。
1. **[機能]** ページで、 **[グループ ポリシー管理]** 機能を選択します。

    ![[機能] ページから "グループ ポリシーの管理" をインストールする](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. **[確認]** ページで **[インストール]** を選択します。 グループ ポリシーの管理ツールのインストールには、1、2 分かかることがあります。
1. 機能のインストールが完了したら、 **[閉じる]** を選択して **[役割と機能の追加]** ウィザードを終了します。

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>グループ ポリシー管理コンソールを開いてオブジェクトを編集する

既定のグループ ポリシー オブジェクト (GPO) は、マネージド ドメイン内のユーザーおよびコンピューター用に存在します。 前のセクションでインストールしたグループ ポリシーの管理機能を使用して、既存の GPO を表示および編集します。 次のセクションでは、カスタム GPO を作成します。

> [!NOTE]
> マネージド ドメインでグループ ポリシーを管理するには、*AAD DC 管理者* グループのメンバーであるユーザー アカウントにサインインする必要があります。

1. スタート画面で **[管理ツール]** を選択します。 使用できる管理ツールの一覧が表示されます。これには、前のセクションでインストールした **グループ ポリシーの管理** が含まれます。
1. グループ ポリシー管理コンソール (GPMC) を開くには、 **[グループ ポリシーの管理]** を選択します。

    ![グループ ポリシー管理コンソールが開き、グループ ポリシー オブジェクトを編集できる状態になります](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

マネージド ドメインには、*AADDC Computers* コンテナー用と *AADDC Users* コンテナー用の 2 つの組み込みのグループ ポリシー オブジェクト (GPO) があります。 これらの GPO をカスタマイズして、マネージド ドメイン内で必要に応じてグループ ポリシーを構成することができます。

1. **グループ ポリシー管理** コンソールで、 **[フォレスト: aaddscontoso.com]** ノードを展開します。 次に、 **[ドメイン]** ノードを展開します。

    *AADDC Computers* と *AADDC Users* には、2 つの組み込みのコンテナーがあります。 これらの各コンテナーには、既定の GPO が適用されています。

    ![既定の "AADDC Computers" と "AADDC Users" コンテナーに適用される組み込みの GPO](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. これらの組み込みの GPO は、マネージド ドメイン上で特定のグループ ポリシーを構成するようにカスタマイズできます。 *AADDC Computers GPO* など、いずれかの GPO を右クリックし、 **[編集]** を選択します。

    ![組み込みの GPO のいずれかを "編集" するオプションを選択する](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. グループ ポリシー管理エディター ツールが開き、 *[アカウント ポリシー]* などの GPO をカスタマイズできるようになります。

    ![グループ ポリシー管理エディターのスクリーンショット。](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    完了したら、 **[ファイル] > [保存]** を選択してポリシーを保存します。 コンピューターの既定では、90 分ごとにグループ ポリシーが更新され、加えた変更が適用されます。

## <a name="create-a-custom-group-policy-object"></a>カスタム グループ ポリシー オブジェクトを作成する

同様のポリシー設定をグループ化するには、多くの場合、必要なすべての設定を 1 つの既定の GPO で適用するのではなく、追加の GPO を作成します。 Azure AD DS を使用すると、カスタム グループ ポリシー オブジェクトを作成またはインポートしてカスタム OU にリンクすることができます。 まずカスタム OU を作成する必要がある場合は、[マネージド ドメインでのカスタム OU の作成](create-ou.md)に関する記事を参照してください。

1. **グループ ポリシー管理コンソール** で、*MyCustomOU* などのカスタム組織単位 (OU) を選択します。 OU を右クリックし、 **[このドメインに GPO を作成し、このコンテナーにリンクする]** を選択します。

    ![グループ ポリシー管理コンソールでカスタム GPO を作成する](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. 新しい GPO の名前 (「*My custom GPO*」など) を指定し、 **[OK]** を選択します。 必要に応じて、既存の GPO と一連のポリシー オプションに基づいてこのカスタム GPO を作成することもできます。

    ![新しいカスタム GPO の名前を指定します](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. カスタム GPO が作成され、カスタム OU にリンクされます。 ポリシー設定を構成するには、カスタム GPO を右クリックし、 **[編集]** を選択します。

    ![カスタム GPO を "編集" するオプションを選択する](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. **グループ ポリシー管理エディター** が開き、GPO をカスタマイズできるようになります。

    ![必要に応じて設定を構成する GPO をカスタマイズする](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    完了したら、 **[ファイル] > [保存]** を選択してポリシーを保存します。 コンピューターの既定では、90 分ごとにグループ ポリシーが更新され、加えた変更が適用されます。

## <a name="next-steps"></a>次のステップ

グループ ポリシー管理コンソールを使用して構成できる使用可能なグループ ポリシー設定の詳細については、[グループ ポリシーの基本設定項目の使用][group-policy-console]に関する記事を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
