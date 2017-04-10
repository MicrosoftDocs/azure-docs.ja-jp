---
title: "Azure Active Directory ドメイン サービス: RHEL VM の管理対象ドメインへの参加 | Microsoft Docs"
description: "Red Hat Enterprise Linux 仮想マシンのAzure AD ドメイン サービスへの参加"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 87291c47-1280-43f8-8fb2-da1bd61a4942
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: d36b4c1391dad88603ad823055e11c0a4a329c3c
ms.openlocfilehash: 89e97cb903e04efa77cfc6b02484d28a9ccfe6fb
ms.lasthandoff: 01/13/2017


---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Red Hat Enterprise Linux 7 仮想マシンの管理対象ドメインへの参加
この記事では、Red Hat Enterprise Linux (RHEL) 7 仮想マシンを Azure AD ドメイン サービスの管理対象ドメインに参加させる方法について説明します。

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Red Hat Enterprise Linux 仮想マシンのプロビジョニング
Azure Portal を使用して RHEL 7 仮想マシンをプロビジョニングするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

    ![Azure Portal ダッシュボード](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)
2. 次のスクリーンショットのように、左側のウィンドウの **[新規]** をクリックして、検索バーに「**Red Hat**」と入力します。 検索結果に Red Hat Enterprise Linux の項目が表示されます。 **[Red Hat Enterprise Linux 7.2]** をクリックします。

    ![検索結果から RHEL を選択](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)
3. **[すべて]** ウインドウの検索結果に、Red Hat Enterprise Linux 7.2 のイメージが表示されます。 **[Red Hat Enterprise Linux 7.2]** をクリックして、仮想マシン イメージの詳細情報を表示します。

    ![検索結果から RHEL を選択](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)
4. **[Red Hat Enterprise Linux 7.2]** ウィンドウに、仮想マシン イメージの詳細情報が表示されます。 **[デプロイ モデルの選択]** ドロップダウンで **[クラシック]** を選択します。 次に **[作成]** ボタンをクリックします。

    ![イメージの詳細を表示](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)
5. **仮想マシンの作成**ウィザードの **[基本]** ページで、新しい仮想マシンの**ホスト名**を入力します。 ローカル管理者のユーザー名を **[ユーザー名]** フィールドで指定し、**パスワード**を入力します。 ローカル管理者ユーザーの認証に SSH キーを使用することもできます。 仮想マシンの **価格レベル** を選択します。

    ![VM の作成 - [基本] ページ](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)
6. **仮想マシンの作成**ウィザードの **[サイズ]** ページで、仮想マシンのサイズを選択します。

    ![VM の作成 - サイズの選択](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-vm-size.png)

7. **仮想マシンの作成**ウィザードの **[設定]** ページで、仮想マシンのストレージ アカウントを選択します。 **[仮想ネットワーク]** をクリックして、Linux VM をデプロイする仮想ネットワークを選択します。 **[仮想ネットワーク]** ブレードで、Azure AD Domain Services を利用できる仮想ネットワークを選択します。 この例では、”MyPreviewVNet” 仮想ネットワークを選択しています。

    ![VM の作成 - 仮想ネットワークの選択](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)
8. **仮想マシンの作成**ウィザードの **[概要]** ページで、概要を確認して **[OK]** ボタンをクリックします。

    ![VM の作成 - 仮想ネットワークが選択されました](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)
9. RHEL 7.2 のイメージに基づき、新しい仮想マシンのデプロイが開始されます。

    ![VM の作成 - デプロイが開始されました](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)
10. 数分後に仮想マシンのデプロイが完了し、使用する準備が整います。

    ![VM の作成 - 展開済み](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)

## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>新しくプロビジョニングされた Linux 仮想マシンへのリモート接続
RHEL 7.2 仮想マシンの Azure でのプロビジョニングが完了しました。 続いて、仮想マシンへのリモート接続を行います。

