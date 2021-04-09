---
title: PowerShell を使用して Windows Virtual Desktop のホスト プールを作成する - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop にホスト プールを作成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/02/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ec900f0537030d3ed0d1c875e8125806159bd51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251456"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>PowerShell を使用した Windows Virtual Desktop のホスト プールの作成

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (classic) を使用している場合は、[この記事](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md)を参照してください。

ホスト プールは、Windows Virtual Desktop テナント環境内にある 1 つまたは複数の同一の仮想マシンをコレクションとしてまとめたものです。 各ホスト プールは、複数の RemoteApp グループ、1 つのデスクトップ アプリ グループ、および複数のセッション ホストに関連付けることができます。

## <a name="prerequisites"></a>前提条件

この記事では、「[PowerShell モジュールを設定する](powershell-module.md)」の手順に従っていることを前提としています。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell クライアントを使用してホスト プールを作成する

次のコマンドレットを実行して Windows Virtual Desktop 環境にサインインします。

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

このコマンドレットにより、ホスト プール、ワークスペース、およびデスクトップ アプリ グループが作成されます。 さらに、デスクトップ アプリ グループがワークスペースに登録されます。 このコマンドレットを使用してワークスペースを作成するか、既存のワークスペースを使用することができます。

次のコマンドレットを実行して、セッション ホストがホスト プールに参加することを承認する登録トークンを作成し、それをローカル コンピューター上の新しいファイルに保存します。 -ExpirationHours パラメーターを使用して、登録トークンが有効である時間の長さを指定できます。

>[!NOTE]
>トークンの有効期限は 1 時間以上、1 か月以内にする必要があります。 この制限を超えて *-ExpirationTime* を設定した場合、コマンドレットではトークンが作成されません。

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

たとえば、2 時間以内に有効期限が切れるトークンを作成する場合は、次のコマンドレットを実行します。

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

その後、このコマンドレットを実行して、Azure Active Directory ユーザーをホスト プールの既定のデスクトップ アプリ グループに追加します。

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

次のコマンドレットを実行して、Azure Active Directory ユーザー グループをホスト プールの既定のデスクトップ アプリ グループに追加します。

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

次のコマンドレットを実行して、登録トークンを変数にエクスポートします。これは後で [Windows Virtual Desktop ホスト プールに仮想マシンを登録](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)するときに使用します。

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>ホスト プールの仮想マシンを作成する

ここで、Windows Virtual Desktop ホスト プールに参加できる Azure 仮想マシンを作成できます。

仮想マシンは複数の方法で作成できます。

- [Azure ギャラリー イメージから仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [マネージド イメージから仮想マシンを作成する](../virtual-machines/windows/create-vm-generalized-managed.md)
- [アンマネージド イメージから仮想マシンを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>ホスト OS として Windows 7 を使用して仮想マシンをデプロイしている場合、作成と展開のプロセスは若干異なります。 詳細については、「[Windows Virtual Desktop で Windows 7 仮想マシンをデプロイする](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)」を参照してください。

セッション ホスト仮想マシンを作成したら、[Windows ライセンスをセッション ホスト VM に適用](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)すると、ライセンス料をさらに支払うことなく、Windows または Windows Server の仮想マシンが実行されます。

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Windows Virtual Desktop エージェントのインストール用に仮想マシンを準備する

Windows Virtual Desktop エージェントをインストールして仮想マシンを Windows Virtual Desktop ホスト プールに登録する前に、次の操作を行って仮想マシンを準備する必要があります。

- マシンをドメインに参加させる必要があります。 これにより、受信 Windows Virtual Desktop ユーザーを Azure Active Directory アカウントから Active Directory アカウントにマップでき、仮想マシンへのアクセスを正常に許可できます。
- 仮想マシンで Windows Server OS を実行している場合は、リモート デスクトップ セッション ホスト (RDSH) ロールをインストールする必要があります。 RDSH ロールでは、Windows Virtual Desktop エージェントを正常にインストールできます。

正常にドメイン参加するには、各仮想マシン上で次の操作を実行します。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)します。
2. 仮想マシン上で、 **[コントロール パネル]** を起動し、 **[システム]** を選択します。
3. **[コンピューター名]** を選択し、 **[設定の変更]** を選択してから **[変更]** を選択します
4. **[ドメイン]** を選択し、仮想ネットワーク上の Active Directory ドメインを入力します。
5. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

    >[!NOTE]
    > ご使用の VM を Azure Active Directory Domain Services (Azure AD DS) 環境へ参加させる場合は、ドメイン参加ユーザーが [AAD DC Administrators グループ](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)のメンバーでもあることを確認します。

>[!IMPORTANT]
>Windows インストーラーを無効にするポリシーや構成は、有効にしないことをお勧めします。 Windows インストーラーを無効にすると、サービスでセッション ホストにエージェントの更新をインストールできなくなり、セッション ホストが正常に機能しなくなります。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Windows Virtual Desktop ホスト プールに仮想マシンを登録する

Windows Virtual Desktop ホスト プールへの仮想マシンの登録は、Windows Virtual Desktop エージェントのインストールと同じくらい簡単です。

Windows Virtual Desktop エージェントを登録するには、各仮想マシン上で次の操作を行います。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)します。
2. Windows Virtual Desktop エージェントをダウンロードしてインストールします。
   - [Windows Virtual Desktop エージェント](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)をダウンロードします。
   - インストーラーを実行します。 インストーラーから登録トークンを要求されたら、**Get-AzWvdRegistrationInfo** コマンドレットから取得した値を入力します。
3. Windows Virtual Desktop エージェント ブートローダーをダウンロードしてインストールします。
   - [Windows Virtual Desktop エージェント ブートローダー](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)をダウンロードします。
   - インストーラーを実行します。

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合は、[Just-In-Time VM アクセス](../security-center/security-center-just-in-time.md)を使用することをお勧めします。 また、パブリック IP に VM を割り当てないことをお勧めします。

## <a name="update-the-agent"></a>エージェントを更新する

次のいずれかの状況に該当する場合は、エージェントを更新する必要があります。

- 以前に登録されたセッション ホストを新しいホスト プールに移行する必要がある
- 更新後にセッション ホストがホスト プールに表示されない

エージェントを更新するには、次の操作を実行します。

1. 管理者として VM にサインインします。
2. **[サービス]** に移動して、**Rdagent** プロセス、**Remote Desktop Agent Loader** プロセスの順に停止します。
3. 次に、エージェント MSI とブートローダー MSI を見つけます。 これらは、**C:\DeployAgent** フォルダー、またはインストール時に保存した場所のいずれかに配置されています。
4. 次のファイルを見つけて、アンインストールします。
     
     - Microsoft.RDInfra.RDAgent.Installer-x64-verx.x.x
     - Microsoft.RDInfra.RDAgentBootLoader.Installer-x64

   これらのファイルをアンインストールするには、各ファイル名を右クリックし、 **[アンインストール]** を選択します。
5. 必要に応じて、次のレジストリ設定を削除することもできます。
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. これらの項目をアンインストールすると、古いホスト プールとの関連付けがすべて削除されます。 このホストをサービスに再登録する場合は、「[Windows Virtual Desktop ホスト プールに仮想マシンを登録する](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)」の手順に従います。


## <a name="next-steps"></a>次のステップ

これでホスト プールが作成されたので、RemoteApp を設定できます。 Windows Virtual Desktop 内でアプリを管理する方法に関する詳細については、アプリ グループの管理に関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [アプリ グループの管理に関するチュートリアル](./manage-app-groups.md)
