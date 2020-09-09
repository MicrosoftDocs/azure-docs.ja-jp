---
title: Azure 仮想マシンの技術資産を作成する
description: Azure Marketplace 向けの仮想マシン (VM) オファーの技術資産を作成および構成する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/13/2020
ms.openlocfilehash: afc012329d0d9e337dfca93a88615ba7c28f1754
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460373"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Azure 仮想マシンの技術資産を作成する

この記事では、Azure Marketplace 向けの仮想マシン (VM) オファーの技術資産を作成および構成する方法について説明します。 1 つの VM には､オペレーティング システムの仮想ハード ディスク (VHD) と、オプションの関連するデータ ディスク VHD の 2 つのコンポーネントが含まれます｡

* **オペレーティング システム VHD** - オファーでデプロイされるオペレーティング システムとソリューションが含まれています。 VHD の準備プロセスは､それが Linux ベース､Windows ベース､またはカスタム ベースの VM のいずれであるかによって異なります。
* **データ ディスク VHD** - VM 専用の永続ストレージ。 永続的な情報の格納には、オペレーティング システム VHD (C: ドライブなど) を使用しないでください。

VM イメージには、1 個のオペレーティング システム ディスクと、最大 16 個のデータ ディスクが含まれます。 ディスクが空の場合でも、データ ディスクごとに 1 つの VHD を使用してください。

> [!NOTE]
> 使用するオペレーティング システムに関係なく、ソリューションに必要な最小数のデータ ディスクのみを追加します。 お客様はデプロイ時にイメージの一部であるディスクを削除することはできませんが、デプロイ中またはデプロイ後にいつでもディスクを追加できます。

> [!IMPORTANT]
> プラン内のどの VM イメージにも、同じ数のデータ ディスクが存在する必要があります。

## <a name="fundamental-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です。 エンジニアリング チームには､ソリューションのドメインばかりでなく､Microsoft の次の技術に関する知識も必要です。

