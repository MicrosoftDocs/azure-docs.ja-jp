---
title: Azure AD Domain Services の DNS を管理する | Microsoft Docs
description: DNS サーバー ツールをインストールして、Azure Active Directory Domain Services マネージド ドメインの DNS を管理し、条件付きフォワーダーを作成する方法について説明します。
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: afa6920a36a5a7218571239b36815004d8f2d450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619353"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services マネージド ドメインで DNS を管理し、条件付きフォワーダーを作成する

Azure Active Directory Domain Services (Azure AD DS) の重要な構成要素の 1 つが DNS (ドメイン名解決) です。 Azure AD DS に含まれる DNS サーバーによって、マネージド ドメインの名前が解決されます。 この DNS サーバーには、サービスの実行を可能にする重要なコンポーネントに対する組み込みの DNS レコードと更新プログラムが含まれます。

独自のアプリケーションやサービスを実行するとき、ドメインに参加していないコンピューターの DNS レコードを作成したり、ロード バランサーの仮想 IP アドレスを構成したり、外部 DNS フォワーダーを設定したりすることが必要になる場合があります。 "*AAD DC 管理者*" グループに属するユーザーには、Azure AD DS マネージド ドメインの DNS 管理者権限が付与され、カスタムの DNS レコードを作成、編集できます。

ハイブリッド環境では、オンプレミスの AD DS 環境など、他の DNS 名前空間内で構成されている DNS ゾーンとレコードは、マネージド ドメインに同期されません。 他の DNS 名前空間で名前付きリソースを解決するには、環境内の既存の DNS サーバーを指す条件付きフォワーダーを作成して使用します。

この記事では、DNS サーバー ツールをインストールしてから、DNS コンソールを使用して Azure AD DS でレコードを管理し、条件付きフォワーダーを作成する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメインを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了します。
* Azure AD DS 仮想ネットワークから、他の DNS 名前空間がホストされている場所への接続。
    * この接続は、[Azure ExpressRoute][expressroute] または [Azure VPN Gateway][vpn-gateway] 接続を使用して提供できます。
* マネージド ドメインに参加している Windows Server 管理 VM。
    * 必要に応じて、[Windows Server VM を作成してマネージド ドメインに参加させる][create-join-windows-vm]チュートリアルを完了します。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="install-dns-server-tools"></a>DNS サーバー ツールのインストール

マネージド ドメインに DNS レコードを作成して変更するには、DNS サーバー ツールをインストールする必要があります。 これらのツールは、Windows Server の機能としてインストールできます。 Windows クライアントに管理ツールをインストールする方法の詳細については、[リモート サーバー管理ツール (RSAT)][install-rsat] のインストールに関するページを参照してください。

