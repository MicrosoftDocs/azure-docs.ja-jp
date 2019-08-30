---
title: Windows Server VM をマネージド ドメインに参加させる | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory Domain Services マネージド ドメインに Windows Server 仮想マシンを参加させる方法を学習します。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: iainfou
ms.openlocfilehash: c3c3252ec2fd850a763bbbf089d470df5173843f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612411"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>チュートリアル:Windows Server 仮想マシンのマネージド ドメインへの参加

Azure Active Directory Domain Services (Azure AD DS) では、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos 認証、NTLM 認証など) が提供されます。 Azure AD DS マネージド ドメインを使用すると、ドメイン参加の機能と管理を Azure の仮想マシン (VM) に提供することができます。 このチュートリアルでは、Windows Server VM を作成した後、それを Azure AD DS マネージド ドメインに参加させる方法を示します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Windows Server VM を作成する
> * Windows Server VM を Azure 仮想ネットワークに接続する
> * VM を Azure AD DS マネージド ドメインに参加させる

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースが必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションを持っていない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)します。
* サブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリに同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services マネージド ドメイン。
    * 必要であれば、[Azure Active Directory Domain Services インスタンスを作成して構成][create-azure-ad-ds-instance]してください。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。
    * アカウントが Azure AD DS マネージド ドメインにサインインできるように、Azure AD Connect のパスワード ハッシュの同期またはパスワード リセットのセルフサービスが実行されていることを確認します。

