---
title: 仮想マシンベースのファイル共有を使用してホスト プール用の FSLogix プロファイル コンテナーを作成する  - Azure
description: 仮想マシンベースのファイル共有を使用して Windows Virtual Desktop プレビュー ホスト プールの FSLogix プロファイル コンテナーを設定する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
ms.openlocfilehash: cf3d682e4d0c68822267a4e63846d80b632cbdcc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876791"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成する

Windows Virtual Desktop プレビュー サービスでは、推奨されるユーザー プロファイル ソリューションとして FSLogix プロファイル コンテナーが提供されます。 ユーザー プロファイル ディスク (UPD) ソリューションの使用は推奨されず、Windows Virtual Desktop の将来のバージョンでは非推奨になります。

この記事では、仮想マシンベースのファイル共有を使用してホスト プールの FSLogix プロファイル コンテナー共有を設定する方法について説明します。 FSLogix のその他のドキュメントについては、[FSLogix サイト](https://docs.fslogix.com/)を参照してください。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>ファイル共有として機能する新しい仮想マシンを作成する

仮想マシンを作成するときは、ホスト プール仮想マシンと同じ仮想ネットワーク、またはホスト プール仮想マシンに接続されている仮想ネットワークに配置してください。 仮想マシンは複数の方法で作成できます。

- [Azure ギャラリー イメージから仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [マネージド イメージから仮想マシンを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [アンマネージド イメージから仮想マシンを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

仮想マシンの作成後に、次の手順を実行してドメインに参加させます。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)します。
2. 仮想マシン上で、 **[コントロール パネル]** を起動し、 **[システム]** を選択します。
3. **[コンピューター名]** を選択し、 **[設定の変更]** を選択してから **[変更]** を選択します
4. **[ドメイン]** を選択し、仮想ネットワーク上の Active Directory ドメインを入力します。
5. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>ユーザー プロファイルのファイル共有として機能する仮想マシンを準備する

ユーザー プロファイルのファイル共有として機能する仮想マシンを準備する方法についての一般的な手順を次に示します。

1. Windows Virtual Desktop の Active Directory ユーザーを [Active Directory セキュリティ グループ](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups)に追加します。 このセキュリティ グループは、先ほど作成したファイル共有仮想マシンに対して、Windows Virtual Desktop ユーザーを認証するために使用されます。
2. [ファイル共有仮想マシンに接続します](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)。
3. ファイル共有仮想マシン上で、プロファイル共有として使用するフォルダーを **C ドライブ**に作成します。
4. 新しいフォルダーを右クリックし、 **[プロパティ]** 、 **[共有]** 、 **[詳細な共有]** を選択します。
5. **[このフォルダーを共有する]** を選択し、 **[アクセス許可]** を選択してから、 **[追加]** を選択します。
6. Windows Virtual Desktop ユーザーを追加したセキュリティ グループを検索し、そのグループに**フル コントロール**があることを確認します。
7. セキュリティ グループを追加した後、フォルダーを右クリックして **[プロパティ]** を選択し、 **[共有]** を選択して、後で使用するために **[ネットワーク パス]** をコピーしておきます。

アクセス許可について詳しくは、[FSLogix のドキュメント](https://docs.microsoft.com/fslogix/fslogix-storage-config-ht)をご覧ください。

## <a name="configure-the-fslogix-profile-container"></a>FSLogix プロファイル コンテナーを構成する

FSLogix ソフトウェアで仮想マシンを構成するには、ホスト プールに登録される各マシンで、以下を実行します。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)します。
2. インターネット ブラウザーを起動し、[このリンク](https://go.microsoft.com/fwlink/?linkid=2084562)に移動して FSLogix エージェントをダウンロードします。
3. .zip ファイル内の \\\\Win32\\Release または \\\\X64\\Release のいずれかに移動し、**FSLogixAppsSetup** を実行して FSLogix エージェントをインストールします。  FSLogix のインストール方法の詳細については、「[Download and install FSLogix (FSLogix のダウンロードとインストール)](https://docs.microsoft.com/fslogix/install-ht)」を参照してください。
4. **Program Files** > **FSLogix** > **Apps** に移動して、インストールされているエージェントを確認します。
5. スタート メニューから、**RegEdit** を管理者として実行します。 **Computer\\HKEY_LOCAL_MACHINE\\software\\FSLogix** に移動します。
6. **Profiles** という名前のキーを作成します。
7. Profiles キーに以下の値を作成します。

| Name                | Type               | データ/値                        |
|---------------------|--------------------|-----------------------------------|
| 有効             | DWORD              | 1                                 |
| VHDLocations        | 複数行文字列値 | "ファイル共有のネットワーク パス"     |

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合は、[Just-In-Time VM アクセス](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)を使用することをお勧めします。