1. 管理 VM にサインインします。 Azure portal を使用した接続方法については、「[Windows Server VM に接続する][connect-windows-server-vm]」を参照してください。
1. VM にサインインしたときに **サーバー マネージャー** が既定で開かない場合は、 **[スタート]** メニューを選択し、 **[サーバー マネージャー]** を選択します。
1. **[サーバー マネージャー]** ウィンドウの *[ダッシュボード]* ウィンドウで **[役割と機能の追加]** を選択します。
1. *[役割と機能の追加]* ウィザードの **[開始する前に]** ページで **[次へ]** を選択します。
1. *[インストールの種類]* で、 **[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** を選択します。
1. **[サーバーの選択]** ページで、サーバー プールから現在の VM (例: *myvm.aaddscontoso.com*) を選択し、 **[次へ]** を選択します。
1. **[サーバーの役割]** ページで、 **[次へ]** をクリックします。
1. **[機能]** ページで、 **[リモート サーバー管理ツール]** ノードを展開し、次に **[役割管理ツール]** ノードを展開します。 役割管理ツールの一覧から、 **[DNS サーバー ツール]** 機能を選択します。

    ![利用できるロール管理ツールの一覧から DNS サーバー ツールのインストールを選択する](./media/manage-dns/install-dns-tools.png)

1. **[確認]** ページで **[インストール]** を選択します。 DNS サーバー ツールのインストールには 1 分から 2 分かかる場合があります。
1. 機能のインストールが完了したら、 **[閉じる]** を選択して **[役割と機能の追加]** ウィザードを終了します。

## <a name="open-the-dns-management-console-to-administer-dns"></a>DNS 管理コンソールを起動して DNS を管理する

DNS サーバー ツールがインストールされたら、マネージド ドメインの DNS レコードを管理できます。

> [!NOTE]
> マネージド ドメインで DNS を管理するには、*AAD DC 管理者* グループのメンバーであるユーザー アカウントにサインインする必要があります。

1. スタート画面で **[管理ツール]** を選択します。 前のセクションでインストールした **DNS** など、利用できる管理ツールの一覧が表示されます。 **[DNS]** を選択し、DNS 管理コンソールを起動します。
1. **[DNS サーバーに接続]** ダイアログで、 **[次のコンピューター]** を選択し、マネージド ドメインの DNS ドメイン名 (例: *aaddscontoso.com*) を入力します。

    ![DNS コンソールでマネージド ドメインに接続する](./media/manage-dns/connect-dns-server.png)

1. DNS コンソールが、指定したマネージド ドメインに接続されます。 **[前方参照ゾーン]** または **[逆引き参照ゾーン]** を展開し、必要な DNS エントリを作成するか、必要であれば、既存のレコードを編集します。

    ![DNS コンソール: ドメインの管理](./media/manage-dns/dns-manager.png)

> [!WARNING]
> DNS サーバー ツールを使用してレコードを管理するとき、Azure AD DS で使用される組み込み DNS レコードを削除したり、変更したりしないでください。 組み込みの DNS レコードには、ドメインの DNS レコード、ネーム サーバー レコード、および DC の検出に使用されるその他のレコードが含まれます。 これらのレコードを変更すると、仮想ネットワークのドメイン サービスが中断されます。

## <a name="create-conditional-forwarders"></a>条件付きフォワーダーを作成する

Azure AD DS DNS ゾーンには、マネージド ドメイン自体のゾーンとレコードのみを含めます。 他の DNS 名前空間で名前付きリソースを解決するために、マネージド ドメインに追加のゾーンを作成しないでください。 代わりに、マネージド ドメインで条件付きフォワーダーを使用して、これらのリソースのアドレスを解決するために DNS サーバーに移動先を指示します。

条件付きフォワーダーは、DNS サーバーの構成オプションであり、クエリの転送先となる DNS ドメイン (*contoso.com* など) を定義することができます。 ローカル DNS サーバーでそのドメイン内のレコードに対するクエリを解決しようとする代わりに、DNS クエリがそのドメインの構成済みの DNS に転送されます。 この構成により、これらのリソースを反映するためにマネージド ドメイン内に重複するレコードを含むローカルの DNS ゾーンを作成しないため、確実に正しい DNS レコードが返されるようになります。

マネージド ドメインに条件付きフォワーダーを作成するには、次の手順を行います。

1. DNS ゾーン (*aaddscontoso.com* など) を選択します。
1. **[条件付きフォワーダー]** を選択し、右クリックして **[新規条件付フォワーダー]** を選択します。
1. 次の例に示すように、他の **[DNS ドメイン]** (*contoso.com* など) を入力し、その名前空間の DNS サーバーの IP アドレスを入力します。

    ![DNS サーバーの条件付きフォワーダーを追加して構成する](./media/manage-dns/create-conditional-forwarder.png)

1. 次の例に示すように、 **[Store this conditional forwarder in Active Directory, and replicate it as follows]\(この条件付きフォワーダーを Active Directory に格納してから、次のようにレプリケートする\)** チェック ボックスをオンにしてから、 *[このドメインのすべての DNS サーバー]* のオプションを選択します。

    ![DNS コンソール - [このドメインのすべての DNS サーバー] を選択する](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > 条件付きフォワーダーが "*ドメイン*" ではなく "*フォレスト*" に格納されている場合、その条件付きフォワーダーは失敗します。

1. 条件付きフォワーダーを作成するには、 **[OK]** を選択します。

マネージド ドメインに接続されている VM から、他の名前空間にあるリソースの名前解決が正常に解決されるようになるはずです。 条件付きフォワーダーで構成された DNS ドメインのクエリは、関連する DNS サーバーに渡されます。

## <a name="next-steps"></a>次のステップ

DNS の管理の詳細については、 [Technet の DNS ツールの記事](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11))を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh