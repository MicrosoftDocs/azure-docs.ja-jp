---
title: FSLogix プロファイル コンテナー NetApp Windows Virtual Desktop - Azure
description: Windows Virtual Desktop で Azure NetApp Files を使用して FSLogix プロファイル コンテナーを作成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016830"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Azure NetApp Files および AD DS を使用してプロファイル コンテナーを作成する

[Windows Virtual Desktop サービス](overview.md)のユーザー プロファイル ソリューションには、FSLogix プロファイル コンテナーを使用することをお勧めします。 FSLogix プロファイル コンテナーは、Windows Virtual Desktop のような非永続型のリモート コンピューティング環境でプロファイルのローミングを行うことを目的として設計されており、1 つのコンテナーにユーザー プロファイル全体が格納されます。 サインイン時には、このコンテナーが、ローカルでサポートされている仮想ハード ディスク (VHD) と Hyper-V 仮想ハード ディスク (VHDX) を使用してコンピューティング環境に動的に接続されます。 これらの高度なフィルタードライバー技術により、ユーザー プロファイルは即座に利用可能になり、ローカルのユーザー プロファイルとまったく同じようにシステムに表示されます。 FSLogix プロファイル コンテナーの詳細については、「[FSLogix プロファイル コンテナーと Azure のファイル](fslogix-containers-azure-files.md)」を参照してください。

FSLogix プロファイル コンテナーは、使いやすい Azure のネイティブ プラットフォーム サービスである [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) を使用して作成できます。このサービスでは、Windows Virtual Desktop 環境向けに、エンタープライズ級の SMB ボリュームをスピーディーかつ確実にプロビジョニングすることができます。 Azure NetApp Files の詳細については、「[Azure NetApp Files とは](../azure-netapp-files/azure-netapp-files-introduction.md)」を参照してください。

このガイドでは、Windows Virtual Desktop で Azure NetApp Files アカウントを設定したうえで、FSLogix プロファイル コンテナーを作成する方法について説明します。

この記事では、お使いの Windows Virtual Desktop 環境で[ホスト プール](create-host-pools-azure-marketplace.md)を設定し、1 つ以上のテナントにグループ化していることを前提としています。 テナントの設定方法については、[Windows Virtual Desktop でのテナントの作成](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)に関する記事、および [Tech Community のブログ記事](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)を参照してください。

このガイドの手順は、Windows Virtual Desktop ユーザー専用のものです。 Windows Virtual Desktop 以外での Azure NetApp Files の設定方法と FSLogix プロファイル コンテナーの作成方法に関するより一般的なガイダンスについては、[Azure NetApp Files の設定と NFS ボリュームの作成に関するクイックスタート](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)を参照してください。

>[!NOTE]
>この記事では、Azure NetApp Files 共有へのアクセスの保護に関するベスト プラクティスは扱いません。

