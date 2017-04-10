---
title: "FreeBSD VM イメージの作成とアップロード | Microsoft Docs"
description: "FreeBSD オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成およびアップロードして、Azure 仮想マシンを作成する方法について説明します"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 6b0d90724f4b7cc065eb186b06816ba818cdaa18
ms.lasthandoff: 03/27/2017


---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>FreeBSD VHD の作成と Azure へのアップロード
この記事では、FreeBSD オペレーティング システムを格納した仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。 アップロードした VHD を独自のイメージとして使用し、Azure の仮想マシン (VM) を作成することができます。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用した VHD のアップロード方法については、[こちら](../../virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

## <a name="prerequisites"></a>前提条件
この記事では、次の項目があることを前提としています。

* **Azure サブスクリプション**- アカウントをお持ちでない場合でも、数分でアカウントを作成できます。 MSDN サブスクリプションをお持ちの場合は、「 [Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」をご覧ください。 それ以外の場合は、 [無料試用版のアカウントの作成](https://azure.microsoft.com/pricing/free-trial/)方法に関するページをご覧ください。  
* **Azure PowerShell ツール**- Azure PowerShell モジュールをインストールし、Azure サブスクリプションを使用するように構成しておきます。 このモジュールをダウンロードするには、 [Azure のダウンロード ページ](https://azure.microsoft.com/downloads/)にアクセスしてください。 モジュールをインストールして構成する方法についてのチュートリアルはこちらで入手できます。 [Azure のダウンロード](https://azure.microsoft.com/downloads/) のコマンドレットを使用して VHD をアップロードします。
* **.vhd ファイルにインストールされている FreeBSD オペレーティング システム**- サポートされている FreeBSD オペレーティング システムを仮想ハード ディスクにインストールしておきます。 .vhd ファイルを作成するツールはいくつかあります。 たとえば Hyper-V などの仮想化ソリューションを使用して .vhd ファイルを作成し、オペレーティング システムをインストールすることができます。 Hyper-V をインストールして使用する手順については、「 [Hyper-V をインストールして仮想マシンを作成する](http://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

> [!NOTE]
> 新しい VHDX 形式は、Azure ではサポートされていません。 Hyper-V マネージャーまたは [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx)コマンドレットを使用して、ディスクを VHD 形式に変換できます。 また、 [Hyper-V で FreeBSD を使用する方法についてのチュートリアルが MSDN](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)に存在します。
>
>

このタスクの手順は次のとおり 5 つあります。

## <a name="step-1-prepare-the-image-for-upload"></a>手順 1. アップロードするイメージを準備する
FreeBSD オペレーティング システムがインストールされた仮想マシンで、次の手順を実行します。

1. DHCP を有効にします。

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. SSH を有効にします。

    SSH は、ディスクからインストールすると、既定で有効になります。 何らかの理由で有効になっていない場合や、FreeBSD VHD を直接使用する場合は、次のように入力してください。

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart
3. シリアル コンソールをセットアップします。

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. sudo をインストールします。

    Azure では "root" アカウントは無効化されます。 つまり、特権のないユーザーから sudo を利用し、昇格した特権でコマンドを実行する必要があります。

        # pkg install sudo
   ;
5. Azure エージェントの前提条件を満たします。

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Azure エージェントをインストールします。

    Azure エージェントの最新版は常に [GitHub](https://github.com/Azure/WALinuxAgent/releases)にあります。 バージョン 2.0.10 以降は FreeBSD 10 および 10.1 を正式にサポートし、バージョン 2.1.4 は FreeBSD 10.2 以降のリリースを正式にサポートします。

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0 については、例として 2.0.16 を使用します。

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1 については、例として 2.1.4 を使用します。

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Azure エージェントがインストール後、実行されていることを確認するようお勧めします。
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log
7. システムのプロビジョニングを解除します。

    システムのプロビジョニングを解除してクリーンアップし、再プロビジョニングに適した状態にします。 以下のコマンドは前回プロビジョニングされたユーザー アカウントおよび関連付けられたデータも削除します。

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    これで VM をシャットダウンできます。

## <a name="step-2-create-a-storage-account-in-azure"></a>手順 2. Azure にストレージ アカウントを作成する
仮想マシンの作成に使用する .vhd ファイルをアップロードするには、Azure のストレージ アカウントが必要です。 ストレージ アカウントは、Azure 旧ポータルを使用して作成できます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. コマンド バーで、 **[新規]**を選択します。
3. **[Data Services]** > **[ストレージ]** > **[簡易作成]** の順に選択します。

    ![ストレージ アカウントの簡易作成](./media/freebsd-create-upload-vhd/Storage-quick-create.png)
4. 次のようにフィールドを指定します。

   * ストレージ アカウントの URL で使用するサブドメイン名を **[URL]** フィールドに入力します。 文字数は 3 ～ 24 文字で、数字とアルファベット小文字を使用できます。 この名前は、対応するサブスクリプションの Azure Blob Storage リソース、Azure Queue Storage リソース、Azure Table Storage リソースのアドレス指定に使用される URL のホスト名になります。
   * **[場所/アフィニティ グループ]** ボックスの一覧から、ストレージ アカウントの**場所またはアフィニティ グループ**を選択します。 アフィニティ グループを使用すると、クラウド サービスとストレージを同じデータ センターに配置できます。
   * **[レプリケーション]** フィールドで、ストレージ アカウントに **geo 冗長**レプリケーションを使用するかどうかを決めます。 geo レプリケーションは既定で有効です。 このオプションでは、ユーザーのコスト負担なしで、データが 2 次拠点にコピーされるため、1 次拠点で大規模な障害が発生した場合に、2 次拠点にストレージをフェールオーバーできます。 2 次拠点は自動的に割り当てられ、変更することはできません。 法律上の要件または組織のポリシー上、クラウド方式のストレージの場所を厳格に管理する必要がある場合は、geo レプリケーションを無効にすることができます。 ただし、後で Geo レプリケーションを有効に戻すと、既存データを 2 次拠点にコピーするためのデータ転送料金が 1 回だけ発生することに注意してください。 Geo レプリケーションなしのストレージ サービスも割引価格で提供されています。 ストレージ アカウントの geo レプリケーションを管理する方法の詳細については、「[Azure Storage のレプリケーション](../../../storage/storage-redundancy.md)」をご覧ください。

     ![ストレージ アカウントの詳細の入力](./media/freebsd-create-upload-vhd/Storage-create-account.png)
5. **[ストレージ アカウントの作成]**を選択します。 作成したアカウントが **[ストレージ]**に表示されます。

    ![ストレージ アカウントの作成に成功](./media/freebsd-create-upload-vhd/Storagenewaccount.png)
6. 次に、アップロードした .vhd ファイルのコンテナーを作成します。 ストレージ アカウントの名前を選択し、 **[コンテナー]**を選択します。

    ![ストレージ アカウントの詳細](./media/freebsd-create-upload-vhd/storageaccount_detail.png)
7. **[コンテナーを作成する]**を選択します。

    ![ストレージ アカウントの詳細](./media/freebsd-create-upload-vhd/storageaccount_container.png)
8. **[名前]** フィールドで、コンテナーの名前を入力します。 **[アクセス]** ボックスの一覧から、必要なアクセス ポリシーの種類を選択します。

    ![コンテナー名](./media/freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > 既定では、コンテナーはプライベートであり、アカウント所有者のみがアクセスできます。 コンテナー内の BLOB にはパブリック読み取りアクセスを許可し、コンテナーのプロパティやメタデータにはアクセスを許可しない場合は、 **[パブリック BLOB]** オプションを使用します。 コンテナーと BLOB に完全パブリック読み取りアクセスを許可するには、 **[パブリック コンテナー]** オプションを使用します。
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>手順 3. Azure への接続を準備する
.vhd ファイルをアップロードには、コンピューターと Azure のサブスクリプションの間にセキュリティで保護された接続を確立する必要があります。 接続の確立には、Azure Active Directory (Azure AD) 方式または証明書方式を使用できます。

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Azure AD 方式を使用して .vhd ファイルをアップロードするには
1. Azure PowerShell コンソールを開きます。
2. 次のコマンドを入力します。  
    `Add-AzureAccount`

    このコマンドによりサインイン ウィンドウが開きます。職場や学校のアカウントを使用してサインインしてください。

    ![PowerShell ウィンドウ](./media/freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure により資格情報が認証および保存され、 ウィンドウが閉じます。

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>証明書方式を使用して .vhd ファイルをアップロードするには
1. Azure PowerShell コンソールを開きます。
2. 次のコマンドを入力します:  `Get-AzurePublishSettingsFile`
3. ブラウザー ウィンドウが開き、.publishsettings ファイルをダウンロードするよう求められます。 このファイルには、Azure サブスクリプションについての情報と証明書が含まれています。

    ![ブラウザーのダウンロード ページ](./media/freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. .publishsettings ファイルを保存します。
5. 「 `Import-AzurePublishSettingsFile <PathToFile>`」と入力します。ここで、`<PathToFile>` は .publishsettings ファイルへの完全なパスです。

   詳細については、「 [Azure コマンドレットの概要](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)」を参照してください。

   PowerShell のインストールと構成の詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="step-4-upload-the-vhd-file"></a>ステップ 4: .vhd ファイルをアップロードする
.vhd ファイルをアップロードするときは、Blob Storage 内であればどこにでも .vhd ファイルを置くことができます。 以下、ファイルをアップロードするときに使用するいくつかの用語について補足します。

* **BlobStorageURL** は、ステップ 2. で作成したストレージ アカウントの URL です。
* **YourImagesFolder** は、イメージを格納する Blob Storage 内のコンテナーです。
* **VHDName** は、仮想ハード ディスクを識別するために Azure クラシック ポータルに表示されるラベルです。
* **PathToVHDFile** は、.vhd ファイルの完全なパスとファイル名です。

前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>ステップ 5: アップロードした .vhd ファイルで VM を作成する
.vhd ファイルをアップロードしたら、サブスクリプションに関連付けられたカスタム イメージの一覧にイメージとして追加し、そのカスタム イメージから仮想マシンを作成できます。

1. 前の手順で使用した Azure PowerShell ウィンドウで、次のように入力します。

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > OS の種類としては Linux を使用します。 現在のバージョンの Azure PowerShell でパラメーターとして指定できるのは "Linux" と "Windows" のみです。
   >
   >
2. ここまでの手順を完了すると、Azure クラシック ポータルで **[イメージ]** タブをクリックしたときに、新しいイメージが一覧に表示されます。  

    ![Choose an image](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. ギャラリーから仮想マシンを作成します。 これで **[マイ イメージ]**で新しいイメージが使用可能になりました。
4. 新しいイメージを選択し、 表示される画面でホスト名、パスワード、SSH キーなどを設定します。

    ![Custom image](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. プロビジョニングが完了すると、Azure に FreeBSD VM が実行状態として表示されます。

    ![FreeBSD image in azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)

