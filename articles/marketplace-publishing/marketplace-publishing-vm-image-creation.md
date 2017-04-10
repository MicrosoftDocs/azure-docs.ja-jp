---
title: "Azure Marketplace 向けの仮想マシン イメージの作成 | Microsoft Docs"
description: "顧客が購入できるように Azure Marketplace 向けの仮想マシン イメージを作成する方法について詳しく説明します。"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5c937b8e-e28d-4007-9fef-624046bca2ae
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 62e9f34216f25bc1feab3bc76f78a9c1ab07ab9c
ms.lasthandoff: 03/27/2017


---
# <a name="guide-to-create-a-virtual-machine-image-for-the-azure-marketplace"></a>Azure Marketplace 向け仮想マシン イメージ作成ガイド
この記事 ( **手順 2**) では、Azure Marketplace にデプロイする仮想ハード ディスク (VHD) の準備について説明します。 VHD は SKU の基礎です。 プロセスは、Linux ベースの SKU または Windows ベースの SKU のどちらを提供するかによって異なります。 この記事では、両方のシナリオについて説明します。 このプロセスは、[アカウントの作成および登録][link-acct-creation]と並行して実行できます。

## <a name="1-define-offers-and-skus"></a>1.プランと SKU の定義
このセクションでは、プランおよびそれと関連付けられている SKU の定義について説明します。

プランは、そのプランに属するすべての SKU の "親" です。 複数のプランを定義できます。 プランをどのように構成するかは、それぞれの状況によって異なります。 プランをステージングにプッシュすると、そのすべての SKU も一緒にプッシュされます。 URL に表示されるため、SKU の ID を慎重に検討します。

* Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}
* Azure プレビュー ポータル: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}  

SKU は、VM イメージの取引名です。 VM イメージには、1 個のオペレーティング システム ディスクと 0 個以上のデータ ディスクが含まれます。 これは、仮想マシンに欠かせない完全なストレージ プロファイルです。 ディスクごとに 1 つの VHD が必要です。 空のデータ ディスクにも VHD を作成する必要があります。

どのオペレーティング システムを使っているかにかかわらず、SKU に必要な最小数のデータ ディスクのみを追加します。 顧客はデプロイメント時にイメージの一部であるディスクを削除できませんが、必要に応じて、デプロイメント中またはデプロイメント後にディスクを追加できます。

> [!IMPORTANT]
> **新しいイメージ バージョンでディスク数を変更しないでください。** イメージでデータ ディスクを再構成する必要がある場合は、新しい SKU を定義します。 新しいイメージ バージョンを異なるディスク数で発行すると、自動スケール、ARM テンプレートを使用したソリューションの自動デプロイメントなどのシナリオで、その新しいイメージ バージョンに基づく新しいデプロイメントが破損する可能性があります。
>
>

### <a name="11-add-an-offer"></a>1.1 プランの追加
1. 販売者アカウントを使用して、[発行ポータル][link-pubportal]にサインインします。
2. 発行ポータルの **[Virtual Machines]** タブを選択します。 フィールドにプラン名を入力します。 通常、プラン名は Azure Marketplace で販売する計画の製品またはサービスの名前です。
3. **[作成]**を選択します。

### <a name="12-define-a-sku"></a>1.2 SKU の定義
プランを追加した後、SKU を定義および特定する必要があります。 複数のプランを作成し、各プランに複数の SKU を定義できます。 プランをステージングにプッシュすると、そのすべての SKU も一緒にプッシュされます。

1. **SKU を追加します。** SKU には ID が必要です。これは、URL で使用します。 ID は、発行プロファイル内で一意である必要がありますが、他の発行元との ID の競合が発生するリスクはありません。

   > [!NOTE]
   > プランと SKU の ID は、Marketplace でプランの URL に表示されます。
   >
   >
2. **SKU の概要説明を追加します。** 概要説明は顧客に表示されるので、読みやすくする必要があります。 この情報は、"ステージングにプッシュ" フェーズまでロックする必要はありません。 それまでは、自由に編集できます。
3. Windows ベースの SKU を使用する場合、表示されたリンクに従って、Windows Server の承認されたバージョンを取得します。

## <a name="2-create-an-azure-compatible-vhd-linux-based"></a>2.Azure と互換性のある VHD の作成 (Linux ベース)
このセクションでは、Azure Marketplace 用 Linux ベースの VM イメージを作成するためのベスト プラクティスについて説明します。 詳細な手順については、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