>[!NOTE]
>Azure 上の FSLogix プロファイル コンテナーのさまざまなストレージ オプションに関する比較資料をお探しの場合は、「[FSLogix プロファイル コンテナー用のストレージ オプション](store-fslogix-profile.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

ホスト プール用の FSLogix プロファイル コンテナーを作成するには、次の手順を済ませておく必要があります。

- Windows Virtual Desktop の設定と構成
- Windows Virtual Desktop のホスト プールのプロビジョニング
- [Azure NetApp Files サブスクリプションの有効化](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Azure NetApp Files アカウントの設定

初めに、Azure NetApp Files アカウントを設定する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。 使用するアカウントに、共同作成者または管理者の権限があることを確認してください。

2. 検索バーの右側にある **Azure Cloud Shell のアイコン** をクリックして、Azure Cloud Shell を開きます。

3. Azure Cloud Shell が開いたら、 **[PowerShell]** を選択します。

4. Azure Cloud Shell を使用するのが初めての場合には、Azure NetApp Files および Windows Virtual Desktop を保持しているのと同じサブスクリプションにストレージ アカウントを作成してください。

   > [!div class="mx-imgBorder"]
   > ![ウィンドウ下部の [ストレージの作成] ボタンが赤色で強調表示されているストレージ アカウント ウィンドウ。](media/create-storage-button.png)

5. Azure Cloud Shell が読み込まれたら、次の 2 つのコマンドレットを実行します。

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. ウィンドウ左側にある **[すべてのサービス]** を選択します。 メニュー上部に表示された検索ボックスに、「**Azure NetApp Files**」と入力します。

   > [!div class="mx-imgBorder"]
   > ![[すべてのサービス] の検索ボックスに「Azure NetApp Files」と入力した状態のスクリーンショット。 検索結果に Azure NetApp Files のリソースが表示されています。](media/azure-netapp-files-search-box.png)


7. 検索結果で **[Azure NetApp Files]** を選択し、 **[作成]** を選択します。

8. **[追加]** ボタンを選びます。
9. **[New NetApp account]\(新しい NetApp アカウント\)** タブが開いたら、次の値を入力します。

    - **[名前]** には、NetApp アカウントの名前を入力します。
    - **[サブスクリプション]** では、ボックスの一覧から、手順 4 で設定したストレージ アカウントのサブスクリプションを選択します。
    - **[リソース グループ]** では、ボックスの一覧から既存のリソース グループを選択するか、 **[新規作成]** を選択して新しいリソース グループを作成します。
    - **[場所]** では、ボックスの一覧から NetApp アカウントのリージョンを選択します。 このリージョンは、セッション ホスト VM と同じリージョンにする必要があります。

   >[!NOTE]
   >現在、Azure NetApp Files は、リージョンをまたいだボリュームのマウントには対応していません。

10. 完了したら、 **[作成]** を選択して NetApp アカウントを作成します。

## <a name="create-a-capacity-pool"></a>容量プールの作成

次に、容量プールを作成します。

1. Azure NetApp Files のメニューを開いて、新しく作成したアカウントを選択します。
2. アカウント メニューで、[ストレージ サービス] の下にある **[容量プール]** を選択します。
3. **[プールの追加]** を選択します。
4. **[New capacity pool]\(新しい容量プール\)** タブが開いたら、次の値を入力します。

    - **[名前]** には、新しい容量プールの名前を入力します。
    - **[サービス レベル]** では、ボックスの一覧から希望する値を選択します。 ほとんどの環境では、 **[Premium]** が推奨されます。
       >[!NOTE]
       >Premium 設定では、Premium サービス レベルで利用できる最低限のスループット (256 MBps) が提供されます。 運用環境では、このスループットの調整が必要になることがあります。 最終的なスループットは、[スループットの制限](../azure-netapp-files/azure-netapp-files-service-levels.md)についての記事で説明している関係に基づいて決まります。
    - **[サイズ (TiB)]** には、ニーズに最も適した容量プールのサイズを入力します。 最小サイズは 4 TiB です。

5. 完了したら、 **[OK]** をクリックします。

## <a name="join-an-active-directory-connection"></a>Active Directory 接続への参加

次に、Active Directory 接続に参加する必要があります。

1. ページ左側にあるメニューで **[Active Directory 接続]** を選択してから、 **[参加]** を選択して、 **[Active Directory に参加します]** ページを開きます。

   > [!div class="mx-imgBorder"]
   > ![Active Directory 接続への参加メニューのスクリーンショット。](media/active-directory-connections-menu.png)

2. **[Active Directory に参加します]** ページで次の値を入力して、接続に参加します。

    - **[プライマリ DNS]** には、環境内にあり、ドメイン名を解決できる DNS サーバーの IP アドレスを入力します。
    - **[ドメイン]** には、完全修飾ドメイン名 (FQDN) を入力します。
    - **[SMB Server (Computer Account) Prefix]\(SMB サーバー (コンピューター アカウント) プレフィックス\)** には、コンピューター アカウント名の先頭に付ける文字列を入力します。
    - **[ユーザー名]** には、ドメイン参加の実行が可能なアクセス許可を持っているアカウントの名前を入力します。
    - **[パスワード]** には、そのアカウントのパスワードを入力します。

## <a name="create-a-new-volume"></a>新しいボリュームの作成

次に、新しいボリュームを作成する必要があります。

1. **[ボリューム]** 、 **[ボリュームの追加]** の順にクリックします。

2. **[ボリュームの作成]** タブが開いたら、次の値を入力します。

    - **[ボリューム名]** には、新しいボリュームの名前を入力します。
    - **[容量プール]** では、ボックスの一覧から、先ほど作成した容量プールを選択します。
    - **[クォータ (GiB)]** には、環境に適したボリューム サイズを入力します。
    - **[仮想ネットワーク]** では、ボックスの一覧から、ドメイン コントローラーに接続されている既存の仮想ネットワークを選択します。
    - **[サブネット]** では **[新規作成]** を選択します。 このサブネットは、Azure NetApp Files に委任されることに留意してください。

3.  **[Next:プロトコル\>\>]** を選択して [プロトコル] タブを開き、ボリュームのアクセス パラメーターを構成します。

## <a name="configure-volume-access-parameters"></a>ボリュームのアクセス パラメーターの構成

ボリュームを作成したので、次はボリュームのアクセス パラメーターを構成します。

1.  プロトコルの種類として **[SMB]** を選択します。
2.  **[Active Directory]** ボックスの一覧にある [構成] で、「[Active Directory 接続への参加](create-fslogix-profile-container.md#join-an-active-directory-connection)」で接続したものと同じディレクトリを選択します。 Active Directory は、サブスクリプションごとに 1 つまでに制限されていることに注意してください。
3.  **[共有名]** テキスト ボックスに、セッション ホスト プールとそのユーザーが使用する共有の名前を入力します。

4.  ページ下部にある **[確認と作成]** を選択します。 検証ページが開きます。 ボリュームの検証が正常に完了したら、 **[作成]** を選択します。

5.  この時点で、新しいボリュームのデプロイが開始されます。 デプロイが完了すれば、Azure NetApp Files 共有を使用できるようになります。

6.  マウント パスを確認するために、 **[リソースに移動]** を選択し、[概要] タブでこのパスを調べます。

    > [!div class="mx-imgBorder"]
    > ![赤矢印がマウント パスを示している [概要] 画面のスクリーンショット。](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>セッション ホスト仮想マシン (VM) 上での FSLogix の構成

このセクションは、「[ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成する](create-host-pools-user-profile.md)」に基づいています。

1. セッション ホスト VM にリモートで接続している状態で、[FSLogix エージェントの .zip ファイルをダウンロードします](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409)。

2. ダウンロードしたファイルを解凍します。

3. ファイル内で **x64** > **Releases** に移動し、**FSLogixAppsSetup.exe** を実行します。 インストール メニューが開きます。

4.  プロダクト キーがある場合は、それを [プロダクト キー] テキスト ボックスに入力します。

5. **[I agree to the license terms and conditions]\(同意する\)** の隣にあるチェック ボックスをオンにします。

6. **[インストール]** を選択します。

7. **C:\\Program Files\\FSLogix\\Apps** に移動して、インストールされたエージェントを確認します。

8. [スタート] メニューから、管理者として **RegEdit** を実行します。

9. **Computer\\HKEY_LOCAL_MACHINE\\software\\FSLogix** に移動します。

10. **Profiles** という名前のキーを作成します。

11.  名前が **Enabled** で種類が **REG_DWORD** である値を作成し、データの値を **1** に設定します。

12. 名前が **VHDLocations** で種類が **複数の文字列** である値を作成し、データの値を Azure NetApp Files 共有の URI に設定します。

13. サインインする前に既存のローカル プロファイルの問題を回避するために、**DeleteLocalProfileWhenVHDShouldApply** という値を作成し、DWORD 値を 1 にします。

     >[!WARNING]
     >DeleteLocalProfileWhenVHDShouldApply 値を作成するときは注意してください。 FSLogix プロファイル システムで、ユーザーに FSLogix プロファイルが必要であると判断されたときに、ローカル プロファイルが既に存在する場合、プロファイル コンテナーによってローカル プロファイルが完全に削除されます。 その場合、ユーザーは新しい FSLogix プロファイルを使用してサインインすることになります。

## <a name="assign-users-to-session-host"></a>セッション ホストへのユーザーの割り当て

1. 管理者として **PowerShell ISE** を開き、Windows Virtual Desktop にサインインします。

2. 次のコマンドレットを実行します。

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 資格情報を求められたら、Windows Virtual Desktop テナントのテナント作成者、RDS 所有者、または RDS 共同作成者のロールが設定されているユーザーの資格情報を入力します。

4. 次のコマンドレットを実行して、リモート デスクトップ グループにユーザーを割り当てます。

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Azure NetApp File 共有にユーザーがアクセスできることを確認する

1. インターネット ブラウザーを開き、<https://rdweb.wvd.microsoft.com/arm/webclient> にアクセスします。

2. リモート デスクトップ グループに割り当てたユーザーの資格情報を使用してサインインします。

3. ユーザー セッションを確立したら、管理者アカウントを使用して Azure portal にサインインします。

4. **[Azure NetApp Files]** を開き、Azure NetApp Files アカウントを選択してから **[ボリューム]** を選択します。 [ボリューム] メニューが表示されたら、該当するボリュームを選択します。

   > [!div class="mx-imgBorder"]
   > ![Azure portal 内で前に設定した NetApp アカウントを示す、[ボリューム] ボタンが選択された状態のスクリーンショット。](media/netapp-account.png)

5. **[概要]** タブに移動して、FSLogix プロファイル コンテナーにより領域が消費されていることを確認します。

6. ホスト プールに属している任意の VM にリモート デスクトップを使用して直接接続し、**エクスプローラー** を開きます。 次に、**マウント パス** (次の例では、マウント パスは \\\\anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL) に移動します。

   このフォルダー内に、次の例のようなプロファイル VHD (または VHDX) があるはずです。

   > [!div class="mx-imgBorder"]
   > ![マウント パスにあるフォルダーの内容のスクリーンショット。 内部には "Profile_ssbb" という名前の VHD ファイルが 1 つ存在しています。](media/mount-path-folder.png)

## <a name="next-steps"></a>次のステップ

FSLogix プロファイル コンテナーを使用すると、ユーザー プロファイル共有を設定できます。 新しいコンテナーを使用してユーザー プロファイル共有を作成する方法については、「[ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成する](create-host-pools-user-profile.md)」を参照してください。

このほか、Azure Files のファイル共有を作成し、そこに FSLogix プロファイルを格納することもできます。 詳細については、「[ドメイン コントローラーを使用して Azure Files のファイル共有を作成する](create-file-share.md)」を参照してください。