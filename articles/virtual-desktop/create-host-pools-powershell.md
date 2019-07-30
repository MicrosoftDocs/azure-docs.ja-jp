---
title: PowerShell を使用した Windows Virtual Desktop プレビューのホスト プールの作成 - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop プレビューにホスト プールを作成する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: helohr
ms.openlocfilehash: 374d5a8f51e28b8a10595842cfc301db503b6bed
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613327"
---
# <a name="create-a-host-pool-with-powershell"></a>PowerShell を使用してホスト プールを作成する

ホスト プールは、Windows Virtual Desktop プレビュー テナント環境内の 1 つ以上の同一仮想マシンのコレクションです。 各ホスト プールには、物理デスクトップ上と同様にユーザーが対話できるアプリ グループを含めることができます。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell クライアントを使用してホスト プールを作成する

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します。

次のコマンドレットを実行して Windows Virtual Desktop 環境にサインインします

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

次に、このコマンドレットを実行して、Windows Virtual Desktop テナントに新しいホスト プールを作成します。

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

次のコマンドレットを実行して、セッション ホストがホスト プールに参加することを承認する登録トークンを作成し、それをローカル コンピューター上の新しいファイルに保存します。 -ExpirationHours パラメーターを使用して、登録トークンが有効である時間の長さを指定できます。

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

その後、このコマンドレットを実行して、Azure Active Directory ユーザーをホスト プールの既定のデスクトップ アプリ グループに追加します。

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**Add-RdsAppGroupUser** コマンドレットではセキュリティ グループの追加がサポートされず、一度に 1 人のユーザーのみがアプリ グループに追加されます。 アプリ グループに複数のユーザーを追加する場合は、適切なユーザー プリンシパル名を使用してコマンドレットを再実行します。

次のコマンドレットを実行して、登録トークンを変数にエクスポートします。これは後で [Windows Virtual Desktop ホスト プールに仮想マシンを登録](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool)するときに使用します。

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>ホスト プールの仮想マシンを作成する

ここで、Windows Virtual Desktop ホスト プールに参加できる Azure 仮想マシンを作成できます。

仮想マシンは複数の方法で作成できます。

- [Azure ギャラリー イメージから仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [マネージド イメージから仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [アンマネージド イメージから仮想マシンを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Windows Virtual Desktop プレビュー エージェントのインストールの仮想マシンを準備する

Windows Virtual Desktop エージェントをインストールして仮想マシンを Windows Virtual Desktop ホスト プールに登録する前に、次の操作を行って仮想マシンを準備する必要があります。

- マシンをドメインに参加させる必要があります。 これにより、受信 Windows Virtual Desktop ユーザーを Azure Active Directory アカウントから Active Directory アカウントにマップでき、仮想マシンへのアクセスを正常に許可できます。
- 仮想マシンで Windows Server OS を実行している場合は、リモート デスクトップ セッション ホスト (RDSH) ロールをインストールする必要があります。 RDSH ロールでは、Windows Virtual Desktop エージェントを正常にインストールできます。

正常にドメイン参加するには、各仮想マシン上で次の操作を実行します。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)します。
2. 仮想マシン上で、 **[コントロール パネル]** を起動し、 **[システム]** を選択します。
3. **[コンピューター名]** を選択し、 **[設定の変更]** を選択してから **[変更]** を選択します
4. **[ドメイン]** を選択し、仮想ネットワーク上の Active Directory ドメインを入力します。
5. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

    >[!NOTE]
    > ご使用の VM を Azure AD Domain Services 環境へ参加させる場合は、ドメイン参加ユーザーが [AAD DC Administrators グループ](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group)のメンバーでもあることを確認します。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Windows Virtual Desktop プレビューのホスト プールに仮想マシンを登録する

Windows Virtual Desktop ホスト プールへの仮想マシンの登録は、Windows Virtual Desktop エージェントのインストールと同じくらい簡単です。

Windows Virtual Desktop エージェントを登録するには、各仮想マシン上で次の操作を行います。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)します。
2. Windows Virtual Desktop エージェントをダウンロードしてインストールします。
   - [Windows Virtual Desktop エージェント](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)をダウンロードします。
   - ダウンロードしたインストーラーを右クリックし、 **[プロパティ]** 、 **[ブロック解除]** を選択してから **[OK]** を選択します。 これにより、システムがインストーラーを信頼できます。
   - インストーラーを実行します。 インストーラーから登録トークンを要求されたら、**Export-RdsRegistrationInfo** コマンドレットから取得した値を入力します。
3. Windows Virtual Desktop エージェント ブートローダーをダウンロードしてインストールします。
   - [Windows Virtual Desktop エージェント ブートローダー](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)をダウンロードします。
   - ダウンロードしたインストーラーを右クリックし、 **[プロパティ]** 、 **[ブロック解除]** を選択してから **[OK]** を選択します。 これにより、システムがインストーラーを信頼できます。
   - インストーラーを実行します。

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合、[Just-In-Time VM アクセス](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)を使用することをお勧めします。

## <a name="next-steps"></a>次の手順

これでホスト プールが作成されたので、RemoteApp を設定できます。 Windows Virtual Desktop 内でアプリを管理する方法について詳しくは、アプリ グループの管理のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [アプリ グループの管理に関するチュートリアル](./manage-app-groups.md)