ドメインに参加させる VM が既にある場合は、「[VM を Azure AD DS マネージド ドメインに参加させる](#join-the-vm-to-the-azure-ad-ds-managed-domain)」に進んでください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

このチュートリアルでは、Azure portal を使用して、Azure AD DS マネージド ドメインに参加させる Windows Server VM を作成します。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-windows-server-virtual-machine"></a>Windows Server 仮想マシンを作成する

コンピューターを Azure AD DS マネージド ドメインに参加させる方法を確認するため、Windows Server VM を作成しましょう。 この VM は、Azure AD DS マネージド ドメインへの接続を提供する Azure 仮想ネットワークに接続されます。 Azure AD DS マネージド ドメインに参加するプロセスは、通常のオンプレミスの Active Directory Domain Services ドメインに参加する場合と同じです。

ドメインに参加させる VM が既にある場合は、「[VM を Azure AD DS マネージド ドメインに参加させる](#join-the-vm-to-the-azure-ad-ds-managed-domain)」に進んでください。

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
2. **[作業の開始]** で **[Windows Server 2016 Datacenter]** を選択します。

    ![Azure portal で Windows Server 2016 Datacenter VM を選択して作成する](./media/join-windows-vm/select-vm-image.png)

3. **[基本]** ウィンドウで、仮想マシンのコア設定を構成します。 *[可用性オプション]* 、 *[イメージ]* 、 *[サイズ]* は、既定のままにしておきます。

    | パラメーター            | 推奨値   |
    |----------------------|-------------------|
    | Resource group       | リソース グループを選択または作成します (*myResourceGroup* など) |
    | 仮想マシン名 | VM の名前を入力します (*myVM* など) |
    | リージョン               | VM を作成するリージョンを選択します ("*米国東部*" など) |
    | ユーザー名             | VM 上に作成するローカル管理者アカウントのユーザー名を入力します (*azureuser* など) |
    | パスワード             | VM 上に作成するローカル管理者の安全なパスワードを入力し、確認します。 ドメイン ユーザー アカウントの資格情報は指定しないでください。 |

4. 既定では、Azure で作成された VM にはインターネットからはアクセスできません。 この構成により、VM のセキュリティが向上し、攻撃される可能性がある領域が減少します。 このチュートリアルの次のステップでは、リモート デスクトップ プロトコル (RDP) を使って VM に接続した後、Windows Server を Azure AD DS マネージド ドメインに参加させる必要があります。

    RDP が有効になっていると、自動サインイン攻撃が発生する可能性があります。これにより、サインインの試行が複数回連続して失敗するため、*admin* や *administrator* などの一般的な名前を持つアカウントが無効になる場合があります。 RDP は、必要な場合にのみ有効にし、承認された IP 範囲のセットに限定する必要があります。 Azure Security Center の一部である [Azure Just-In-Time VM アクセス][jit-access]を使用すると、これらの有効期間が短い、制限付きの RDP セッションを有効にすることができます。 [Azure Bastion ホスト (現在プレビュー段階) を作成、使用][azure-bastion]して、SSL を介した Azure portal 経由のアクセスのみを許可することもできます。

    このチュートリアルでは、VM への RDP 接続を手動で有効にします。

    **[パブリック受信ポート]** で、 **[選択したポートを許可する]** オプションを選択します。 **[受信ポートを選択]** のドロップダウン メニューから、 *[RDP]* を選択します。

5. 完了したら、 **[次へ: ディスク]** を選択します。
6. **[OS ディスクの種類]** のドロップダウン メニューから *[Standard SSD]* を選択し、 **[次へ: ネットワーク]** を選択します。
7. Azure AD DS マネージド ドメインがデプロイされているサブネットと通信できる Azure 仮想ネットワーク サブネットに、VM を接続する必要があります。 Azure AD DS マネージド ドメインを専用のサブネットにデプロイすることをお勧めします。 Azure AD DS マネージド ドメインと同じサブネットに VM をデプロイしないでください。

    VM をデプロイして適切な仮想ネットワーク サブネットに接続するには、主に次の 2 つの方法があります。
    
    * Azure AD DS マネージド ドメインがデプロイされているのと同じ仮想ネットワークで、サブネットを作成するか、既存のサブネットを選択します。
    * [Azure 仮想ネットワーク ピアリング][vnet-peering]を使用して接続されている Azure 仮想ネットワーク内のサブネットを選択します。
    
    Azure AD DS インスタンスのサブネットに接続されていない仮想ネットワーク サブネットを選択した場合、その VM をマネージド ドメインに参加させることはできません。 このチュートリアルでは、Azure 仮想ネットワークに新しいサブネットを作成します。

    **[ネットワーク]** ウィンドウで、Azure AD DS マネージド ドメインがデプロイされている仮想ネットワークを選択します (例: *myVnet*)
8. この例では、Azure AD DS マネージド ドメインの接続先として既存の *DomainServices* サブネットが表示されます。 このサブネットには VM を接続しないでください。 VM 用のサブネットを作成するには、 **[サブネット構成の管理]** を選択します。

    ![Azure portal でサブネット構成の管理を選択する](./media/join-windows-vm/manage-subnet.png)

9. **[+ サブネット]** を選択し、サブネットの名前を入力します (例: *ManagedVMs*)。 **[アドレス範囲 (CIDR ブロック)]** を指定します (例: *10.1.1.0/24*)。 この IP アドレス範囲が、他の既存の Azure またはオンプレミスのアドレス範囲と重複していないことを確認します。 他のオプションは既定値のままにして、 **[OK]** を選択します。

    ![Azure portal でサブネットの構成を作成する](./media/join-windows-vm/create-subnet.png)

10. サブネットの作成には数秒かかります。 作成されたら、 *[X]* を選択してサブネット ウィンドウを閉じます。
11. **[ネットワーク]** ウィンドウに戻って VM を作成し、ドロップダウン メニューから作成したサブネットを選択します (例: *ManagedVMs*)。 ここでも、適切なサブネットを選択します。Azure AD DS マネージド ドメインと同じサブネットには VM をデプロイしないでください。
12. 他のオプションは既定値のままにして、 **[管理]** を選択します。
13. **[ブート診断]** を *[オフ]* に設定します。 他のオプションは既定値のままにして、 **[確認と作成]** を選択します。
14. VM の設定を確認して、 **[作成]** を選択します。

VM の作成には数分かかります。 Azure portal に、デプロイの状態が表示されます。 VM の準備ができたら、 **[リソースに移動]** を選択します。

![正常に作成されたら Azure portal で VM リソースにアクセスする](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Windows Server VM に接続する

次に、新しく作成した Windows Server VM に RDP を使用して接続し、Azure AD DS マネージド ドメインに参加させます。 既存のドメイン資格情報ではなく、前のステップで VM を作成するときに指定したローカル管理者の資格情報を使用します。

1. **[概要]** ウィンドウで、 **[接続]** を選択します。

    ![Azure portal で Windows 仮想マシンに接続する](./media/join-windows-vm/connect-to-vm.png)

1. *[RDP ファイルのダウンロード]* オプションを選択します。 この RDP ファイルを Web ブラウザーで保存します。
1. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。
1. 前のステップで VM を作成するために入力したローカル管理者の資格情報を入力します (例: *localhost\azureuser*)
1. サインイン プロセス中に証明書警告が表示された場合、 **[はい]** または **[続行]** を選択して接続します。

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>VM を Azure AD DS マネージド ドメインに参加させる

VM を作成し、RDP 接続を確立したので、Windows Server 仮想マシンを Azure AD DS マネージド ドメインに参加させましょう。 このプロセスは、通常のオンプレミスの Active Directory Domain Services ドメインに接続しているコンピューターと同じです。

1. VM にサインインすると、既定で**サーバー マネージャー**が表示されます。 表示されない場合は、 **[スタート]** メニューの **[サーバー マネージャー]** を選択してください。
1. **[サーバー マネージャー]** ウィンドウの左側のウィンドウで、 **[ローカル サーバー]** を選択します。 右側のウィンドウの **[プロパティ]** で、 **[ワークグループ]** を選択します。

    ![VM でサーバー マネージャーを開き、ワークグループのプロパティを編集する](./media/join-windows-vm/server-manager.png)

1. **[システム プロパティ]** ウィンドウで **[変更]** を選択し、Azure AD DS マネージド ドメインに参加します。

    ![ワークグループまたはドメイン プロパティの変更を選択する](./media/join-windows-vm/change-domain.png)

1. **[ドメイン]** ボックスで Azure AD DS マネージド ドメインの名前を指定し (例: *contoso.com*)、 **[OK]** を選択します。

    ![参加する Azure AD DS マネージド ドメインを指定する](./media/join-windows-vm/join-domain.png)

1. ドメインの資格情報を入力してドメインに参加します。 *Azure AD DC administrators* グループに属しているユーザーの資格情報を使用します。 このグループのメンバーだけが、マシンを Azure AD DS マネージド ドメインに参加させるための権限を持っています。 アカウントの資格情報は、次のいずれかの方法で指定できます。

    * **UPN 形式** (推奨) - Azure AD で構成したように、ユーザー アカウントのユーザー プリンシパル名 (UPN) サフィックスを入力します。 たとえば、ユーザー *contosoadmin* の UPN サフィックスは `contosoadmin@contoso.onmicrosoft.com` になります。 *SAMAccountName* 形式ではなく UPN 形式を使用するとドメインに確実にサインインできる一般的なユースケースが 2 つあります。
        * ユーザーの UPN プレフィックスが長い場合 (例: *deehasareallylongname*)、*SAMAccountName* が自動生成される場合があります。
        * Azure AD テナントで複数のユーザーが同じ UPN プレフィックスを使用していると (例: *dee*)、*SAMAccountName* 形式が自動生成される場合があります。
    * **SAMAccountName 形式** - *SAMAccountName* 形式でアカウント名を入力します。 たとえば、ユーザー *contosoadmin* の *SAMAccountName* は `CONTOSO\contosoadmin` になります。

1. Azure AD DS マネージド ドメインに参加するには数秒かかります。 完了すると、ドメインへの参加を歓迎する次のようなメッセージが表示されます。

    ![ドメインへようこそ](./media/join-windows-vm/join-domain-successful.png)

    **[OK]** を選択して続行します。

1. Azure AD DS マネージド ドメインに参加するプロセスを完了するには、VM を再起動します。

> [!TIP]
> PowerShell の [Add-Computer][add-computer] コマンドレットを使用して、VM をドメインに参加させることもできます。 次の例では、*CONTOSO* ドメインに参加した後、VM を再起動しています。 要求されたら、*Azure AD DC administrators* グループに属しているユーザーの資格情報を入力します。
>
> `Add-Computer -DomainName CONTOSO -Restart`

Windows Server VM が再起動すると、Azure AD DS マネージド ドメインで適用されているすべてのポリシーが、VM にプッシュされます。 また、適切なドメイン資格情報を使用して Windows Server VM にサインインすることもできます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のチュートリアルでは、この Windows Server VM を使用して、Azure AD DS マネージド ドメインを管理できる管理ツールをインストールします。 このチュートリアル シリーズを続けない場合は、次のクリーンアップ手順を確認して、[RDP を無効にする](#disable-rdp)か、[VM を削除](#delete-the-vm)します。 そうでない場合は、[次のチュートリアルを続けます](#next-steps)。

### <a name="disable-rdp"></a>RDP を無効にする

このチュートリアルで作成した Windows Server VM を、独自のアプリケーションやワークロードを実行するために引き続き使用する場合は、インターネット経由で RDP が開かれていることを思い出してください。 セキュリティを強化し、攻撃のリスクを軽減するには、インターネット経由で RDP を無効にする必要があります。 インターネット経由で Windows Server VM への RDP を無効にするには、次の手順のようにします。

1. 左側のメニューから、 **[リソース グループ]** を選択します
1. お使いのリソース グループを選択します (例: *myResourceGroup*)。
1. お使いの VM を選択し (例: *myVM*)、 *[ネットワーク]* を選択します。
1. ネットワーク セキュリティ グループの **[Inbound network security rules]\(受信ネットワーク セキュリティ規則\)** で、RDP を許可する規則を選択し、 **[削除]** を選択します。 受信セキュリティ規則が削除されるまで数秒かかります。

### <a name="delete-the-vm"></a>VM の削除

この Windows Server VM をもう使わない場合は、次の手順に従って VM を削除します。

1. 左側のメニューから、 **[リソース グループ]** を選択します
1. お使いのリソース グループを選択します (例: *myResourceGroup*)。
1. お使いの VM を選択し (例: *myVM*)、 **[削除]** を選択します。 **[はい]** を選択して、リソースの削除を確定します。 VM の削除には数分かかります。
1. VM が削除されたら、*myVM-* プレフィックスが付いている OS ディスク、ネットワーク インターフェイス カード、その他のすべてのリソースを選択して削除します。

## <a name="troubleshoot-domain-join-issues"></a>ドメイン参加の問題のトラブルシューティング

通常のオンプレミス コンピューターを Active Directory Domain Services ドメインに参加させる場合と同じ方法で、Windows Server VM も Azure AD DS マネージド ドメインに正常に参加させることができるはずです。 Windows Server VM を Azure AD DS マネージド ドメインに参加させることができない場合は、接続または資格情報に関連する問題があることを示しています。 マネージド ドメインに正常に参加させるには、次のトラブルシューティングのセクションを確認してください。

### <a name="connectivity-issues"></a>接続に関する問題

ドメインに参加するための資格情報を要求するプロンプトが表示されない場合は、接続に問題があります。 VM は、仮想ネットワーク上の Azure AD DS マネージド ドメインには接続できません。

以下の各トラブルシューティング手順を実行した後、Windows Server VM をマネージド ドメインに再度参加させてください。

* Azure AD DS が有効になっているのと同じ仮想ネットワークに VM が接続されていること、または VM にピアリングされたネットワーク接続があることを確認します。
* マネージド ドメインの DNS ドメイン名に対して ping を実行します (例: `ping contoso.com`)。
    * ping 要求が失敗する場合は、マネージド ドメインの IP アドレスに対して ping を実行します (例: `ping 10.0.0.4`)。 環境の IP アドレスは、Azure リソースの一覧から Azure AD DS マネージド ドメインを選択すると、 *[プロパティ]* ページに表示されます。
    * ドメインではなく IP アドレスを ping できた場合、DNS が正しく構成されていない可能性があります。 マネージド ドメインの IP アドレスが仮想ネットワークの DNS サーバーとして構成されていることを確認します。
* `ipconfig /flushdns` コマンドを使用して、仮想マシンの DNS リゾルバー キャッシュをフラッシュしてみます。

### <a name="credentials-related-issues"></a>資格情報に関連した問題

ドメインに参加するための資格情報を要求するプロンプトが表示されても、資格情報を入力した後でエラーが発生する場合は、VM は Azure AD DS マネージド ドメインに接続できます。 指定した資格情報では、VM を Azure AD DS マネージド ドメインに参加させることはできません。

以下の各トラブルシューティング手順を実行した後、Windows Server VM をマネージド ドメインに再度参加させてください。

* 指定したユーザー アカウントが *AAD DC Administrators* グループに属していることを確認します。
* UPN 形式を使用して資格情報を指定します (例: `contosoadmin@contoso.onmicrosoft.com`)。 たくさんのユーザーがテナントで同じ UPN プレフィックスを使用している場合、または UPN プレフィックスが最大文字数を超えている場合は、アカウントの *SAMAccountName* が自動生成される可能性があります。 そのような場合、アカウントの *SAMAccountName* 形式が、想定されている形式やオンプレミス ドメインで使用されている形式と異なる可能性があります。
* マネージド ドメインとの[パスワード同期を有効にしている][password-sync]ことを確認します。 この構成手順を行わないと、サインインの試行を正しく認証するために必要なパスワード ハッシュが、Azure AD DS マネージド ドメインに存在しません。
* パスワード同期が完了するまで待ちます。 ユーザー アカウントのパスワードを変更すると、ドメインへの参加にパスワードを使用できるようになるまでに 15 から 20 分かかることがあります。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Windows Server VM を作成する
> * Windows Server VM を Azure 仮想ネットワークに接続する
> * VM を Azure AD DS マネージド ドメインに参加させる

Azure AD DS マネージド ドメインを管理するには、Active Directory 管理センター (ADAC) を使用して管理 VM を構成します。

> [!div class="nextstepaction"]
> [管理 VM に管理ツールをインストールする](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