## <a name="3-create-an-azure-compatible-vhd-windows-based"></a>手順 3.Azure と互換性のある VHD の作成 (Windows ベース)
このセクションでは、Azure Marketplace 用の Windows Server に基づいて SKU を作成するためのステップについて説明します。

### <a name="31-ensure-that-you-are-using-the-correct-base-vhds"></a>3.1 正しい基本 VHD を使用していることの確認
VM イメージのオペレーティング システム VHD は、Azure 承認の基本イメージに基づいている必要があります (Windows Server、SQL Server など)。

始めに、[Microsoft Azure Portal][link-azure-portal] にある次のイメージのいずれかから VM を作成します。

* Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2]、[2012 Datacenter][link-datactr-2012]、[2008 R2 SP1][link-datactr-2008-r2])
* SQL Server 2014 ([Enterprise][link-sql-2014-ent]、[Standard][link-sql-2014-std]、[Web][link-sql-2014-web])
* SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent]、[Standard][link-sql-2012-std]、[Web][link-sql-2012-web])

これらのリンクは、SKU ページの発行ポータルにもあります。

> [!TIP]
> 現行の Azure ポータルまたは PowerShell を使用している場合は、2014 年 9 月 8 日以降に発行された Windows Server イメージが承認されます。
>
>

### <a name="32-create-your-windows-based-vm"></a>3.2 Windows ベースの VM の作成
Microsoft Azure ポータルから承認された基本イメージに基づいて、簡単な数ステップ実行して VM を作成できます。 大まかな手順を以下に示します。

1. 基本イメージ ページで、**[仮想マシンの作成]** を選択して新しい [Microsoft Azure Portal][link-azure-portal] に移動します。

    ![図][img-acom-1]
2. 使用する Azure サブスクリプションの Microsoft アカウントとパスワードでポータルにサインインします。
3. プロンプトに従って、選択した基本イメージを使って VM を作成します。 ホスト名 (コンピューターの名前)、ユーザー名 (管理者として登録された)、VM のパスワードを入力する必要があります。

    ![図][img-portal-vm-create]
4. デプロイする VM のサイズを選択します。

    a.    VHD をオンプレミスで開発する場合は、サイズの選択は必要ありません。 小さいサイズの VM を使用することを検討してください。

    b.    Azure でイメージを開発する場合は、選択したイメージに推奨される VM サイズを使用することを検討してください。

    c.    価格情報については、ポータルに表示される**推奨される価格レベル**のセレクターを参照してください。 (この場合、発行元はマイクロソフト。 )

    ![図][img-portal-vm-size]
5. プロパティを設定します。

    a.    迅速にデプロイするには、**[オプションの構成]** と **[リソース グループ]** のプロパティの既定値をそのまま使用します。

    b.    必要に応じて、**[ストレージ アカウント]** でオペレーティング システム VHD を格納するストレージのアカウントを選択できます。

    c.    必要に応じて、**[リソース グループ]** で VM を配置する論理グループを選択できます。
6. デプロイメントの **[場所]** を選択します。

    a.    VHD をオンプレミスで開発する場合は、後でイメージを Azure にアップロードするため、場所を指定する必要はありません。

    b.    Azure でイメージで開発する場合は、最初から US ベースの Microsoft Azure リージョンのいずれかを使用することを検討してください。 これにより認定でイメージを送信した際に、お客様に代わりマイクロソフトが実行することで VHD コピー プロセスが高速化されます。

    ![図][img-portal-vm-location]
7. **[作成]**をクリックします。 VM がデプロイを開始します。 数分でデプロイメントが完了し、SKU のイメージの作成を開始できます。

### <a name="33-develop-your-vhd-in-the-cloud"></a>3.3 クラウドでの VHD の開発
リモート デスクトップ プロトコル (RDP) を使用してクラウドで VHD を開発することを強くお勧めします。 プロビジョニングではユーザー名とパスワードを指定して RDP に接続します。

> [!IMPORTANT]
> オンプレミスで VHD を開発する場合は (推奨されません)、「 [Creating a virtual machine image on-premises (オンプレミスでの仮想マシン イメージの作成)](marketplace-publishing-vm-image-creation-on-premise.md)」を参照してください。 クラウドで開発する場合は、VHD をダウンロードする必要はありません。
>
>

**[Microsoft Azure Portal][link-azure-portal] を使用した RDP 経由の接続**

1. **[参照]** > **[VM]** の順に選択します。
2. [仮想マシン] ブレードが開きます。 接続する VM が実行していることを確認し、デプロイ済み VM の一覧からその VM を選択します。
3. ブレードが開き、選択した VM の詳細が表示されます。 上部で、 **[接続]**をクリックします。
4. プロビジョニング時に指定したユーザー名とパスワードを入力するように求められます。

