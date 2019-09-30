---
title: Azure AD Domain Services の DNS を管理する | Microsoft Docs
description: Azure Active Directory Domain Services マネージド ドメインの DNS を管理するために DNS サーバー ツールをインストールする方法について説明します。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: iainfou
ms.openlocfilehash: 9279f97d5260eae698d5dbee10e077b71ab01992
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612339"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services マネージド ドメインで DNS を管理する

Azure Active Directory Domain Services (Azure AD DS) の重要な構成要素の 1 つが DNS (ドメイン名解決) です。 Azure AD DS に含まれる DNS サーバーによって、マネージド ドメインの名前が解決されます。 この DNS サーバーには、サービスの実行を可能にする重要なコンポーネントに対する組み込みの DNS レコードと更新プログラムが含まれます。

独自のアプリケーションやサービスを実行するとき、ドメインに参加していないコンピューターの DNS レコードを作成したり、ロード バランサーの仮想 IP アドレスを構成したり、外部 DNS フォワーダーを設定したりすることが必要になる場合があります。 "*AAD DC 管理者*" グループに属するユーザーには、Azure AD DS マネージド ドメインの DNS 管理者権限が付与され、カスタムの DNS レコードを作成、編集できます。

この記事では、DNS サーバー ツールをインストールしてから、DNS コンソールを使用してレコードを管理する方法について説明します。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリと同期されているか、クラウド専用ディレクトリ)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services インスタンスを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了してください。
* Azure AD DS マネージド ドメインに参加している Windows Server 管理 VM。
    * 必要に応じて、[Windows Server VM を作成してマネージド ドメインに参加させる][create-join-windows-vm]チュートリアルを完了してください。
* Azure AD テナントの "*Azure AD DC 管理者*" グループのメンバーであるユーザー アカウント。

## <a name="install-dns-server-tools"></a>DNS サーバー ツールのインストール

DNS を作成して変更するには、DNS サーバー ツールをインストールする必要があります。 これらのツールは、Windows Server の機能としてインストールできます。 Windows クライアントに管理ツールをインストールする方法の詳細については、[リモート サーバー管理ツール (RSAT)][install-rsat] のインストールに関するページを参照してください。

1. 管理 VM にサインインします。 Azure portal を使用した接続方法については、「[Windows Server VM に接続する][connect-windows-server-vm]」を参照してください。
1. VM にサインインすると、既定で**サーバー マネージャー**が表示されるはずです。 そうならない場合は、 **[スタート]** メニューの **[サーバー マネージャー]** を選択します。
1. **[サーバー マネージャー]** ウィンドウの *[ダッシュボード]* ウィンドウで **[役割と機能の追加]** を選択します。
1. *[役割と機能の追加ウィザード]* の **[開始する前に]** ページで **[次へ]** を選択します。
1. *[インストールの種類]* で、 **[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** を選択します。
1. **[サーバーの選択]** ページで、サーバー プールから現在の VM (例: *myvm.contoso.com*) を選択し、 **[次へ]** を選択します。
1. **[サーバーの役割]** ページで、 **[次へ]** をクリックします。
1. **[機能]** ページで、 **[リモート サーバー管理ツール]** ノードを展開し、次に **[役割管理ツール]** ノードを展開します。 役割管理ツールの一覧から、 **[DNS サーバー ツール]** 機能を選択します。

    ![利用できるロール管理ツールの一覧から DNS サーバー ツールのインストールを選択する](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. **[確認]** ページで **[インストール]** を選択します。 グループ ポリシー管理ツールをインストールするには、1、2 分かかることがあります。
1. 機能のインストールが完了したら、 **[閉じる]** を選択して **[役割と機能の追加ウィザード]** を終了します。

## <a name="open-the-dns-management-console-to-administer-dns"></a>DNS 管理コンソールを起動して DNS を管理する

DNS サーバー ツールがインストールされたら、Azure AD DS マネージド ドメインの DNS レコードを管理できます。

> [!NOTE]
> Azure AD DS マネージド ドメインで DNS を管理するには、*AAD DC 管理者*グループのメンバーであるユーザー アカウントにサインインする必要があります。

1. スタート画面で **[管理ツール]** を選択します。 前のセクションでインストールした **DNS** など、利用できる管理ツールの一覧が表示されます。 **[DNS]** を選択し、DNS 管理コンソールを起動します。
1. **[DNS サーバーに接続]** ダイアログで、 **[次のコンピューター]** を選択し、マネージド ドメインの DNS ドメイン名 (例: *contoso.com*) を入力します。

    ![DNS コンソールで Azure AD DS マネージド ドメインに接続する](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. DNS コンソールが、指定した Azure AD DS マネージド ドメインに接続されます。 **[前方参照ゾーン]** または **[逆引き参照ゾーン]** を展開し、必要な DNS エントリを作成するか、必要であれば、既存のレコードを編集します。

    ![DNS コンソール: ドメインの管理](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> DNS サーバー ツールを使用してレコードを管理するとき、Azure AD DS で使用される組み込み DNS レコードを削除したり、変更したりしないでください。 組み込みの DNS レコードには、ドメインの DNS レコード、ネーム サーバー レコード、および DC の検出に使用されるその他のレコードが含まれます。 これらのレコードを変更すると、仮想ネットワークのドメイン サービスが中断されます。

## <a name="next-steps"></a>次の手順

DNS の管理の詳細については、 [Technet の DNS ツールの記事](https://technet.microsoft.com/library/cc753579.aspx)を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