**RHEL 7.2 仮想マシンへの接続** [Linux が実行されている仮想マシンにログオンする方法](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事の手順に従ってください。

次の手順では、PuTTY SSH クライアントを使用して、RHEL 仮想マシンに接続することを想定しています。 詳細については、「 [PuTTY Download Page (PuTTY のダウンロード ページ)](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)」を参照してください。

1. PuTTY プログラムを開きます。
2. 新たに作成された RHEL 仮想マシンの **ホスト名** を入力します。 この例では、仮想マシンのホスト名は ”contoso rhel.cloudapp .net” です。 仮想マシンのホスト名が不明な場合は、Azure Portal の VM ダッシュボードを参照してください。

    ![PuTTY 接続](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)
3. 仮想マシンの作成時に指定したローカル管理者の資格情報を使用して、仮想マシンにログオンします。 この例では、ローカル管理者アカウント "mahesh" を使用しました。

    ![PuTTY ログイン](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)

## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Linux 仮想マシンに必要なパッケージのインストール
仮想マシンへの接続が完了したら、ドメイン参加に必要なパッケージを仮想マシンにインストールします。 次の手順に従います。

1. **realmd のインストール:** ドメイン参加には realmd パッケージを使用します。 PuTTY ターミナルで、次のコマンドを入力します。

    sudo yum install realmd

    ![realmd のインストール](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    数分後に realmd パッケージが仮想マシンにインストールされます。

    ![realmd がインストールされました](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)
2. **sssd のインストール:** realmd パッケージは、ドメイン参加操作を実行するために sssd に依存します。 PuTTY ターミナルで、次のコマンドを入力します。

    sudo yum install sssd

    ![sssd のインストール](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    数分後に sssd パッケージが仮想マシンにインストールされます。

    ![realmd がインストールされました](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)
3. **Kerberos のインストール:** PuTTY ターミナルで、次のコマンドを入力します。

    sudo yum install krb5-workstation krb5-libs

    ![kerberos のインストール](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    数分後に realmd パッケージが仮想マシンにインストールされます。

    ![Kerberos がインストールされました](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)

## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Linux 仮想マシンの管理対象ドメインへの参加
Linux 仮想マシンに必要なパッケージがインストールされたら、続いて仮想マシンを管理対象ドメインに参加させます。

1. AAD ドメイン サービスの管理対象ドメインを探します。 PuTTY ターミナルで、次のコマンドを入力します。

    sudo realm discover CONTOSO100.COM

    ![「realm discover」](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    **「realm discover」** で管理対象ドメインが見つからない場合は、ドメインが仮想マシンから到達可能か確認します (ping をお試しください)。 また、仮想マシンが、管理対象ドメインが利用可能な同じ仮想ネットワークにデプロイされているかも確認します。
2. kerberos を初期化します。 PuTTY ターミナルで、次のコマンドを入力します。 ”AAD DC 管理者” グループに所属するユーザーを指定します。 指定されたユーザーのみが、管理対象ドメインにコンピューターを参加させることができます。

    kinit bob@CONTOSO100.COM

    ![kinit ](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    kinit のエラーを防ぐため、ドメイン名は必ず大文字で指定します。
3. コンピューターをドメインに参加させます。 PuTTY ターミナルで、次のコマンドを入力します。 前の手順で指定した ("kinit") のと同じユーザーを指定します。

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

    ![領域の結合](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

コンピューターの管理対象ドメインへの参加が完了すると、「Successfully enrolled machine in realm (コンピューターは領域に正常に登録されました)」という旨のメッセージが表示されます。

## <a name="verify-domain-join"></a>ドメイン参加の確認
コンピューターが管理対象ドメインに参加できたか確認してみましょう。 ドメインに新しく参加した RHEL VM に、ssh とドメイン ユーザー アカウントを使用して接続し、ユーザー アカウントが正しく解決済みかを確認します。

1. PuTTY ターミナルで次のコマンドを入力し、ドメインに新しく参加した RHEL 仮想マシンに、SSH を使用して接続します。 管理対象ドメインに属するドメイン アカウントを使用します (例: ここでは 'bob@CONTOSO100.COM')。

    ssh-l bob@CONTOSO100.COM contoso rhel.cloudapp.net
2. PuTTY ターミナルで次のコマンドを入力し、ユーザーのホーム ディレクトリが正しく初期化されているかを確認します。

    pwd
3. PuTTY ターミナルで次のコマンドを入力し、グループ メンバーシップが正しく解決済みかを確認します。

    id

コマンドのサンプル出力は次のとおりです。

![ドメイン参加の確認](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)

## <a name="troubleshooting-domain-join"></a>ドメイン参加のトラブルシューティング
「 [Troubleshooting domain join (ドメイン参加のトラブルシューティング)](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) 」を参照してください。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Azure AD ドメイン サービスで管理されているドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Linux が実行されている仮想マシンにログオンする方法](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Installing Kerberos (Kerberos のインストール)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7 - Windows 統合ガイド)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)

