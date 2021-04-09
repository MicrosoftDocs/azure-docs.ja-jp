---
title: PowerShell を使用して Windows Virtual Desktop (クラシック) のホスト プールを作成する - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop (クラシック) にホスト プールを作成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c035a7fbafe9b3a42fbd16e3f8377014010ddd49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88003552"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-powershell"></a>PowerShell を使用して Windows Virtual Desktop (クラシック) でホスト プールを作成する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../create-host-pools-powershell.md)を参照してください。

ホスト プールは、Windows Virtual Desktop テナント環境内にある 1 つまたは複数の同一の仮想マシンをコレクションとしてまとめたものです。 各ホスト プールには、物理デスクトップの場合と同じようにユーザーが利用できるアプリ グループを含めることができます。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell クライアントを使用してホスト プールを作成する

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します。

次のコマンドレットを実行して Windows Virtual Desktop 環境にサインインします

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

次に、このコマンドレットを実行して、Windows Virtual Desktop テナントに新しいホスト プールを作成します。

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

次のコマンドレットを実行して、セッション ホストがホスト プールに参加することを承認する登録トークンを作成し、それをローカル コンピューター上の新しいファイルに保存します。 -ExpirationHours パラメーターを使用して、登録トークンが有効である時間の長さを指定できます。

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

その後、このコマンドレットを実行して、Azure Active Directory ユーザーをホスト プールの既定のデスクトップ アプリ グループに追加します。

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Add-RdsAppGroupUser** コマンドレットではセキュリティ グループの追加がサポートされず、一度に 1 人のユーザーのみがアプリ グループに追加されます。 アプリ グループに複数のユーザーを追加する場合は、適切なユーザー プリンシパル名を使用してコマンドレットを再実行します。

次のコマンドレットを実行して、登録トークンを変数にエクスポートします。これは後で [Windows Virtual Desktop ホスト プールに仮想マシンを登録](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)するときに使用します。

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>ホスト プールの仮想マシンを作成する

ここで、Windows Virtual Desktop ホスト プールに参加できる Azure 仮想マシンを作成できます。

仮想マシンは複数の方法で作成できます。

- [Azure ギャラリー イメージから仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [マネージド イメージから仮想マシンを作成する](../../virtual-machines/windows/create-vm-generalized-managed.md)
- [アンマネージド イメージから仮想マシンを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>ホスト OS として Windows 7 を使用して仮想マシンをデプロイしている場合、作成と展開のプロセスは若干異なります。 詳細については、「[Windows Virtual Desktop で Windows 7 仮想マシンをデプロイする](deploy-windows-7-virtual-machine.md)」を参照してください。

セッション ホスト仮想マシンを作成したら、[Windows ライセンスをセッション ホスト VM に適用](../apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)すると、ライセンス料をさらに支払うことなく、Windows または Windows Server の仮想マシンが実行されます。

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Windows Virtual Desktop エージェントのインストール用に仮想マシンを準備する

Windows Virtual Desktop エージェントをインストールして仮想マシンを Windows Virtual Desktop ホスト プールに登録する前に、次の操作を行って仮想マシンを準備する必要があります。

- マシンをドメインに参加させる必要があります。 これにより、受信 Windows Virtual Desktop ユーザーを Azure Active Directory アカウントから Active Directory アカウントにマップでき、仮想マシンへのアクセスを正常に許可できます。
- 仮想マシンで Windows Server OS を実行している場合は、リモート デスクトップ セッション ホスト (RDSH) ロールをインストールする必要があります。 RDSH ロールでは、Windows Virtual Desktop エージェントを正常にインストールできます。

正常にドメイン参加するには、各仮想マシン上で次の操作を実行します。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)します。
2. 仮想マシン上で、 **[コントロール パネル]** を起動し、 **[システム]** を選択します。
3. **[コンピューター名]** を選択し、 **[設定の変更]** を選択してから **[変更]** を選択します
4. **[ドメイン]** を選択し、仮想ネットワーク上の Active Directory ドメインを入力します。
5. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

    >[!NOTE]
    > ご使用の VM を Azure Active Directory Domain Services (Azure AD DS) 環境へ参加させる場合は、ドメイン参加ユーザーが [AAD DC Administrators グループ](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)のメンバーでもあることを確認します。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Windows Virtual Desktop ホスト プールに仮想マシンを登録する

Windows Virtual Desktop ホスト プールへの仮想マシンの登録は、Windows Virtual Desktop エージェントのインストールと同じくらい簡単です。

Windows Virtual Desktop エージェントを登録するには、各仮想マシン上で次の操作を行います。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](../../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)します。
2. Windows Virtual Desktop エージェントをダウンロードしてインストールします。
   - [Windows Virtual Desktop エージェント](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)をダウンロードします。
   - ダウンロードしたインストーラーを右クリックし、 **[プロパティ]** 、 **[ブロック解除]** を選択してから **[OK]** を選択します。 これにより、システムがインストーラーを信頼できます。
   - インストーラーを実行します。 インストーラーから登録トークンを要求されたら、**Export-RdsRegistrationInfo** コマンドレットから取得した値を入力します。
3. Windows Virtual Desktop エージェント ブートローダーをダウンロードしてインストールします。
   - [Windows Virtual Desktop エージェント ブートローダー](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)をダウンロードします。
   - ダウンロードしたインストーラーを右クリックし、 **[プロパティ]** 、 **[ブロック解除]** を選択してから **[OK]** を選択します。 これにより、システムがインストーラーを信頼できます。
   - インストーラーを実行します。

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合は、[Just-In-Time VM アクセス](../../security-center/security-center-just-in-time.md)を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

これでホスト プールが作成されたので、RemoteApp を設定できます。 Windows Virtual Desktop 内でアプリを管理する方法に関する詳細については、アプリ グループの管理に関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [アプリ グループの管理に関するチュートリアル](../manage-app-groups.md)
