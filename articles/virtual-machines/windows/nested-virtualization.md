---
title: Azure Virtual Machines で入れ子になった仮想化を有効にする方法 | Microsoft Docs
description: Azure Virtual Machines で入れ子になった仮想化を有効にする方法
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 843dfa64cdf0af3ad6cfd3a9f83c16f0ce85fcd0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720215"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Azure VM で入れ子になった仮想化を有効にする方法

入れ子になった仮想化は、複数の Azure 仮想マシン ファミリでサポートされています。 この機能により、開発、テスト、トレーニング、デモなど、各環境のシナリオに柔軟に対応できます。   

この記事では、Azure VM で Hyper-V を有効にして、ゲスト仮想マシンへのインターネット接続を構成する手順について説明します。

## <a name="create-a-nesting-capable-azure-vm"></a>入れ子対応の Azure VM を作成する

新しい Windows Server 2016 Azure VM を作成します。  たとえば、すべての v3 仮想マシンは入れ子になった仮想化をサポートしています。 入れ子をサポートしている仮想マシンのサイズの完全な一覧については、「[Azure コンピューティング ユニット (ACU)](acu.md)」をご覧ください。

必ずゲスト仮想マシンの要求をサポートするのに十分な大きさの VM サイズを選択してください。 この例では、D3_v3 サイズの Azure VM を使用しています。 