**PowerShell を使用した RDP 経由の接続**

リモート デスクトップ ファイルをローカル マシンにダウンロードするには、[Get-AzureRemoteDesktopFile コマンドレット][link-technet-2]を使用します。 このコマンドレットを使用するには、サービス名と VM 名が必要です。 [Microsoft Azure Portal][link-azure-portal] から VM を作成した場合は、この情報を [VM プロパティ] で探すことができます。

1. Microsoft Azure ポータルで **[参照]** > **[VM]** の順に選択します。
2. [仮想マシン] ブレードが開きます。 デプロイした VM を選択します。
3. ブレードが開き、選択した VM の詳細が表示されます。
4. **[プロパティ]**をクリックします。
5. ドメイン名の最初の部分はサービス名です。 ホスト名は、VM の名前です。

    ![図][img-portal-vm-rdp]
6. 作成した VM の RDP ファイルを管理者のローカル デスクトップにダウンロードするコマンドレットは次のとおりです。

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

RDP について詳しくは、MSDN の記事「 [RDP または SSH を使用した Azure 仮想マシンへの接続](http://msdn.microsoft.com/library/azure/dn535788.aspx)」をご覧ください。

**VM の構成と SKU の作成**

オペレーティング システム VHD がダウンロードされた後、Hyper-V を使って、SKU の作成を開始するように VM を構成します。 詳細な手順については、TechNet リンク「 [Hyper-V の役割のインストールと仮想マシンの構成](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

### <a name="34-choose-the-correct-vhd-size"></a>3.4 適切な VHD サイズの選択
VM イメージ内の Windows オペレーティング システム VHD は、128 GB 固定形式 VHD として作成する必要があります。  

物理サイズが 128 GB 未満の場合、VHD が疎になります。 Windows と SQL Server の基本イメージがこれらの要件を既に満たしている場合は、取得された VHD の形式またはサイズを変更しないでください。  

データ ディスクは 1 TB にすることができます。 ディスク サイズを決めるときに、顧客はデプロイメント時にイメージ内の VHD をサイズ変更できないことに注意してください。 データ ディスク VHD は固定形式の VHD として作成する必要があります。 また、疎にする必要もあります。 データ ディスクは空にすることも、データを含むこともできます。

### <a name="35-install-the-latest-windows-patches"></a>3.5 最新の Windows 更新プログラムのインストール
基本イメージには、発行日の時点における最新の更新プログラムが含まれています。 作成したオペレーティング システム VHD を発行する前に、Windows Update が実行されていること、すべての最新の "重要な" セキュリティ更新プログラムがインストールされていることを確認してください。

### <a name="36-perform-additional-configuration-and-schedule-tasks-as-necessary"></a>3.6 必要に応じて追加の構成およびタスクのスケジュールを実行
追加の構成が必要な場合は、デプロイ後に最終的な変更が VM に反映されるように、起動時にスケジュールされたタスクを使用することを検討してください。

* 実行が正常に完了したら、タスクが自動的に削除されるように設定するのがベスト プラクティスです。
* ドライブ C または D は常設の唯一のドライブであるため、構成はこれら以外を使用して設定するようにしてください。 ドライブ C はオペレーティング システム ディスクで、ドライブ D は一時的なローカル ディスクです。

### <a name="37-generalize-the-image"></a>3.7 イメージの汎用化
Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。 つまり、オペレーティング システム VHD を一般化する必要があります。

* Windows では、イメージが "sysprep 済み" で、 **sysprep** コマンドをサポートしない構成を設定しないことが必要です。
* ディレクトリ %windir%\System32\Sysprep から次のコマンドを実行できます。

        sysprep.exe /generalize /oobe /shutdown

  OS を sysprep する方法のガイダンスについては、MSDN の記事「[Windows Server VHD の作成と Azure へのアップロード](../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」の手順を参照してください。

## <a name="4-deploy-a-vm-from-your-vhds"></a>4.VHD からの VM のデプロイ
VHD (汎用化されたオペレーティング システム VHD および 0 個以上のデータ ディスク VHD) を Azure ストレージ アカウントにアップロードした後、これらをユーザー VM イメージとして登録できます。 その後、そのイメージをテストできます。 オペレーティング システム VHD は汎用化されるため、VHD URL を指定して VM を直接デプロイすることはできません。

VM イメージの詳細については、次のブログを参照してください。

* [VM イメージ](https://azure.microsoft.com/blog/vm-image-blog-post/)
* [VM Image PowerShell How To (VM イメージ PowerShell ハウツー)](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
* [Azure のバーチャル マシン イメージについて](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### <a name="set-up-the-necessary-tools-powershell-and-azure-cli"></a>必要なツール、PowerShell、Azure CLI をセットアップする
* [PowerShell をセットアップする方法](/powershell/azureps-cmdlets-docs)
* [Azure CLI をセットアップする方法](../cli-install-nodejs.md)

### <a name="41-create-a-user-vm-image"></a>4.1 ユーザー VM イメージの作成
#### <a name="capture-vm"></a>VM のキャプチャ
API/PowerShell/Azure CLI を使って VM をキャプチャする方法については、次のリンクをご覧ください。

* [API](https://msdn.microsoft.com/library/mt163560.aspx)
* [PowerShell](../virtual-machines/virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="generalize-image"></a>イメージの汎用化
API/PowerShell/Azure CLI を使って VM をキャプチャする方法については、次のリンクをご覧ください。

* [API](https://msdn.microsoft.com/library/mt269439.aspx)
* [PowerShell](../virtual-machines/virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure CLI](../virtual-machines/virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="42-deploy-a-vm-from-a-user-vm-image"></a>4.2 ユーザー VM イメージからの VM のデプロイ
ユーザー VM イメージから VM をデプロイするには、既存の [Azure ポータル](https://manage.windowsazure.com) または PowerShell を使用します。

**現在の Azure ポータルからの VM のデプロイ**

1. **[新規]** > **[Compute]** > **[仮想マシン]** > **[ギャラリーから]** の順に移動します。

    ![図][img-manage-vm-new]
2. **[マイ イメージ]**に移動し、VM のデプロイ元の VM イメージを選択します。

   1. **[マイ イメージ]** ビューにはオペレーティング システム イメージと VM イメージの両方が表示されるため、イメージを選択する際には注意が必要です。
   2. ほとんどの VM イメージに 1 つ以上のディスクが含まれるため、ディスクの数に注目すると、デプロイするイメージの種類を特定する際に役立ちます。 ただし、 **[ディスク数]** が 1 に設定された、オペレーティング システム ディスクが 1 つのみの VM イメージを選択することもできます。

      ![図][img-manage-vm-select]
3. VM 作成ウィザードに従って、VM 名、VM サイズ、場所、ユーザー名、パスワードを指定します。

**PowerShell からの VM のデプロイ**

作成したばかりの汎用化された VM イメージから大規模な VM をデプロイするには、次のコマンドレットを使用します。

    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot

> [!IMPORTANT]
> 「Troubleshooting common issues encountered during VHD creation」(VHD の作成中に発生した一般的な問題をトラブルシューティングする) をご覧ください。
>
>

## <a name="5-obtain-certification-for-your-vm-image"></a>5.VM イメージの証明書の取得
Azure Marketplace 用の VM イメージを準備するための次のステップは、イメージの認定です。

このプロセスには、特別な認定ツールの実行、検証結果の Azure コンテナー (VHD が配置された) へのアップロード、プランの追加、SKU の定義、認定のための VM イメージの送信などがあります。

### <a name="51-download-and-run-the-certification-test-tool-for-azure-certified"></a>5.1 Azure 認定用の認定テスト ツールのダウンロードと実行
認定ツールを、ユーザー VM イメージからプロビジョニングされた実行中の VM で実行して、VM イメージが Microsoft Azure と互換性があることを証明します。 VHD の準備に関するガイダンスと要件を満たしているかどうかを検証します。 ツールの出力の互換性レポートを、認定要求のときに発行ポータルにアップロードする必要があります。

認定ツールは、Windows および Linux VM の両方で使用できます。 Windows ベースの VM には PowerShell から接続し、Linux VM には SSH.Net から接続します。

1. 最初に、[Microsoft ダウンロード サイト][link-msft-download]で認定ツールをダウンロードします。
2. 認定ツールを開き、 **[新規テストの開始]** ボタンをクリックします。
3. **[テスト情報]** 画面で、実行するテストの名前を入力します。
4. Linux VM か Windows VM かを選択します。 選択内容に基づいて、後続のオプションを選択します。

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>**Linux VM イメージへの認定ツールの接続**
1. SSH 認証モード (パスワードまたはキー ファイル) を選択します。
2. パスワード ベースの認証を使う場合は、ドメイン ネーム システム (DNS) 名、ユーザー名、およびパスワードを入力します。
3. キー ファイル認証を使う場合は、DNS 名、ユーザー名、および秘密キーの場所を入力します。

   ![Linux VM イメージのパスワード認証][img-cert-vm-pswd-lnx]

   ![Linux VM イメージのキー ファイル認証][img-cert-vm-key-lnx]

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Windows ベースの VM イメージへの認定ツールの接続**
1. 完全修飾 VM DNS 名 (MyVMName.Cloudapp.net など) を入力します。
2. ユーザー名とパスワードを入力します。

   ![Windows VM イメージのパスワード認証][img-cert-vm-pswd-win]

Linux または Windows ベースの VM イメージに適したオプションを選択した後、 **[接続のテスト]** を選択して、SSH.Net または PowerShell にテスト用の有効な接続が確立されたことを確認します。 接続が確立された後、 **[次へ]** をクリックしてテストを開始します。

テストが完了すると、各テスト要素の結果 (合格/不合格/警告) が届きます。

![Linux VM イメージのテスト ケース][img-cert-vm-test-lnx]

![Windows VM イメージのテスト ケース][img-cert-vm-test-win]

いずれかのテストが失敗した場合、イメージは認定されません。 その場合は、要件を確認し、必要な変更を行います。

自動テストが済むと、アンケート画面で VM イメージへの追加入力を求められます。  質問に回答し、 **[次へ]**をクリックします。

![認定ツールのアンケート][img-cert-vm-questionnaire]

![認定ツールのアンケート][img-cert-vm-questionnaire-2]

アンケートに回答した後は、Linux VM イメージの SSH アクセス情報や、失敗した評価の説明などの他の情報を提供できます。 実行されたテスト ケースのテスト結果とログ ファイルおよびアンケートへの回答をダウンロードできます。 VHD と同じコンテナーに結果を保存します。

![認定テスト結果の保存][img-cert-vm-results]

### <a name="52-get-the-shared-access-signature-uri-for-your-vm-images"></a>5.2 VM イメージの Shared Access Signature の URI の取得
発行プロセスにおいて、SKU として作成した各 VHD にリンクされた Uniform Resource Identifier (URI) を指定します。 マイクロソフトは、認定プロセスでこれらの VHD にアクセスします。 そのため、VHD ごとに Shared Access Signature URI を作成する必要があります。 これは、発行ポータルの **[イメージ]** タブに入力する URI です。

作成した Shared Access Signature URI は次の要件を満たす必要があります。

* VHD の Shared Access Signature URI の作成時に、一覧表示および読み取りアクセス許可が付与されていること。 書き込みまたは削除アクセス権を付与しないでください。
* アクセス許可の期間は、Shared Access Signature URI の作成時から最低 3 週間必要です。
* UTC 時刻を保護するため、現在の日付の前日を選択します。 たとえば、今日が 2014 年 10 月 6 日の場合は、2014 年 10 月 5 を選択します。

Azure Marketplace の VHD を共有する複数の方法で、SAS URL を生成できます。
以下は、3 つの推奨されるツールです。

1.    Azure ストレージ エクスプローラー
2.    Microsoft Storage Explorer
3.    Azure CLI

**Azure Storage Explorer (Windows ユーザーに推奨)**

Azure Storage Explorer を使用して SAS URL を生成するための手順を次に示します。

1. [Azure Storage Explorer 6 プレビュー 3](https://azurestorageexplorer.codeplex.com/) を CodePlex からダウンロードします。 [Azure Storage Explorer 6 プレビュー](https://azurestorageexplorer.codeplex.com/) にアクセスして **[Downloads]** (ダウンロード) をクリックします。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_01.png)

2. [AzureStorageExplorer6Preview3.zip](https://azurestorageexplorer.codeplex.com/downloads/get/891668) をダウンロードし、解凍してからインストールします。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_02.png)

3. インストールした後、アプリケーションを開きます。
4. **[アカウントの追加]**をクリックします。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_03.png)

5. ストレージ アカウント名、ストレージ アカウント キー、およびストレージ エンドポイントのドメインを指定します。 これは、Azure Portal で VHD を保持した Azure サブスクリプションのストレージ アカウントです。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_04.png)

6. Azure Storage Explorer を特定のストレージ アカウントに接続すると、ストレージ アカウント内に含まれているすべての項目が表示されるようになります。 オペレーティング システム ディスクの VHD ファイルを (シナリオによってはデータ ディスクも) コピーしたコンテナーを選択します。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_05.png)

7. BLOB コンテナーを選択すると、Azure Storage エクスプローラーはコンテナー内のファイルの表示を始めます。 送信する必要があるイメージ ファイル (.vhd) を選択します。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_06.png)

8.    コンテナーで .vhd ファイルを選択した後、 **[セキュリティ]** タブをクリックします。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_07.png)

9.    **[BLOB コンテナーのセキュリティ]** ダイアログ ボックスで、**[アクセス レベル]** タブの既定の設定はそのままにして、**[Shared Access Signature]** タブをクリックします。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_08.png)

10.    次の手順に従って、.vhd イメージの Shared Access Signature URI を生成します。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_09.png)

    a. **[アクセス許可開始]**: UTC 時刻を保護するため、現在の日付の前日を選択します。 たとえば、今日が 2014 年 10 月 6 日の場合は、2014 年 10 月 5 を選択します。

    b. **[アクセス許可終了]**: **[アクセス許可開始]** の日付より少なくとも 3 週間後の日付を選択します。

    c. **[許可する操作]**: **[一覧]** および **[読み取り]** アクセス許可を選択します。

    d. .vhd ファイルを正しく選択した場合、**[アクセスする BLOB 名]** の下に拡張子 .vhd の付いたファイルが表示されます。

    e. **[署名の生成]** をクリックします。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[このコンテナーの生成された Shared Access Signature URI]** で、上の図で強調表示されている部分について以下をチェックします。

        - イメージ ファイル名および **".vhd"** が URI に含まれることを確認します。
        - 署名の末尾に **"=rl"** があることを確認します。 これは、読み取りおよび一覧アクセスが正常に提供されたことを示します。
        - 署名の一部に **"sr=c"** があることを確認します。 これは、コンテナー レベルのアクセスがあることを示します。

11.    生成された Shared Access Signature URI が動作することを確認するには、**[ブラウザーでテスト]** をクリックします。 ダウンロード プロセスが開始します。

12.    Shared Access Signature URI をコピーします。 この URI を発行ポータルに貼り付けます。

13.    SKU 内の各 VHD に対して手順 6 ～ 10 を繰り返します。

**Microsoft Azure Storage Explorer (Windows/MAC/Linux)**

Microsoft Azure Storage Explorer を使用して SAS URL を生成するための手順を次に示します。

1.    Microsoft Azure Storage Explorer を [http://storageexplorer.com/](http://storageexplorer.com/) Web サイトからダウンロードします。 [Microsoft Azure Storage Explorer](http://storageexplorer.com/releasenotes.html) にアクセスして、**[Download for Windows]** (Windows 向けダウンロード) をクリックします。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_10.png)

2.    インストールした後、アプリケーションを開きます。

3.    **[アカウントの追加]**をクリックします。

4.    自分のアカウントにサインインして、サブスクリプションに Microsoft Azure Storage Explorer を構成します。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_11.png)

5.    ストレージ アカウントに移動し、コンテナーを選択します。

6.    <seg>
  **[Get Shared Access Signature..]** (Shared Access Signature の取得..) を選択します。</seg> **コンテナー**の右クリックを使用

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_12.png)

7.    [開始時刻]、[有効期限]、および [アクセス許可] を次のように更新します。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_13.png)

    a.    **[開始時刻]:** UTC 時刻を保護するため、現在の日付の前日を選択します。 たとえば、今日が 2014 年 10 月 6 日の場合は、2014 年 10 月 5 を選択します。

    b.    **[有効期限]:** **[開始時刻]** の日付から少なくとも 3 週間後の日付を選択します。

    c.    **[アクセス許可]:** **[一覧]** および **[読み取り]** アクセス許可を選択します。

8.    コンテナーの Shared Access Signature URI のコピー

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_14.png)

    生成された SAS URL はコンテナー レベルの URL で、ここで VHD の名前を追加する必要があります。

    コンテナー レベルの SAS URL の形式: `https://testrg009.blob.core.windows.net/vhds?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    次に示すように SAS URL のコンテナー名の後に VHD 名を挿入します。`https://testrg009.blob.core.windows.net/vhds/<VHD NAME>?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    例:

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_15.png)

    TestRGVM201631920152.vhd は VHD 名です。VHD SAS URL は次になります。`https://testrg009.blob.core.windows.net/vhds/TestRGVM201631920152.vhd?st=2016-04-22T23%3A05%3A00Z&se=2016-04-30T23%3A05%3A00Z&sp=rl&sv=2015-04-05&sr=c&sig=J3twCQZv4L4EurvugRW2klE2l2EFB9XyM6K9FkuVB58%3D`

    - イメージ ファイル名および **".vhd"** が URI に含まれることを確認します。
    - 署名の一部に **"sp=rl"** があることを確認します。 これは、読み取りおよび一覧アクセスが正常に提供されたことを示します。
    - 署名の一部に **"sr=c"** があることを確認します。 これは、コンテナー レベルのアクセスがあることを示します。

9.    生成された Shared Access Signature URI が動作することを確認するために、ブラウザーでテストします。 ダウンロード プロセスが開始します。

10.    Shared Access Signature URI をコピーします。 この URI を発行ポータルに貼り付けます。

11.    SKU 内の各 VHD に対してこれらのステップを繰り返します。

**Azure CLI (非 Windows と継続的インテグレーションに推奨)**

Azure CLI を使用して SAS URL を生成するための手順を次に示します。

1.    Microsoft Azure CLI を[ここ](https://azure.microsoft.com/en-in/documentation/articles/xplat-cli-install/)からダウンロードします。 **[Windows](http://aka.ms/webpi-azure-cli)** 向けと **[MAC OS](http://aka.ms/mac-azure-cli)** 向けのリンクがあります。

2.    ダウンロードしたら、インストールしてください。

3.    次のコードで PowerShell ファイルを作成し、ローカルに保存します。

          $conn="DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<Storage Account Key>"
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl <Permission End Date> -c $conn --start <Permission Start Date>  

    上記の次のパラメーターを更新します。

    a. **`<StorageAccountName>`**: ストレージ アカウント名を指定します。

    b. **`<Storage Account Key>`**: ストレージ アカウント キーを指定します。

    c. **`<Permission Start Date>`**: UTC 時刻を保護するため、現在の日付の前日を選択します。 たとえば、今日が 2016 年 10 月 26 日の場合は、2016 年 10 月 25 日にしてください。

    d. **`<Permission End Date>`**: **開始日**から少なくとも 3 週間後の日付を選択します。 値は **2016 年 11 月 2日** にする必要があります。

    適切なパラメーターに更新した後のコード例を次に示します。

          $conn="DefaultEndpointsProtocol=https;AccountName=st20151;AccountKey=TIQE5QWMKHpT5q2VnF1bb+NUV7NVMY2xmzVx1rdgIVsw7h0pcI5nMM6+DVFO65i4bQevx21dmrflA91r0Vh2Yw=="
          azure storage container list vhds -c $conn
          azure storage container sas create vhds rl 11/02/2016 -c $conn --start 10/25/2016  

4.    "管理者として実行" モードで Powershell エディターを開き、手順 3 のファイルを開きます。

5.    スクリプトを実行すると、コンテナー レベル アクセスのための SAS URL が生成されます。

    SAS 署名の出力は次のようになります。メモ帳に強調表示されている部分をコピーします。

    ![図](media/marketplace-publishing-vm-image-creation/img5.2_16.png)

6.    コンテナー レベルの SAS URL を取得したところで、VHD の名前を追加する必要があります。

    コンテナー レベルの SAS URL #

    `https://st20151.blob.core.windows.net/vhds?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

7.    次に示すように SAS URL のコンテナー名の後に VHD 名を挿入します。`https://st20151.blob.core.windows.net/vhds/<VHDName>?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    例:

    TestRGVM201631920152.vhd は VHD 名です。VHD SAS URL は次になります。

    `https://st20151.blob.core.windows.net/vhds/ TestRGVM201631920152.vhd?st=2016-10-25T07%3A00%3A00Z&se=2016-11-02T07%3A00%3A00Z&sp=rl&sv=2015-12-11&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

    - イメージ ファイル名および ".vhd" が URI に含まれることを確認します。
    -    署名の一部に "sp=rl" があることを確認します。 これは、読み取りおよび一覧アクセスが正常に提供されたことを示します。
    -    署名の一部に "sr=c" があることを確認します。 これは、コンテナー レベルのアクセスがあることを示します。

8.    生成された Shared Access Signature URI が動作することを確認するために、ブラウザーでテストします。 ダウンロード プロセスが開始します。

9.    Shared Access Signature URI をコピーします。 この URI を発行ポータルに貼り付けます。

10.    SKU 内の各 VHD に対してこれらのステップを繰り返します。


### <a name="53-provide-information-about-the-vm-image-and-request-certification-in-the-publishing-portal"></a>5.3 VM イメージに関する情報の指定と発行ポータルでの証明書の要求
プランと SKU を作成した後は、その SKU に関連するイメージの詳細を入力する必要があります。

1. [発行ポータル][link-pubportal]に移動し、販売者アカウントでサインインします。
2. **[VM イメージ]** タブを選択します。
3. ページの上部に一覧表示された ID はプラン ID で、SKU ID ではありません。
4. **[SKU]** セクションでプロパティを入力します。
5. **[オペレーティング システム ファミリ]**でオペレーティング システム VHD に関連付けられたオペレーティング システムの種類をクリックします。
6. **[オペレーティング システム]** ボックスにオペレーティング システムの詳細を入力します。 オペレーティング システム ファミリ、種類、バージョン、更新の形式で記載してください。 たとえば、「Windows Server Datacenter 2014 R2」などと入力します。
7. 最大 6 個の推奨される仮想マシン サイズを選択します。 これらは、イメージの購入およびデプロイ時に Azure ポータルの [価格レベル] ブレードで顧客に表示されるものです。 **これらは単なる推奨サイズです。顧客はイメージに指定されたディスクを収容できる任意の VM サイズを選択できます。**
8. バージョンを入力します。 バージョン フィールドには、製品とその更新プログラムを識別するためのセマンティック バージョンがカプセル化されています。
   * バージョンの形式は X.Y.Z で、X、Y、Z は整数です。
   * 異なる SKU のイメージは、メジャー バージョンおよびマイナー バージョンが異なっていてもかまいません。
   * SKU 内のバージョンはパッチ バージョン (X.Y.Z の Z) が変化する増分変更のみでなければなりません。
9. **[OS VHD URL]** ボックスに、オペレーティング システム VHD に対して作成した Shared Access Signature URI を入力します。
10. この SKU に関連付けられたデータ ディスクがある場合は、デプロイメント時のこのデータ ディスクのマウント先である論理ユニット番号 (LUN) を選択します。
11. **[LUN X VHD URL]** ボックスに、最初のデータ VHD に対して作成した Shared Access Signature URI を入力します。

    ![図](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)


## <a name="common-sas-url-issues--fixes"></a>SAS URL に関する一般的な問題と解決策

|問題|エラー メッセージ|解決策|ドキュメント リンク|
|---|---|---|---|
|イメージのコピーに失敗する - SAS URL の "?" が見つからない|Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri. (指定された SAS URI を使用して BLOB をダウンロードすることができません。)|推奨ツールを使用して SAS URL を更新します|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|イメージのコピーに失敗する - SAS URL の "st" および "se" パラメーターがない|Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri. (指定された SAS URI を使用して BLOB をダウンロードすることができません。)|開始日と終了日を指定して SAS URL を更新します|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|イメージのコピーに失敗する - SAS URL の "sp=rl" がない|Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri (指定された SAS URI を使用して BLOB をダウンロードすることができません)|「読み取り」と「一覧」として設定したアクセス許可を指定して SAS URL を更新します|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|イメージのコピーに失敗する - SAS URL の VHD 名に空白が含まれる|Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob using provided SAS Uri. (指定された SAS URI を使用して BLOB をダウンロードすることができません。)|空白のない状態で SAS URL を更新します|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|
|イメージのコピーに失敗する - SAS URL の承認エラー|Failure: Copying Images. (エラー: イメージのコピー。) Not able to download blob due to authorization error (承認エラーのため BLOB をダウンロードできません)|SAS URL を再生成します|[https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-1/)|


## <a name="next-step"></a>次のステップ
SKU の詳細について入力が完了したら、[Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging]のページに進むことができます。 発行プロセスのそのステップでは、**ステップ 3: ステージングでの VM プランのテスト**の前に必要なマーケティング コンテンツ、価格、その他の情報を提供します。ステップ 3 では、プランを Azure Marketplace にデプロイして一般に公開して購入できるようにする前にさまざまなユース ケース シナリオをテストします。  

## <a name="see-also"></a>関連項目
* [Microsoft Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)

[img-acom-1]:media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]:media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]:media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]:media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]:media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]:media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-manage-vm-new]:media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]:media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]:media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]:media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]:media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]:media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]:marketplace-publishing-push-to-staging.md
[link-github-waagent]:https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]:https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2.md
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[link-msft-download]:http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]:https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]:https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]:https://portal.azure.com
[link-pubportal]:https://publish.windowsazure.com
[link-sql-2014-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]:http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]:http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-technet-1]:https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]:./virtual-machines-linux-agent-user-guide/
[link-openssl]:https://www.openssl.org/
[link-intsvc]:http://www.microsoft.com/download/details.aspx?id=41554
[link-python]:https://www.python.org/

