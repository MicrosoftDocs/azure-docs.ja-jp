---
title: Azure AD Domain Services のマネージド ドメインに Windows Server VM を参加させる | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory Domain Services マネージド ドメインに Windows Server 仮想マシンを参加させる方法を学習します。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 869c827485d9b7a6baf68d2619af98d4c2ee82b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619574"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>チュートリアル:Azure Active Directory Domain Services のマネージド ドメインに Windows Server 仮想マシンを参加させる

Azure Active Directory Domain Services (Azure AD DS) では、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos 認証、NTLM 認証など) が提供されます。 Azure AD DS マネージド ドメインを使用すると、ドメイン参加の機能と管理を Azure の仮想マシン (VM) に提供することができます。 このチュートリアルでは、Windows Server VM を作成した後、それをマネージド ドメインに参加させる方法を示します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Windows Server VM を作成する
> * Windows Server VM を Azure 仮想ネットワークに接続する
> * VM をマネージド ドメインに参加させる

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースが必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。
* マネージド ドメインの一部であるユーザー アカウント。
    * アカウントがマネージド ドメインにサインインできるように、Azure AD Connect のパスワード ハッシュの同期またはセルフサービス パスワード リセットが実行されていることを確認します。
* Azure AD DS 仮想ネットワークにデプロイされた Azure Bastion ホスト。
    * 必要に応じて [Azure Bastion ホストを作成][azure-bastion]してください。

