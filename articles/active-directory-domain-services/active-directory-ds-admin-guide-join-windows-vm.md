---
title: "Azure Active Directory ドメイン サービス: Windows Server VM の管理対象ドメインへの参加 | Microsoft Docs"
description: "Windows Server 仮想マシンの Azure AD ドメイン サービスへの参加"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 74dbdb33-05db-4d47-badc-0d7bb6d0c8cb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f8d21f6964d26a2e17e31d1f2947e7eb07c177d
ms.lasthandoff: 03/25/2017


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Windows Server 仮想マシンの管理対象ドメインへの参加
> [!div class="op_single_selector"]
> * [Azure クラシック ポータル - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

この記事では、Azure クラシック ポータルを使用して、Windows Server 2012 R2 を実行する仮想マシンを、Azure AD ドメイン サービスによって管理されているドメインに参加させる方法を示します。

## <a name="step-1-create-the-windows-server-virtual-machine"></a>ステップ 1: Windows Server 仮想マシンを作成する
「[Windows を実行する仮想マシンを Azure クラシック ポータルで作成する](../virtual-machines/windows/classic/tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」チュートリアルで説明されている手順に従います。 新しく作成した仮想マシンが、Azure AD Domain Services を有効にしたのと同じ仮想ネットワークに確実に参加していることが重要です。 [簡易作成] オプションを使用すると、仮想マシンを仮想ネットワークに参加させることができません。 そのため、[ギャラリーから] オプションを使用して仮想マシンを作成する必要があります。

Azure AD Domain Services を有効にした仮想ネットワークに参加する Windows 仮想マシンを作成するには、次の手順を実行します。

1. Azure クラシック ポータルのウィンドウの下部にあるコマンド バーで、 **[新規]**をクリックします。
2. **[Compute]** で、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。
3. 最初の画面の **[イメージの選択]** で、利用できるイメージの一覧から仮想マシンのイメージを選択できます。 適切なイメージを選択します。

    ![イメージの選択](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. 2 番目の画面では、コンピューターの名前、サイズ、管理ユーザーの名前とパスワードを選択します。 アプリやワークロードの実行に必要な階層とサイズを選択してください。 ここでは選択するユーザー名は、マシンのローカル管理者のユーザーです。 ここではドメイン ユーザー アカウントの資格情報を入力しないでください。

    ![仮想マシンの構成](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)
5. 3 番目の画面では、リソースのネットワーク、ストレージ、可用性を構成できます。 **[リージョン/アフィニティ グループ/仮想ネットワーク]** ボックスで、Azure AD ドメイン サービスを有効にした仮想ネットワークを選択していることを確認します。 仮想マシンに対して適切な **[クラウド サービス DNS 名]** を指定します。

    ![仮想マシンの仮想ネットワークの選択](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

   > [!WARNING]
   > 作成した仮想マシンを、Azure AD ドメイン サービスを有効にしたのと同じ仮想ネットワークに参加させていることを確認します。 その結果、仮想マシンがドメインを "参照" し、ドメイン参加などのタスクを実行できるようになります。 別の仮想ネットワークの仮想マシンを作成する場合は、その仮想ネットワークを、Azure AD ドメイン サービスを有効にした仮想ネットワークに接続します。
   >
   >
6. 4 番目の画面では、VM エージェントをインストールし、使用可能な一部の拡張機能を構成できます。

    ![完了](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
7. 仮想マシンが作成されると、クラシック ポータルの **[Virtual Machines]** ノードに新しい仮想マシンが一覧表示されます。 仮想マシンとクラウド サービスが自動的に起動し、その状態がいずれも " **実行中**" として表示されます。

    ![稼働している仮想マシン](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)

## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>ステップ 2: ローカル管理者アカウントを使用して Windows Server 仮想マシンに接続する
次に、新たに作成された Windows Server 仮想マシンをドメインに参加させるため、その仮想マシンに接続します。 仮想マシンに接続するには、仮想マシンの作成時に指定したローカル管理者の資格情報を使用します。

仮想マシンに接続するには、次の手順を実行します。

1. クラシック ポータルの **[Virtual Machines]** ノードに移動します。 手順 1. で作成した仮想マシンを選択し、ウィンドウ下部にあるコマンド バーで **[接続]** をクリックします。

    ![Windows 仮想マシンに接続する](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. クラシック ポータルでは、仮想マシンへの接続に使用される ".rdp" という拡張子のファイルを開くか保存するように求められます。 ダウンロードが完了したら、ファイルをクリックして開きます。
3. ログイン プロンプトで、仮想マシンの作成時に指定した **ローカル管理者の資格情報**を入力します。 たとえば、この例では "localhost\mahesh" を使用しています。

この時点で、ローカル管理者の資格情報を使用して、新しく作成した Windows 仮想マシンにログインしている必要があります。 次のステップでは、仮想マシンをドメインに参加させます。

## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>ステップ 3: Windows Server 仮想マシンを AAD-DS 管理対象ドメインに参加させる
Windows Server 仮想マシンを AAD DS の管理対象ドメインに参加させるには、次の手順を実行します。

1. 手順 2. で示したように、Windows Server に接続します。 スタート画面で、 **[サーバー マネージャー]**を開きます。
2. [サーバー マネージャー] ウィンドウの左側のウィンドウで **[ローカル サーバー]** をクリックします。

    ![仮想マシンでのサーバー マネージャーの起動](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. **[プロパティ]** セクションで **[ワークグループ]** をクリックします。 **[システムのプロパティ]** のプロパティ ページで、**[変更]** をクリックしてドメインに参加します。

    ![システムのプロパティ ページ](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. **[ドメイン]** テキスト ボックスで、Azure AD ドメイン サービス管理対象ドメインのドメイン名を指定し、**[OK]** をクリックします。

    ![参加するドメインの指定](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. ドメインに参加するために、資格情報を入力するように求められます。 **AAD DC 管理者グループに属しているユーザーの資格情報を指定** します。 このグループのメンバーだけが、マシンを管理対象のドメインに参加させる権限を有します。

    ![ドメインへの参加の資格情報を指定する](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. 次のいずれかの方法で、資格情報を指定できます。

   * UPN 形式: Azure AD で構成されたユーザー アカウントの UPN サフィックスを指定します。 この例では、ユーザー 'bob' の UPN サフィックスは 'bob@domainservicespreview.onmicrosoft.com' です。
   * SAMAccountName 形式: SAMAccountName 形式でアカウント名を指定することができます。 この例では、ユーザー 'bob' は、「CONTOSO100\bob」を入力する必要があります。

     > [!NOTE]
     > **UPN 形式を使用して資格情報を指定することをお勧めします。** ユーザーの UPN プレフィックスが最大文字数を超えていると (例: "joereallylongnameuser")、SAMAccountName が自動生成される場合があります。 複数のユーザーが Azure AD テナントで同じ UPN プレフィックス (例: "bob") を使用していると、サービスにより SAMAccountName 形式が自動生成される場合があります。 このような場合は、UPN 形式を使用するとドメインに確実にログインすることができます。
     >
     >
7. ドメイン参加が成功すると、ドメインへの歓迎を通知する次のメッセージが表示されます。 ドメイン参加操作を完了するには、仮想マシンを再起動します。

    ![ドメインへようこそ](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

## <a name="troubleshooting-domain-join"></a>ドメイン参加のトラブルシューティング
### <a name="connectivity-issues"></a>接続に関する問題
仮想マシンでドメインを検索できない場合は、次のトラブルシューティング手順を参照してください。

* ドメイン サービスを有効にしているのと同じ仮想ネットワークに仮想マシンが接続されていることを確認します。 そうでない場合、仮想マシンがドメインに接続できず、そのため、ドメインに参加することできません。
* 仮想マシンが別の仮想ネットワークに接続されている場合は、この仮想ネットワークが、ドメイン サービスを有効にした仮想ネットワークに接続されていることを確認します。
* 管理対象ドメインのドメイン名を使用して、ドメインの ping を試行してください (例: "ping contoso100.com")。 これを実行できない場合は、Azure AD Domain Services を有効にしたページに表示されているドメインの IP アドレスに対して ping を試行してください (例: "ping 10.0.0.4")。 ドメインではなく IP アドレスを ping できた場合は、DNS が正しく構成されていない場合があります。 ドメインの IP アドレスを仮想ネットワークの DNS サーバーとして構成していない可能性があります。
* 仮想マシンのDNS リゾルバー キャッシュのフラッシュを試行します ('ipconfig/flushdns')。

ドメインに参加するために資格情報の入力を求めるダイアログ ボックスが表示される場合、接続の問題はありません。

### <a name="credentials-related-issues"></a>資格情報に関連した問題
資格情報で問題があり、ドメインに参加することができない場合は、次の手順を参照してください。

* UPN 形式を使用して資格情報を指定します。 複数のユーザーがテナントで同じ UPN プレフィックスを使用していたり、使用する UPN プレフィックスが最大文字数を超えている場合は、アカウントの SAMAccountName が自動生成されることがあります。 そのため、アカウントの SAMAccountName 形式が想定した形式やオンプレミス ドメインで使用する形式と異なる可能性があります。
* "AAD DC 管理者" グループに属するユーザー アカウントの資格情報を使用して、コンピューターを管理対象ドメインに参加させるようにします。
* ファースト ステップ ガイドで説明されている手順に従って [パスワード同期が有効になっている](active-directory-ds-getting-started-password-sync.md) ことを確認します。
* サインインするためには、Azure AD で構成されているユーザーの UPN (例: 'bob@domainservicespreview.onmicrosoft.com') を使用します。
* ファースト ステップ ガイドで指定されているとおり、パスワード同期が完了するまで十分な時間待機します。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)