* [Azure Services](https://azure.microsoft.com/services/) に関する基本知識
* [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
* [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/) と [Azure ストレージ](https://azure.microsoft.com/services/?filter=storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking)に関する実用的な知識
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識
* [JSON](https://www.json.org/) に関する実用的な知識

## <a name="suggested-tools--optional"></a>推奨ツール - オプション

VM と VHD の管理に役立つ次のスクリプト環境のいずれかを利用することを検討してください。

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Azure CLI](https://code.visualstudio.com/)

また、開発環境に次のツールを追加することを検討してください。

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * 拡張機能: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * 拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * 拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

「[Azure の開発者ツール](https://azure.microsoft.com/product-categories/developer-tools/)」ページと [Visual Studio Marketplace](https://marketplace.visualstudio.com/) (Visual Studio を使用する場合) で利用可能なツールを確認してください。

## <a name="create-a-vm-image-using-an-approved-base"></a>承認済みのベースを使用して VM イメージを作成する

> [!NOTE]
> 自社で構築したイメージを使用して仮想マシンの技術資産を作成するには、「[独自のイメージを使用して VM を作成する](#create-a-vm-using-your-own-image)」に進んでください。

このセクションでは、リモート デスクトップ プロトコル (RDP) の使用、VM サイズの選択、最新の Windows 更新プログラムのインストール、VHD イメージの一般化など、承認済みのベースを使用するさまざまな側面について説明します。

以下のセクションでは、主に Windows ベースの VHD に焦点を当てています。 Linux ベースの VHD の作成の詳細については、「[Azure で動作保証済みの Linux ディストリビューション](../../virtual-machines/linux/endorsed-distros.md)」を参照してください。

> [!WARNING]
> Azure を使用して、事前に構成された動作保証済みのオペレーティング システムを含む VM を作成するには、このトピックのガイダンスに従います。 これがお使いのソリューションと互換性がない場合は、承認済みのオペレーティング システムを使用して、オンプレミスの VM を作成して構成することができます。 その場合は、「[Azure にアップロードする Windows VHD または VHDX を準備する](../../virtual-machines/windows/prepare-for-upload-vhd-image.md)」で説明されている方法で、アップロードのための構成と準備を行うことができます。

### <a name="select-an-approved-base"></a>承認済みのベースを選択する

ベースとして Windows オペレーティング システムまたは Linux のいずれかを選択します。

#### <a name="windows"></a>Windows

Windows ベースの VM イメージのオペレーティング システム VHD は、Windows Server またはSQL Server を含む Azure 承認済みベース イメージに基づいている必要があります。 始めに、Azure portal で次のイメージのいずれかから VM を作成します。

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing)、[2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [Windows 10 IoT Enterprise](/windows/iot-core/windows-iot-enterprise)
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise、Standard、Web)
* [SQL Server 2012 SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise、Standard、Web)

> [!NOTE]
> 現行の Azure portal または Azure PowerShell を使用している場合は、2014 年 9 月 8 日以降に発行された Windows Server イメージが承認されます。

#### <a name="linux"></a>Linux

さまざまな承認済みの Linux ディストリビューションが Azure から提供されています。 現在のリストについては、「[Azure で動作保証済みの Linux ディストリビューション](../../virtual-machines/linux/endorsed-distros.md)」を参照してください。

### <a name="create-vm-in-the-azure-portal"></a>Azure portal で VM を作成する

次の手順に従って、[Azure portal](https://ms.portal.azure.com/) でベース VM イメージを作成します。

1. VM オファーの公開に使用する Azure サブスクリプションに関連付けられている Microsoft アカウントで [Azure portal](https://ms.portal.azure.com/) にサインインします。
2. 新しいリソース グループを作成して、 **リソース グループ名**、**サブスクリプション**、**リソース グループの場所**を指定します。 詳細については、[リソースの管理](../../azure-resource-manager/resource-group-portal.md)に関する記事を参照してください。
3. 左側にある **[Virtual Machines]** をクリックして、仮想マシンの詳細ページを表示します。
4. **[+ 追加]** を選択して、 **[Create a virtual machine experience]\(仮想マシン エクスペリエンスの作成\)** を開きます。
5. ドロップダウン リストからイメージを選択するか、 **[すべてのパブリックおよびプライベート イメージを参照する]** をクリックして、使用可能なすべての仮想マシ イメージを検索または参照します。
6. 次の推奨事項に従ってデプロイする VM のサイズを選択します。
    * オンプレミスで VHD を開発する場合、サイズは重要ではありません。 小さいサイズの VM を使用することを検討してください。
    * Azure でイメージを開発する場合は、選択したイメージに推奨される VM サイズを使用することを検討してください。

7. **ディスク** セクションで、**詳細設定］** セクションを展開し、**マネージド ディスクの使用** オプションを **いいえ** に設定します。
8. VM の作成に必要なその他の詳細を指定します。
9. **[確認と作成]** を選択して、選択内容を確認します。 "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

Azure で、指定した仮想マシンのプロビジョニングが開始されます。 左側の **[Virtual Machines]** タブを選択して、進捗状況を追跡できます。 作成後に、状態は **[実行中]** に変わります。

Azure ベースの新しい VHD の作成で問題が発生した場合は、「[VHD 作成における一般的な問題 (FAQ)](common-issues-during-vhd-creation.md)」を参照してください。

### <a name="connect-to-your-azure-vm"></a>Azure VM に接続する

このセクションでは、Azure で作成した VM に接続してサインインする方法について説明します。 正常に接続した後、ホスト サーバーにローカルでログインしているかのように VM を操作できます。

#### <a name="connect-to-a-windows-based-vm"></a>Windows ベースの VM に接続する

Azure でホストされている Windows ベースの VM への接続には、リモート デスクトップ クライアントを使用します。 ほとんどのバージョンの Windows は、リモート デスクトップ プロトコル (RDP) をネイティブでサポートしています。 その他のオペレーティング システムについては、「[リモート デスクトップ クライアント](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)」でクライアントの詳細を確認できます。

次の記事では、組み込みの Windows RDP サポートを使用して VM に接続する方法について詳しく説明しています。[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](../../virtual-machines/windows/connect-logon.md)。

> [!TIP]
> このプロセス中にセキュリティ警告が表示されることがあります。 たとえば、"The .rdp file is from an unknown publisher" (.rdp ファイルの発行元が不明です) や "Your user credentials cannot be verified." (ユーザーの資格情報を確認できません) などの警告です。 これらの警告は無視しても問題ありません。

#### <a name="connect-to-a-linux-based-vm"></a>Linux ベースの VM に接続する

Linux ベースの VM に接続するには、Secure Shell プロトコル (SSH) クライアントが必要です。 次の手順では、無料の [PuTTY](https://www.ssh.com/ssh/putty/) SHH ターミナルを使用します。

1. [Azure ポータル](https://ms.portal.azure.com/)にアクセスします。
2. **[仮想マシン]** を検索して選択します。
3. 接続先の VM を選択します。
4. その VM がまだ実行されていない場合は、それを起動します。
5. VM の名前を選択して、その **[概要]** ページを開きます。
6. お使いの VM のパブリック IP アドレスと DNS 名をメモします (これらの値が設定されていない場合は、[ネットワーク インターフェイスを作成](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface)) する必要があります)。
7. PuTTY アプリケーションを開きます。
8. [PuTTY Configuration]\(PuTTY 構成\) ダイアログで、VM の IP アドレスまたは DNS 名を入力します。

    :::image type="content" source="media/avm-putty.png" alt-text="PuTTY のターミナル設定の画像。[ホスト名 (または IP アドレス)] と [ポート] のボックスが強調表示されています。":::

9. **[Open]\(開く\)** を選択して PuTTY ターミナルを開きます。
10. プロンプトが表示されたら、Linux VM のアカウント名とパスワードを入力します。

接続に問題がある場合は、SSH クライアントのドキュメントを参照してください。 たとえば、「[Chapter 10:Common error messages (第 10 章: 一般的なエラー メッセージ)](https://www.ssh.com/ssh/putty/putty-manuals)」を参照してください。

プロビジョニング済みの Linux VM にデスクトップを追加する方法など、詳細については、「[リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する](../../virtual-machines/linux/use-remote-desktop.md)」を参照してください。

## <a name="create-a-vm-using-your-own-image"></a>独自のイメージを使用して VM を作成する

このセクションでは、ユーザー指定の仮想マシン (VM) イメージを作成してデプロイする方法について説明します。 これを行うには、Azure でデプロイされた仮想ハード ディスク (VHD) からオペレーティング システムとデータ ディスクの VHD イメージを提供します。

> [!NOTE]
> 承認済みのベース イメージを必要に応じて使用するには、「[承認済みのベースを使用して VM イメージを作成する](#create-a-vm-image-using-an-approved-base)」の手順に従います。

1. Azure ストレージ アカウントにイメージをアップロードします。
2. VM イメージをデプロイします。
3. VM イメージをキャプチャします。

### <a name="upload-your-images-to-an-azure-storage-account"></a>イメージを Azure ストレージ アカウントにアップロードする

1. [Azure Portal](https://portal.azure.com/) にログオンします。
2. 一般化されたオペレーティング システム VHD とデータ ディスク VHD を Azure ストレージ アカウントにアップロードします。

### <a name="deploy-your-image"></a>イメージをデプロイする

Azure portal または Azure PowerShell を使用してイメージを作成します。

#### <a name="deploy-using-the-azure-portal"></a>Azure Portal を使用したデプロイ

1. ホーム ページで、 **[リソースの作成]** を選択し、「テンプレートのデプロイ」を検索して、 **[作成]** を選択します。
2. **[Build your own template in the editor]\(エディターで独自のテンプレートを作成する\)** を選択します。

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="[カスタム デプロイ] ページの画像。":::

3. この [JSON テンプレート](../partner-center-portal/azure-vm-image-certification.md)をエディターに貼り付け、 **[保存]** を選択します。
4. 表示されている**カスタム デプロイ**のプロパティ ページのパラメーター値を指定します。

    | パラメーター | 説明 |
    | ------------ | ------------- |
    | ユーザー ストレージ アカウント名 | セル 2 の内容 |
    | ユーザー ストレージ コンテナー名 | 汎用化された VHD が配置されているストレージ アカウント名 |
    | パブリック IP の DNS 名 | パブリック IP DNS 名。 オファーがデプロイされた後、 Azure portal でパブリック IP アドレスの DNS 名を定義します。 |
    | 管理ユーザー名 | 新しい VM の管理者アカウントのユーザー名 |
    | 管理パスワード | 新しい VM の管理者アカウントのパスワード |
    | OS の種類 | VM のオペレーティング システム:Windows または Linux |
    | サブスクリプション ID | 選択したサブスクリプションの識別子 |
    | 場所 | デプロイの地理的な場所 |
    | [VM サイズ] | [Azure VM サイズ](../../virtual-machines/windows/sizes.md)。Standard_A2 など |
    | パブリック IP アドレス名 | パブリック IP アドレスの名前 |
    | VM 名 | 新しい VM の名前 |
    | 仮想ネットワーク名 | VM で使用する仮想ネットワークの名前 |
    | NIC 名 | 仮想ネットワークを実行しているネットワーク インターフェイス カードの名前 |
    | VHD の URL | OS ディスクの VHD の URL を完了する |
    |  |  |

5. これらの値を指定した後、 **[購入]** を選択します。

Azure でデプロイが開始されます。 これにより、指定のストレージ アカウント パスに、指定されたアンマネージド VHD を含む新しい VM が作成されます。 Azure portal で進捗状況を追跡するには、ポータルの左側にある **[Virtual Machines]** を選択します。 VM が作成されると、状態は [開始中] から [実行中] に変わります。

#### <a name="deploy-using-azure-powershell"></a>Azure PowerShell を使用したデプロイ

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>VM イメージのキャプチャ

お使いのアプローチに適合する次の手順を使用してください。

* Azure PowerShell:[Azure VM から非管理対象 VM イメージを作成する方法](../../virtual-machines/windows/capture-image-resource.md)
* Azure CLI:[仮想マシンまたは VHD のイメージを作成する方法](../../virtual-machines/linux/capture-image.md)
* API:[Virtual Machines - キャプチャ](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>仮想マシンを構成する

このセクションでは、Azure VM のサイズ変更、更新、一般化を行う方法について説明します。 これらの手順は、Azure Marketplace にデプロイする VM を準備するために必要です。

### <a name="sizing-the-vhds"></a>VHD のサイズ変更

オペレーティング システム (および必要に応じてその他のサービス) が事前に構成されているいずれかの VM を選択した場合、標準の Azure VM サイズが既に選択されています。 事前構成された OS でソリューションを始めるのが、推奨されるアプローチです。 ただし、OS を手動でインストールする場合は、VM イメージ内のプライマリ VHD のサイズを変更する必要があります。

* Windows の場合、オペレーティング システム VHD は 127 から 128 GB の固定形式の VHD として作成する必要があります。
* Linux の場合、この VHD は 30 から 50 GB の固定形式の VHD として作成する必要があります。

物理サイズが 127 から 128 GB 未満の場合、VHD は拡張可能 (スパース/容量可変) である必要があります。 提供されている Windows と SQL Server のベース イメージはこれらの要件を既に満たしているため、VHD の形式やサイズは変更しないでください。

データ ディスクは 1 TB にすることができます。 サイズを決めるときに、お客様はデプロイ時にイメージ内の VHD のサイズを変更できないことに注意してください。 データ ディスク VHD は固定形式の VHD として作成する必要があります。 また、拡張可能 (スパース/容量可変) にする必要もあります。 データ ディスクは最初に空にすることも、データを含むこともできます。

### <a name="install-the-most-current-updates"></a>最新の更新プログラムをインストールする

オペレーティング システム VM のベース イメージには、発行日までの最新の更新プログラムが含まれている必要があります。 作成したオペレーティング システム VHD を公開する前に、最新のすべてのセキュリティとメンテナンスの修正プログラムを使用して、OS およびすべてのインストール済みサービスを更新するようにしてください。

Windows Server では、**更新プログラムの確認**コマンドを実行します。

Linux ディストリビューションの場合、更新プログラムは通常、コマンド ライン ツールまたはグラフィカル ユーティリティによってダウンロードおよびインストールされます。 たとえば Ubuntu Linux では、OS を更新するための [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) コマンドおよび [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) ツールが提供されています。

### <a name="perform-additional-security-checks"></a>追加のセキュリティ チェックを実行する

Azure Marketplace のソリューション イメージのセキュリティは、高いレベルに維持してください。 次の記事には、セキュリティ構成と手順の役立つチェックリストが記載されています。(「[Azure Marketplace イメージのセキュリティに関する推奨事項](../../security/security-recommendations-azure-marketplace-images.md)」)。 これらの推奨事項の一部は Linux ベースのイメージに固有ですが、ほとんどの事項はすべての VM イメージに当てはまります。

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>カスタム構成とスケジュールされたタスクを実行する

追加の構成が必要な場合は、デプロイ後に最終的な変更が VM に反映されるように、起動時に実行されるスケジュールされたタスクを使用します。 また、次の推奨事項も考慮してください。

* 一度だけ実行されるタスクの場合、タスクは正常に完了した後に自身を削除する必要があります。
* 構成は、C と D 以外のドライブに依存しないようにする必要があります。これは、存在が必ず保証されるドライブがこれら 2 つのみのためです (ドライブ C はオペレーティング システム ディスク、ドライブ D は一時ローカル ディスクです)。

拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](../../virtual-machines/extensions/features-linux.md)」を参照してください。

## <a name="generalize-the-image"></a>イメージを汎用化する

Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。 これを実現するには、オペレーティング システム VHD を一般化する必要があります。これは、インスタンス固有の識別子とソフトウェア ドライバーを VM からすべて削除する操作です。

### <a name="windows"></a>Windows

Windows OS ディスクは [sysprep ツール](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)を使用して汎用化されます。 OS を後で更新または再構成する場合、sysprep を再実行する必要があります。

> [!WARNING]
> 更新プログラムは自動的に実行されることがあるため、sysprep の実行後、デプロイされるまで VM をオフにしてください。 このシャットダウンで、以降の更新プログラムによるオペレーティング システムまたはインストール済みサービスへのインスタンス固有の変更が行われなくなります。 sysprep の実行の詳細については、[VHD の一般化の手順](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)に関するページを参照してください。

### <a name="linux"></a>Linux

次のプロセスでは、Linux VM を一般化して別の VM として再デプロイします。 詳細については、「[仮想マシンまたは VHD のイメージを作成する方法](../../virtual-machines/linux/capture-image.md)」を参照してください。 「キャプチャしたイメージから VM を作成する」セクションまで進んだところでやめることができます。

1. **Azure Linux エージェントを削除します**

    1. SSH クライアントを使って Linux VM に接続します。
    2. SSH ウィンドウで、コマンド `sudo waagent -deprovision+user` を入力します。
    3. 「**Y**」と入力して続行します (前のコマンドに **-force** パラメーターを追加すると、この確認手順を省略できます)。
    d. コマンドが完了したら、「**Exit**」と入力して、SSH クライアントを閉じます。

2. **仮想マシンを停止します**

    1. Azure portal でリソース グループ (RG) を選択し、VM の割り当てを解除します。
    2. これで VHD が一般化されたため、この VHD を使用して新しい VM を作成できます。

## <a name="next-steps"></a>次のステップ

Azure ベースの新しい VHD の作成で問題が発生した場合は、「[VHD 作成における一般的な問題](common-issues-during-vhd-creation.md)」を参照してください。

それ以外の場合:

* [VHD からデプロイされた仮想マシン (VM) のテスト](azure-vm-image-certification.md)に関するページでは、*Certification Test Tool for Azure Certified* ツールを入手する場所や、VM イメージを認定するためにそれを使用する方法など、Azure Marketplace 認定のために VM イメージをテストして送信する方法について説明しています。
