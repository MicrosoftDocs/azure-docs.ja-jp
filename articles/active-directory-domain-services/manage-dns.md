---
title: Azure AD Domain Services の DNS を管理する | Microsoft Docs
description: Azure AD Domain Services の DNS を管理する
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 6753c26a99bb38e92613a6bad753e7dd101ba68e
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473142"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメイン上の DNS の管理
Azure Active Directory Domain Services には、マネージド ドメインの DNS 解決を提供する DNS (ドメイン名解決) サーバーが含まれています。 場合によっては、マネージド ドメインの DNS を構成する必要があります。 ドメインに参加していないコンピューターの DNS レコードを作成したり、ロード バランサーの仮想 IP アドレスを構成したり、外部 DNS フォワーダーを設定したりすることが必要になる場合があります。 このため、AAD DC 管理者グループに属するユーザーは、マネージド ドメインの DNS 管理者権限が付与されます。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に
この記事のタスク一覧を完了するには、以下が必要です。

1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](create-instance.md)に記載されているすべてのタスクを実行してください。
4. Azure AD Domain Services のマネージド ドメインの管理に使用する、 **ドメインに参加している仮想マシン** 。 そのような仮想マシンがない場合は、「[Windows Server 仮想マシンのマネージド ドメインへの参加](active-directory-ds-admin-guide-join-windows-vm.md)」で概要が示されているすべてのタスクに従います。
5. マネージド ドメインの DNS を管理するには、ディレクトリの **"AAD DC Administrators" グループに属するユーザー アカウント** の資格情報が必要です。

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>タスク 1 - ドメインに参加している仮想マシンを作成して、管理対象ドメインの DNS をリモートで管理する
Azure AD Domain Services のマネージド ドメインは、Active Directory 管理センター (ADAC) や AD PowerShell などの使い慣れた Active Directory 管理ツールでリモート管理することができます。 同様に、マネージド ドメインの DNS は、DNS サーバーの管理ツールを使用してリモートで管理できます。

Azure AD ディレクトリの管理者には、マネージド ドメイン上のドメイン コントローラーにリモート デスクトップで接続する権限はありません。 "AAD DC Administrators" グループのメンバーは、マネージド ドメインに参加している Windows Server とクライアント コンピューターから、DNS サーバー ツールを使用して、リモートでマネージド ドメインの DNS を管理できます。 DNS サーバー ツールは、リモート サーバー管理ツール (RSAT) のオプション機能の一部です。

最初のタスクでは、管理対象ドメインに参加している Windows Server 仮想マシンを作成します。 手順については、「[Azure AD Domain Services のマネージド ドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)」を参照してください。

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>タスク 2 - 仮想マシンに DNS サーバー ツールをインストールする
ドメインに参加している仮想マシンに DNS 管理ツールをインストールするには、次の手順を完了します。 [リモート サーバー管理ツールのインストールおよび使用](https://technet.microsoft.com/library/hh831501.aspx)の詳細については、TechNet を参照してください。

1. Azure Portal に移動します。 左側のパネルの **[すべてのリソース]** をクリックします。 タスク 1 で作成した仮想マシンを見つけてクリックします。
2. [概要] タブの **[接続]** ボタンをクリックします。リモート デスクトップ プロトコル (.rdp) ファイルが作成されてダウンロードされます。

    ![Windows 仮想マシンに接続する](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、 **[接続]** をクリックします。 'AAD DC Administrators' グループに属しているユーザーの資格情報を使用します。 たとえば、「bob@domainservicespreview.onmicrosoft.com」のように入力します。 サインイン処理中に証明書の警告が表示される場合があります。 [はい] または [続行] をクリックして接続します。

4. スタート画面で、 **[サーバー マネージャー]** を開きます。 [サーバー マネージャー] ウィンドウの中央ウィンドウで **[役割と機能の追加]** をクリックします。

    ![仮想マシンでのサーバー マネージャーの起動](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. **[役割と機能の追加]** ウィザードの **[開始する前に]** ページで **[次へ]** をクリックします。

    ![ページを開始する前に](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. **[インストールの種類]** ページで、 **[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** をクリックします。

    ![インストールの種類のページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. **[サーバーの選択]** ページで、サーバー プールから現在の仮想マシンを選択して **[次へ]** をクリックします。

    ![サーバー選択ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. **[サーバーの役割]** ページで、 **[次へ]** をクリックします。
9. **[機能]** ページで、 **[リモート サーバー管理ツール]** ノードをクリックして展開し、次に **[役割管理ツール]** ノードをクリックして展開します。 役割管理ツールの一覧から、 **[DNS サーバー ツール]** 機能を選択します。

    ![機能ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. **[確認]** ページで、 **[インストール]** をクリックして仮想マシンに DNS サーバー ツールの機能をインストールします。 機能のインストールが正常に完了したら、 **[閉じる]** をクリックして **[役割と機能の追加]** ウィザードを終了します。

    ![確認ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>タスク 3 - DNS 管理コンソールを起動して DNS を管理する
ここで、Windows Server DNS ツールを使用して、管理対象ドメインの DNS を管理できます。

> [!NOTE]
> マネージド ドメインの DNS を管理するには、"AAD DC Administrators" グループのメンバーである必要があります。
>
>

1. スタート画面で **[管理ツール]** をクリックします。 仮想マシンにインストールされた **DNS** コンソールが表示されます。

    ![管理ツール： DNS コンソール](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. **[DNS]** をクリックして、DNS 管理コンソールを起動します。
3. **[DNS サーバーに接続]** ダイアログ ボックスで、 **[次のコンピューター]** をクリックし、管理対象ドメインの DNS ドメイン名 (例: "contoso100.com") を入力します。

    ![DNS コンソール: ドメインへの接続](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. DNS コンソールがマネージド ドメインに接続されます。

    ![DNS コンソール: ドメインの管理](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. この DNS コンソールを使用して、AAD ドメイン サービスを有効にしている仮想ネットワーク内で、コンピューターに DNS エントリを追加できます。

> [!WARNING]
> DNS 管理ツールを使用してマネージド ドメインの DNS を管理する際は、注意してください。 **ドメイン内のドメイン サービスで使用される組み込みの DNS レコードを削除または変更**しないでください。 組み込みの DNS レコードには、ドメインの DNS レコード、ネーム サーバー レコード、および DC の検出に使用されるその他のレコードが含まれます。 これらのレコードを変更すると、仮想ネットワークのドメイン サービスが中断されます。
>
>

DNS の管理の詳細については、 [Technet の DNS ツールの記事](https://technet.microsoft.com/library/cc753579.aspx)を参照してください。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](create-instance.md)
* [Azure AD Domain Services のマネージド ドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD Domain Services ドメインを管理する](manage-domain.md)
* [DNS 管理ツール](https://technet.microsoft.com/library/cc753579.aspx)
