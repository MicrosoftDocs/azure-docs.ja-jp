---
title: PowerShell を使用した Windows Virtual Desktop プレビューのホスト プールの作成 - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop プレビューにホスト プールを作成する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 2af9df4771d58f2288820dad8ef8d7ac84deb8ae
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59258472"
---
# <a name="create-a-host-pool-with-powershell"></a>PowerShell を使用してホスト プールを作成する

ホスト プールは、Windows Virtual Desktop プレビュー テナント環境内の 1 つ以上の同一仮想マシンのコレクションです。 各ホスト プールには、物理デスクトップ上と同様にユーザーが対話できるアプリ グループを含めることができます。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell クライアントを使用してホスト プールを作成する

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します。

次のコマンドレットを実行して Windows Virtual Desktop 環境にサインインします

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

その後、次のコマンドレットを実行してテナント グループにコンテキストを設定します。 テナント グループの名前がない場合、テナントは "既定のテナント グループ" にある可能性が最も高いので、このコマンドレットはスキップできます。

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
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
2. 仮想マシン上で、**[コントロール パネル]** を起動し、**[システム]** を選択します。
3. **[コンピューター名]** を選択し、**[設定の変更]** を選択してから **[変更]** を選択します
4. **[ドメイン]** を選択し、仮想ネットワーク上の Active Directory ドメインを入力します。
5. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Windows Virtual Desktop プレビューのホスト プールに仮想マシンを登録する

Windows Virtual Desktop ホスト プールへの仮想マシンの登録は、Windows Virtual Desktop エージェントのインストールと同じくらい簡単です。

Windows Virtual Desktop エージェントを登録するには、各仮想マシン上で次の操作を行います。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)します。
2. Windows Virtual Desktop エージェントをダウンロードしてインストールします。
   - [Windows Virtual Desktop エージェント](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)をダウンロードします。
   - ダウンロードしたインストーラーを右クリックし、**[プロパティ]**、**[ブロック解除]** を選択してから **[OK]** を選択します。 これにより、システムがインストーラーを信頼できます。
   - インストーラーを実行します。 インストーラーから登録トークンを要求されたら、**Export-RdsRegistrationInfo** コマンドレットから取得した値を入力します。
3. Windows Virtual Desktop エージェント ブートローダーをダウンロードしてインストールします。
   - [Windows Virtual Desktop エージェント ブートローダー](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)をダウンロードします。
   - ダウンロードしたインストーラーを右クリックし、**[プロパティ]**、**[ブロック解除]** を選択してから **[OK]** を選択します。 これにより、システムがインストーラーを信頼できます。
   - インストーラーを実行します。
4. Windows Virtual Desktop サイドバイサイド スタックをインストールまたはアクティブ化します。 手順は、仮想マシンで使用されている OS バージョンに応じて異なります。
   - 仮想マシンの OS が Windows Server 2016 の場合:
     - [Windows Virtual Desktop サイドバイサイド スタック](https://go.microsoft.com/fwlink/?linkid=2084270)をダウンロードします。
     - ダウンロードしたインストーラーを右クリックし、**[プロパティ]**、**[ブロック解除]** を選択してから **[OK]** を選択します。 これにより、システムがインストーラーを信頼できます。
     - インストーラーを実行します。
   - 仮想マシンの OS が Windows 10 1809 以降または Windows Server 2019 以降の場合:
     - サイド バイ サイド スタックをアクティブ化する[スクリプト](https://go.microsoft.com/fwlink/?linkid=2084268)をダウンロードします。
     - ダウンロードしたスクリプトを右クリックし、**[プロパティ]**、**[ブロック解除]** を選択してから **[OK]** を選択します。 これにより、システムがスクリプトを信頼できます。
     - **[スタート]** メニューから、Windows PowerShell ISE を検索して右クリックし、**[管理者として実行]** を選択します。
     - **[ファイル]**、**[開く]** を選択し、ダウンロードしたファイルから PowerShell スクリプトを検索して開きます。
     - 緑色の再生ボタンを選択してスクリプトを実行します。

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合、[Just-In-Time VM アクセス](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time)を使用することをお勧めします。

## <a name="next-steps"></a>次の手順

これでホスト プールが作成されたので、RemoteApp を設定できます。 Windows Virtual Desktop 内でアプリを管理する方法について詳しくは、アプリ グループの管理のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [アプリ グループの管理に関するチュートリアル](./manage-app-groups.md)