ドメインに参加させる VM が既にある場合は、[VM をマネージド ドメインに参加させる](#join-the-vm-to-the-managed-domain)方法についてのセクションに進んでください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルでは、Azure portal を使用して、マネージド ドメインに参加させる Windows Server VM を作成します。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-windows-server-virtual-machine"></a>Windows Server 仮想マシンを作成する

コンピューターをマネージド ドメインに参加させる方法を確認するため、Windows Server VM を作成しましょう。 この VM は、マネージド ドメインへの接続を提供する Azure 仮想ネットワークに接続されます。 マネージド ドメインに参加するプロセスは、通常のオンプレミスの Active Directory Domain Services ドメインに参加する場合と同じです。

ドメインに参加させる VM が既にある場合は、[VM をマネージド ドメインに参加させる](#join-the-vm-to-the-managed-domain)方法についてのセクションに進んでください。

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。

1. **[作業の開始]** で **[Windows Server 2016 Datacenter]** を選択します。

    ![Azure portal で Windows Server 2016 Datacenter VM を選択して作成する](./media/join-windows-vm/select-vm-image.png)

1. **[基本]** ウィンドウで、仮想マシンのコア設定を構成します。 *[可用性オプション]* 、 *[イメージ]* 、 *[サイズ]* は、既定のままにしておきます。

    | パラメーター            | 推奨値   |
    |----------------------|-------------------|
    | Resource group       | リソース グループを選択または作成します (*myResourceGroup* など) |
    | 仮想マシン名 | VM の名前を入力します (*myVM* など) |
    | リージョン               | VM を作成するリージョンを選択します ("*米国東部*" など) |
    | ユーザー名             | VM 上に作成するローカル管理者アカウントのユーザー名を入力します (*azureuser* など) |
    | Password             | VM 上に作成するローカル管理者の安全なパスワードを入力し、確認します。 ドメイン ユーザー アカウントの資格情報は指定しないでください。 |

1. 既定では、Azure に作成された VM にインターネットから RDP を使用してアクセスすることができます。 RDP が有効になっていると、自動サインイン攻撃が発生する可能性があります。これにより、サインインの試行が複数回連続して失敗するため、*admin* や *administrator* などの一般的な名前を持つアカウントが無効になる場合があります。

    RDP は、必要な場合にのみ有効にし、承認された IP 範囲のセットに限定する必要があります。 この構成により、VM のセキュリティが向上し、攻撃される可能性がある領域が減少します。 または、Azure Bastion ホストを作成、使用して、TLS を介した Azure portal 経由のアクセスのみを許可することもできます。 このチュートリアルの次の手順では、Azure Bastion ホストを使用して安全に VM に接続します。

    **[パブリック受信ポート]** で *[なし]* を選択します。

1. 完了したら、 **[次へ: ディスク]** を選択します。
1. **[OS ディスクの種類]** のドロップダウン メニューから *[Standard SSD]* を選択し、 **[次へ: ネットワーク]** を選択します。
1. マネージド ドメインがデプロイされているサブネットと通信できる Azure 仮想ネットワーク サブネットに、VM を接続する必要があります。 マネージド ドメインを専用のサブネットにデプロイすることをお勧めします。 マネージド ドメインと同じサブネットに VM をデプロイしないでください。

    VM をデプロイして適切な仮想ネットワーク サブネットに接続するには、主に次の 2 つの方法があります。
    
    * マネージド ドメインがデプロイされているのと同じ仮想ネットワークで、サブネットを作成するか、既存のサブネットを選択します。
    * [Azure 仮想ネットワーク ピアリング][vnet-peering]を使用して接続されている Azure 仮想ネットワーク内のサブネットを選択します。
    
    マネージド ドメインのサブネットに接続されていない仮想ネットワーク サブネットを選択した場合、その VM をマネージド ドメインに参加させることはできません。 このチュートリアルでは、Azure 仮想ネットワークに新しいサブネットを作成します。

    **[ネットワーク]** ペインで、マネージド ドメインがデプロイされている仮想ネットワークを選択します (例: *aaads-vnet*)。
1. この例では、マネージド ドメインの接続先として既存の *aaads-subnet* が表示されます。 このサブネットには VM を接続しないでください。 VM 用のサブネットを作成するには、 **[サブネット構成の管理]** を選択します。

    ![Azure portal でサブネット構成の管理を選択する](./media/join-windows-vm/manage-subnet.png)

1. 仮想ネットワーク ウィンドウの左側のメニューで、 **[アドレス空間]** を選択します。 この仮想ネットワークは、既定のサブネットで使用される単一のアドレス空間 *10.0.2.0/24* で作成されています。 その他のサブネット ("*ワークロード*" や Azure Bastion 用など) も既に存在している場合があります。

    この仮想ネットワークにさらに IP アドレス範囲を追加します。 このアドレス範囲のサイズと実際に使用する IP アドレス範囲は、既にデプロイされている他のネットワーク リソースによって異なります。 この IP アドレス範囲は、Azure またはオンプレミスの環境における既存のアドレス範囲と重複することはできません。 サブネットにデプロイする予定の VM の数に対して十分な大きさの IP アドレス範囲を設定するようにしてください。

    次の例では、追加の IP アドレス範囲 *10.0.5.0/24* が追加されています。 準備ができたら、 **[保存]** を選択します。

    ![Azure portal で追加の仮想ネットワーク IP アドレス範囲を追加する](./media/join-windows-vm/add-vnet-address-range.png)

1. 次に、仮想ネットワーク ウィンドウの左側のメニューで、 **[サブネット]** を選択し、 **[+ サブネット]** を選択してサブネットを追加します。

1. **[+ サブネット]** を選択し、サブネットの名前を入力します (例: *management*)。 **[アドレス範囲 (CIDR ブロック)]** を指定します (例: *10.0.5.0/24*)。 この IP アドレス範囲が、他の既存の Azure またはオンプレミスのアドレス範囲と重複していないことを確認します。 他のオプションは既定値のままにして、 **[OK]** を選択します。

    ![Azure portal でサブネットの構成を作成する](./media/join-windows-vm/create-subnet.png)

1. サブネットの作成には数秒かかります。 作成されたら、 *[X]* を選択してサブネット ウィンドウを閉じます。
1. **[ネットワーク]** ウィンドウに戻って VM を作成し、ドロップダウン メニューから作成したサブネットを選択します (例: *management*)。 ここでも、適切なサブネットを選択します。マネージド ドメインと同じサブネットには VM をデプロイしないでください。
1. **[パブリック IP]** には、ドロップダウン メニューから *[なし]* を選択します。 このチュートリアルでは、Azure Bastion を使用して "management" に接続するため、VM にパブリック IP アドレスを割り当てる必要はありません。
1. 他のオプションは既定値のままにして、 **[管理]** を選択します。
1. **[ブート診断]** を *[オフ]* に設定します。 他のオプションは既定値のままにして、 **[確認と作成]** を選択します。
1. VM の設定を確認して、 **[作成]** を選択します。

VM の作成には数分かかります。 Azure portal に、デプロイの状態が表示されます。 VM の準備ができたら、 **[リソースに移動]** を選択します。

![正常に作成されたら Azure portal で VM リソースにアクセスする](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Windows Server VM に接続する

VM に対して安全に接続するために、Azure Bastion ホストを使用します。 Azure Bastion では、仮想ネットワークにマネージド ホストがデプロイされ、VM への Web ベースの RDP 接続または SSH 接続は、そのマネージド ホストによって提供されます。 VM にパブリック IP アドレスは不要であり、外部のリモート トラフィック向けにネットワーク セキュリティ グループの規則を開放する必要もありません。 VM には、Web ブラウザーから Azure portal を使用して接続します。 必要に応じて [Azure Bastion ホストを作成][azure-bastion]してください。

要塞ホストを使用して VM に接続するには、次の手順を実行します。

1. VM の **[概要]** ペインで **[接続]** を選択し、 **[Bastion]** を選択します。

    ![Azure portal から Bastion を使用して Windows 仮想マシンに接続する](./media/join-windows-vm/connect-to-vm.png)

1. 前のセクションで指定した VM の資格情報を入力し、 **[接続]** を選択します。

   ![Azure portal から Bastion ホストを使用して接続する](./media/join-windows-vm/connect-to-bastion.png)

必要に応じて、ポップアップの表示を Web ブラウザーに許可して、Bastion 接続を表示します。 VM への接続には数秒かかります。

## <a name="join-the-vm-to-the-managed-domain"></a>VM をマネージド ドメインに参加させる

VM を作成し、Azure Bastion を使用して Web ベースの RDP 接続を確立したので、Windows Server 仮想マシンをマネージド ドメインに参加させましょう。 このプロセスは、通常のオンプレミスの Active Directory Domain Services ドメインに接続しているコンピューターと同じです。

1. VM にサインインしたときに **サーバー マネージャー** が既定で開かない場合は、 **[スタート]** メニューを選択し、 **[サーバー マネージャー]** を選択します。
1. **[サーバー マネージャー]** ウィンドウの左側のウィンドウで、 **[ローカル サーバー]** を選択します。 右側のウィンドウの **[プロパティ]** で、 **[ワークグループ]** を選択します。

    ![VM でサーバー マネージャーを開き、ワークグループのプロパティを編集する](./media/join-windows-vm/server-manager.png)

1. **[システム プロパティ]** ウィンドウで **[変更]** を選択し、マネージド ドメインに参加させます。

    ![ワークグループまたはドメイン プロパティの変更を選択する](./media/join-windows-vm/change-domain.png)

1. **[ドメイン]** ボックスでマネージド ドメインの名前を指定し (例: *aaddscontoso.com*)、 **[OK]** を選択します。

    ![参加するマネージド ドメインを指定する](./media/join-windows-vm/join-domain.png)

1. ドメインの資格情報を入力してドメインに参加します。 マネージド ドメインの一部であるユーザーの資格情報を指定します。 アカウントはマネージド ドメインまたは Azure AD テナントの一部である必要があります。Azure AD テナントに関連付けられている外部ディレクトリのアカウントが、ドメイン参加プロセス中に正しく認証を行うことはできません。

    アカウントの資格情報は、次のいずれかの方法で指定できます。

    * **UPN 形式** (推奨) - Azure AD で構成したように、ユーザー アカウントのユーザー プリンシパル名 (UPN) サフィックスを入力します。 たとえば、ユーザー *contosoadmin* の UPN サフィックスは `contosoadmin@aaddscontoso.onmicrosoft.com` になります。 *SAMAccountName* 形式ではなく UPN 形式を使用するとドメインに確実にサインインできる一般的なユースケースが 2 つあります。
        * ユーザーの UPN プレフィックスが長い場合 (例: *deehasareallylongname*)、*SAMAccountName* が自動生成される場合があります。
        * Azure AD テナントで複数のユーザーが同じ UPN プレフィックスを使用していると (例: *dee*)、*SAMAccountName* 形式が自動生成される場合があります。
    * **SAMAccountName 形式** - *SAMAccountName* 形式でアカウント名を入力します。 たとえば、ユーザー *contosoadmin* の *SAMAccountName* は `AADDSCONTOSO\contosoadmin` になります。

1. マネージド ドメインへの参加には数秒かかります。 完了すると、ドメインへの参加を歓迎する次のようなメッセージが表示されます。

    ![ドメインへようこそ](./media/join-windows-vm/join-domain-successful.png)

    **[OK]** を選択して続行します。

1. マネージド ドメインに参加させるプロセスを完了するには、VM を再起動します。

> [!TIP]
> PowerShell の [Add-Computer][add-computer] コマンドレットを使用して、VM をドメインに参加させることができます。 次の例では、*AADDSCONTOSO* ドメインに参加した後、VM を再起動しています。 メッセージが表示されたら、マネージド ドメインの一部であるユーザーの資格情報を入力します。
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> VM に接続したり手動で接続を構成したりすることなく VM をドメインに参加させるには、[Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell コマンドレットを使用できます。

Windows Server VM が再起動すると、マネージド ドメインで適用されているすべてのポリシーが VM にプッシュされます。 また、適切なドメイン資格情報を使用して Windows Server VM にサインインすることもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルでは、この Windows Server VM を使用して、マネージド ドメインを管理できる管理ツールをインストールします。 このチュートリアル シリーズを続けない場合は、次のクリーンアップ手順を確認して、[VM を削除](#delete-the-vm)します。 そうでない場合は、[次のチュートリアルを続けます](#next-steps)。

### <a name="unjoin-the-vm-from-the-managed-domain"></a>マネージド ドメインへの VM の参加を解除する

マネージド ドメインから VM を削除するには、もう一度、[VM をドメインに参加させる](#join-the-vm-to-the-managed-domain)ための手順を実行します。 このとき、マネージド ドメインに参加させる代わりに、WORKGROUP (既定の "*WORKGROUP*" など) に参加させることを選択します。 VM が再起動すると、コンピューター オブジェクトがマネージド ドメインから削除されます。

ドメインへの参加を解除せずに [VM を削除](#delete-the-vm)すると、孤立したコンピューター オブジェクトが Azure AD DS に残されます。

### <a name="delete-the-vm"></a>VM の削除

この Windows Server VM をもう使わない場合は、次の手順に従って VM を削除します。

1. 左側のメニューから、 **[リソース グループ]** を選択します
1. お使いのリソース グループを選択します (例: *myResourceGroup*)。
1. お使いの VM を選択し (例: *myVM*)、 **[削除]** を選択します。 **[はい]** を選択して、リソースの削除を確定します。 VM の削除には数分かかります。
1. VM が削除されたら、*myVM-* プレフィックスが付いている OS ディスク、ネットワーク インターフェイス カード、その他のすべてのリソースを選択して削除します。

## <a name="troubleshoot-domain-join-issues"></a>ドメイン参加の問題のトラブルシューティング

通常のオンプレミス コンピューターを Active Directory Domain Services ドメインに参加させる場合と同じ方法で、Windows Server VM もマネージド ドメインに正常に参加させることができるはずです。 Windows Server VM をマネージド ドメインに参加させることができない場合は、接続または資格情報に関連する問題があることを示しています。 マネージド ドメインに正常に参加させるには、次のトラブルシューティングのセクションを確認してください。

### <a name="connectivity-issues"></a>接続に関する問題

ドメインに参加するための資格情報を要求するプロンプトが表示されない場合は、接続に問題があります。 仮想ネットワーク上のマネージド ドメインに VM が到達できません。

以下の各トラブルシューティング手順を実行した後、Windows Server VM をマネージド ドメインに再度参加させてください。

* Azure AD DS が有効になっているのと同じ仮想ネットワークに VM が接続されていること、または VM にピアリングされたネットワーク接続があることを確認します。
* マネージド ドメインの DNS ドメイン名に対して ping を実行します (例: `ping aaddscontoso.com`)。
    * ping 要求が失敗する場合は、マネージド ドメインの IP アドレスに対して ping を実行します (例: `ping 10.0.0.4`)。 環境の IP アドレスは、Azure リソースの一覧からマネージド ドメインを選択すると、 *[プロパティ]* ページに表示されます。
    * ドメインではなく IP アドレスを ping できた場合、DNS が正しく構成されていない可能性があります。 マネージド ドメインの IP アドレスが仮想ネットワークの DNS サーバーとして構成されていることを確認します。
* `ipconfig /flushdns` コマンドを使用して、仮想マシンの DNS リゾルバー キャッシュをフラッシュしてみます。

### <a name="credentials-related-issues"></a>資格情報に関連した問題

ドメインに参加するための資格情報を要求するプロンプトが表示されても、資格情報を入力した後でエラーが発生する場合は、VM はマネージド ドメインに接続できます。 指定した資格情報では、VM をマネージド ドメインに参加させることはできません。

以下の各トラブルシューティング手順を実行した後、Windows Server VM をマネージド ドメインに再度参加させてください。

* 指定するユーザー アカウントがマネージド ドメインに属していることを確認します。
* アカウントがマネージド ドメインまたは Azure AD テナントの一部であることを確認してください。 Azure AD テナントに関連付けられている外部ディレクトリのアカウントが、ドメイン参加プロセス中に正しく認証を行うことはできません。
* UPN 形式を使用して資格情報を指定します (例: `contosoadmin@aaddscontoso.onmicrosoft.com`)。 たくさんのユーザーがテナントで同じ UPN プレフィックスを使用している場合、または UPN プレフィックスが最大文字数を超えている場合は、アカウントの *SAMAccountName* が自動生成される可能性があります。 そのような場合、アカウントの *SAMAccountName* 形式が、想定されている形式やオンプレミス ドメインで使用されている形式と異なる可能性があります。
* マネージド ドメインとの[パスワード同期を有効にしている][password-sync]ことを確認します。 この構成手順を行わないと、サインインの試行を正しく認証するために必要なパスワード ハッシュがマネージド ドメインに存在しなくなります。
* パスワード同期が完了するまで待ちます。 ユーザー アカウントのパスワードが変更されると、Azure AD からの自動バックグラウンド同期によって、Azure AD DS 内のパスワードが更新されます。 ドメインへの参加にパスワードを使用できるようになるまでに、しばらく時間がかかります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Windows Server VM を作成する
> * Windows Server VM を Azure 仮想ネットワークに接続する
> * VM をマネージド ドメインに参加させる

マネージド ドメインを管理するには、Active Directory 管理センター (ADAC) を使用して管理 VM を構成します。

> [!div class="nextstepaction"]
> [管理 VM に管理ツールをインストールする](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension