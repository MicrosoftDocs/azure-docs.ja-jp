---
title: Windows Virtual Desktop FSLogix プロファイル コンテナー共有 - Azure
description: 仮想マシンベースのファイル共有を使用して Windows Virtual Desktop ホスト プールの FSLogix プロファイル コンテナーを設定する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 35b692033cc16f276b48bc6d973b27d994c1082a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88002589"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成する

Windows Virtual Desktop サービスでは、推奨されるユーザー プロファイル ソリューションとして FSLogix プロファイル コンテナーが提供されます。 ユーザー プロファイル ディスク (UPD) ソリューションの使用は推奨されず、Windows Virtual Desktop の将来のバージョンでは非推奨になります。

この記事では、仮想マシンベースのファイル共有を使用してホスト プールの FSLogix プロファイル コンテナー共有を設定する方法について説明します。 ファイル共有ではなく Azure Files を使用することを強くお勧めします。 FSLogix のその他のドキュメントについては、[FSLogix サイト](https://docs.fslogix.com/)を参照してください。

>[!NOTE]
>Azure 上の FSLogix プロファイル コンテナーのさまざまなストレージ オプションに関する比較資料をお探しの場合は、「[FSLogix プロファイル コンテナー用のストレージ オプション](store-fslogix-profile.md)」を参照してください。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>ファイル共有として機能する新しい仮想マシンを作成する

仮想マシンを作成するときは、ホスト プール仮想マシンと同じ仮想ネットワーク、またはホスト プール仮想マシンに接続されている仮想ネットワークに配置してください。 仮想マシンは複数の方法で作成できます。

- [Azure ギャラリー イメージから仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [マネージド イメージから仮想マシンを作成する](../virtual-machines/windows/create-vm-generalized-managed.md)
- [アンマネージド イメージから仮想マシンを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

仮想マシンの作成後に、次の手順を実行してドメインに参加させます。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)します。
2. 仮想マシン上で、 **[コントロール パネル]** を起動し、 **[システム]** を選択します。
3. **[コンピューター名]** を選択し、 **[設定の変更]** を選択してから **[変更]** を選択します
4. **[ドメイン]** を選択し、仮想ネットワーク上の Active Directory ドメインを入力します。
5. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>ユーザー プロファイルのファイル共有として機能する仮想マシンを準備する

ユーザー プロファイルのファイル共有として機能する仮想マシンを準備する方法についての一般的な手順を次に示します。

1. Windows Virtual Desktop の Active Directory ユーザーを [Active Directory セキュリティ グループ](/windows/security/identity-protection/access-control/active-directory-security-groups/)に追加します。 このセキュリティ グループは、先ほど作成したファイル共有仮想マシンに対して、Windows Virtual Desktop ユーザーを認証するために使用されます。
2. [ファイル共有仮想マシンに接続します](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
3. ファイル共有仮想マシン上で、プロファイル共有として使用するフォルダーを **C ドライブ** に作成します。
4. 新しいフォルダーを右クリックし、 **[プロパティ]** 、 **[共有]** 、 **[詳細な共有]** を選択します。
5. **[このフォルダーを共有する]** を選択し、 **[アクセス許可]** を選択してから、 **[追加]** を選択します。
6. Windows Virtual Desktop ユーザーを追加したセキュリティ グループを検索し、そのグループに **フル コントロール** があることを確認します。
7. セキュリティ グループを追加した後、フォルダーを右クリックして **[プロパティ]** を選択し、 **[共有]** を選択して、後で使用するために **[ネットワーク パス]** をコピーしておきます。

アクセス許可について詳しくは、[FSLogix のドキュメント](/fslogix/fslogix-storage-config-ht/)をご覧ください。

## <a name="configure-the-fslogix-profile-container"></a>FSLogix プロファイル コンテナーを構成する

FSLogix ソフトウェアで仮想マシンを構成するには、ホスト プールに登録される各マシンで、以下を実行します。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)します。
2. インターネット ブラウザーを起動し、[このリンク](https://go.microsoft.com/fwlink/?linkid=2084562)に移動して FSLogix エージェントをダウンロードします。
3. .zip ファイル内の \\\\Win32\\Release または \\\\X64\\Release のいずれかに移動し、**FSLogixAppsSetup** を実行して FSLogix エージェントをインストールします。  FSLogix のインストール方法の詳細については、「[Download and install FSLogix (FSLogix のダウンロードとインストール)](/fslogix/install-ht/)」を参照してください。
4. **Program Files** > **FSLogix** > **Apps** に移動して、インストールされているエージェントを確認します。
5. スタート メニューから、**RegEdit** を管理者として実行します。 **Computer\\HKEY_LOCAL_MACHINE\\software\\FSLogix** に移動します。
6. **Profiles** という名前のキーを作成します。
7. Profiles キーに以下の値を作成します。

| 名前                | Type               | データ/値                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | 複数行文字列値 | "ファイル共有のネットワーク パス"     |

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合は、[Just-In-Time VM アクセス](../security-center/security-center-just-in-time.md)を使用することをお勧めします。