Dv3 または Ev3 シリーズの仮想マシンのリージョン別提供状況については、[こちら](https://azure.microsoft.com/regions/services/)をご覧ください。

>[!NOTE]
>
>新しい仮想マシンを作成する手順の詳細については、「[Azure PowerShell モジュールを使用して Windows VM を作成および管理する](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)」を参照してください。
    
## <a name="connect-to-your-azure-vm"></a>Azure VM に接続する

仮想マシンへのリモート デスクトップ接続を作成します。

1. 仮想マシンのプロパティで、 **[接続]** ボタンをクリックします。 リモート デスクトップ プロトコル ファイル (.rdp ファイル) が作成されてダウンロードされます。

2. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、 **[接続]** をクリックします。 Mac では、この[リモート デスクトップ クライアント](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)のような RDP クライアントを Mac App Store から入手する必要があります。

3. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し、 **[OK]** をクリックします。

4. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Azure VM の Hyper-V 機能を有効にする
この設定は手動で構成するか、用意されている PowerShell スクリプトを使って自動で構成できます。

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>選択肢 1: PowerShell スクリプトを使用して、入れ子になった仮想化を構成する
Windows Server 2016 ホストの入れ子になった仮想化を有効にする PowerShell スクリプトは [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested) でご利用いただけます。 スクリプトは、前提条件をチェックし、Azure VM で入れ子になった仮想化を構成します。 構成を完了するには、Azure VM を再起動する必要があります。 このスクリプトは他の環境で動作する可能性はありますが、保証されません。 Azure のブログ記事と、Azure で実行されている入れ子になった仮想化のライブ ビデオ デモをご確認ください [https://aka.ms/AzureNVblog](https://aka.ms/AzureNVblog )

### <a name="option-2-configure-nested-virtualization-manually"></a>選択肢 2: 入れ子になった仮想化を手動で構成する

1. Azure VM で、PowerShell を管理者として開きます。 

2. Hyper-V の機能と管理ツールを有効にします。

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >このコマンドは、Azure VM を再起動します。 再起動プロセスの間、RDP 接続は失われます。
    
3. Azure VM の再起動後、RDP を使用して VM に再接続します。

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>ゲスト仮想マシンのインターネット接続を設定する
ゲスト仮想マシンの新しい仮想ネットワーク アダプターを作成し、インターネット接続が有効になるように NAT ゲートウェイを構成します。

### <a name="create-a-nat-virtual-network-switch"></a>NAT 仮想ネットワーク スイッチを作成する

1. Azure VM で、PowerShell を管理者として開きます。
   
2. 内部スイッチを作成します。

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. スイッチのプロパティを表示し、新しいアダプターの ifIndex を書き留めます。

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >作成したばかりの仮想スイッチの "ifIndex" を書き留めます。
    
4. NAT ゲートウェイの IP アドレスを作成します。
    
ゲートウェイを構成するには、ネットワークに関する情報がいくつか必要です。    
  * IPAddress - NAT ゲートウェイ IP は、仮想ネットワーク サブネットの既定のゲートウェイ アドレスとして使用する IPv4 または IPv6 アドレスを指定します。 一般的な形式は a.b.c.1 ("192.168.0.1" など) です。 末尾は .1 である必要はありませんが、通常は .1 です (プレフィックス長に基づきます)。 一般的には、RFC 1918 プライベート ネットワーク アドレス空間を使用する必要があります。 
  * PrefixLength - サブネット プレフィックス長は、ローカル サブネット サイズ (サブネット マスク) を定義します。 サブネット プレフィックス長は 0 ～ 32 の整数値になります。 0 は、インターネット全体をマップし、32 は、マップされた IP を 1 つのみ許可します。 一般的な値は 24 ～ 12 で、NAT に接続する必要がある IP 数によって異なります。 通常、PrefixLength は 24 です。これは 255.255.255.0 のサブネット マスクです。
  * InterfaceIndex - **ifIndex** は、前の手順で作成した仮想スイッチのインターフェイス インデックスです。 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>NAT ネットワークを作成する

ゲートウェイを構成するには、ネットワークと NAT ゲートウェイに関する情報を指定する必要があります。
  * Name - NAT ネットワークの名前です。 
  * InternalIPInterfaceAddressPrefix - NAT サブネット プレフィックスは、上記の NAT ゲートウェイ IP プレフィックスと NAT サブネット プレフィックス長の両方を記述します。 一般的な形式は a.b.c.0/NAT サブネット プレフィックス長になります。 

PowerShell で、新しい NAT ネットワークを作成します。
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>ゲスト仮想マシンを作成する

>[!IMPORTANT] 
>
>Azure ゲスト エージェントは入れ子になった VM ではサポートされておらず、ホスト VM と入れ子になった VM の両方で問題が発生する可能性があります。 入れ子になった VM には Azure エージェントをインストールしないでください。また、既に Azure ゲスト エージェントがインストールされている、入れ子になった VM を作成するためのイメージを使用しないでください。

1. Hyper-V マネージャーを開いて、新しい仮想マシンを作成します。 作成した新しい内部ネットワークを使用するように仮想マシンを構成します。
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. ゲスト仮想マシンでオペレーティング システムをインストールします。
    
    >[!NOTE] 
    >
    >VM にインストールするオペレーティング システムのインストール メディアが必要です。 ここでは、Windows 10 Enterprise を使用しています。

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>IP アドレスをゲスト仮想マシンに割り当てる

IP アドレスをゲスト仮想マシンに割り当てるには、ゲスト仮想マシンで静的 IP アドレスを手動で設定するか、IP アドレスが動的に割り当てられるように Azure VM で DHCP を構成します。

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>選択肢 1: IP アドレスがゲスト仮想マシンに動的に割り当てられるように DHCP を構成する
動的アドレス割り当てのためにホスト仮想マシンで DHCP を構成するには、次の手順に従ってください。

#### <a name="install-dchp-server-on-the-azure-vm"></a>Azure VM で DCHP サーバーをインストールする

1. サーバー マネージャーを開きます。 ダッシュボードで **[役割と機能の追加]** をクリックします。 役割と機能の追加ウィザードが表示されます。
  
2. ウィザードで、[サーバーの役割] ページが表示されるまで **[次へ]** をクリックします。
  
3. クリックして **[DHCP サーバー]** チェック ボックスをオンにし、 **[機能の追加]** をクリックして、ウィザードが完了するまで **[次へ]** をクリックします。
  
4. **[インストール]** をクリックします。

#### <a name="configure-a-new-dhcp-scope"></a>新しい DHCP スコープを構成する

1. DHCP マネージャーを開きます。
  
2. ナビゲーション ウィンドウで、サーバー名を展開し、 **[IPv4]** を右クリックして、 **[新しいスコープ]** をクリックします。 新しいスコープ ウィザードが表示されたら、 **[次へ]** をクリックします。
  
3. スコープの名前と説明を入力し、 **[次へ]** をクリックします。
  
4. DHCP サーバーの IP 範囲を定義します (192.168.0.100 ～ 192.168.0.200 など)。
  
5. [デフォルト ゲートウェイ] ページが表示されるまで、 **[次へ]** をクリックします。 既定のゲートウェイとして前に作成した IP アドレス (192.168.0.1 など) を入力し、 **[追加]** をクリックします。
  
6. ウィザードが完了するまで、すべて既定値のままにして **[次へ]** をクリックし、 **[完了]** をクリックします。
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>選択肢 2: ゲスト仮想マシンに静的 IP アドレスを手動で設定する
IP アドレスがゲスト仮想マシンに動的に割り当てられるように DHCP を構成しなかった場合は、次の手順を実行して静的 IP アドレスを設定します。

1. Azure VM で、PowerShell を管理者として開きます。

2. ゲスト仮想マシンを右クリックし、[接続] をクリックします。

3. ゲスト仮想マシンにサインインします。

4. ゲスト仮想マシンで、ネットワークと共有センターを開きます。

5. 前のセクションで作成した NAT ネットワークの範囲内のアドレスに対して、ネットワーク アダプターを構成します。

この例では、192.168.0.0/24 の範囲のアドレスを使用します。

## <a name="test-connectivity-in-guest-virtual-machine"></a>ゲスト仮想マシンで接続をテストする

ゲスト仮想マシンで、ブラウザーを開いて Web ページに移動します。
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>ゲスト仮想マシンのイントラネット接続を設定する

ゲスト VM と Azure VM との間で透過的接続を有効にする手順については、[こちらのドキュメント](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network)を参照してください。
