---
title: Windows Virtual Desktop プレビュー ホスト プールのユーザー プロファイル共有を設定する - Azure
description: Windows Virtual Desktop プレビュー ホスト プールの FSLogix プロファイル コンテナーを設定する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: af4147de06f9fb7c856dfd93dc186f1a6e83ffff
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58628984"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>ホスト プールのユーザー プロファイル共有を設定する

Windows Virtual Desktop プレビュー サービスでは、推奨されるユーザー プロファイル ソリューションとして FSLogix プロファイル コンテナーが提供されます。 ユーザー プロファイル ディスク (UPD) ソリューションの使用は推奨されず、Windows Virtual Desktop の将来のバージョンでは非推奨になります。

ここでは、ホスト プールに対して FSLogix プロファイル コンテナー共有を設定する方法を説明します。 FSLogix に関する一般的なドキュメントについては、[FSLogix サイト](https://docs.fslogix.com/)を参照してください。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>ファイル共有として機能する新しい仮想マシンを作成する

仮想マシンを作成するときは、ホスト プール仮想マシンと同じ仮想ネットワーク、またはホスト プール仮想マシンに接続されている仮想ネットワークに配置してください。 仮想マシンは複数の方法で作成できます。

- [Azure ギャラリー イメージから仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [マネージド イメージから仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [アンマネージド イメージから仮想マシンを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

仮想マシンの作成後に、次の手順を実行してドメインに参加させます。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)します。
2. 仮想マシン上で、**[コントロール パネル]** を起動し、**[システム]** を選択します。
3. **[コンピューター名]** を選択し、**[設定の変更]** を選択してから **[変更]** を選択します
4. **[ドメイン]** を選択し、仮想ネットワーク上の Active Directory ドメインを入力します。
5. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>ユーザー プロファイルのファイル共有として機能する仮想マシンを準備する

ユーザー プロファイルのファイル共有として機能する仮想マシンを準備する方法についての一般的な手順を次に示します。

1. セッション ホスト仮想マシンを [Active Directory セキュリティ グループ](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups)に参加させます。 このセキュリティ グループは、セッション ホスト仮想マシンに対して、先ほど作成したファイル共有仮想マシンを認証するために使用されます。
2. [ファイル共有仮想マシンに接続します](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)。
3. ファイル共有仮想マシン上で、プロファイル共有として使用するフォルダーを **C ドライブ**に作成します。
4. 新しいフォルダーを右クリックし、**[プロパティ]**、**[共有]**、**[詳細な共有]** を選択します。
5. **[このフォルダーを共有する]** を選択し、**[アクセス許可]** を選択してから、**[追加]** を選択します。
6. セッション ホスト仮想マシンを追加したセキュリティ グループを検索し、そのグループに**フル コントロール**があることを確認します。
7. セキュリティ グループを追加した後、フォルダーを右クリックして **[プロパティ]** を選択し、**[共有]** を選択して、後で使用するために **[ネットワーク パス]** をコピーしておきます。

アクセス許可について詳しくは、[FSLogix のドキュメント](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers)をご覧ください。

## <a name="configure-the-fslogix-profile-container"></a>FSLogix プロファイル コンテナーを構成する

FSLogix ソフトウェアで仮想マシンを構成するには、ホスト プールに登録される各マシンで、以下を実行します。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)します。
2. インターネット ブラウザーを起動し、[このリンク](https://go.microsoft.com/fwlink/?linkid=2084562)に移動して FSLogix エージェントをダウンロードします。 Windows Virtual Desktop パブリック プレビューの一部として、FSLogix ソフトウェアをアクティブにするライセンス キーを取得します。 キーは、FSLogix エージェントの .zip ファイルに含まれる LicenseKey.txt ファイルです。
3. FSLogix エージェントをインストールします。
4. **Program Files** > **FSLogix** > **Apps** に移動して、インストールされているエージェントを確認します。
5. スタート メニューから、**RegEdit** を管理者として実行します。 **Computer\\HKEY_LOCAL_MACHINE\\software\\FSLogix\\Profiles** に移動します
6. 次の値を作成します。

| 名前                | Type               | データ/値                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | 複数行文字列値 | "ファイル共有のネットワーク パス" |
| VolumeType          | String             | VHDX                              |
| SizeInMBs           | DWORD              | "プロファイルのサイズの整数"     |
| IsDynamic           | DWORD              | 1                                 |
| LockedRetryCount    | DWORD              | 1                                 |
| LockedRetryInterval | DWORD              | 0                                 |
