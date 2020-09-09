---
title: Azure VMware ソリューション - Azure Data Box を使用した移行
description: Azure Data Box を使用して、Azure VMware ソリューションにデータを一括移行する方法について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507664"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Azure Data Box を使用した Azure VMware ソリューションへのデータ移行

Microsoft Azure Data Box クラウド ソリューションを使用すると、迅速かつ安価な信頼性の高い方法で、数テラバイト (TB) のデータを Azure に送信できます。 独自の Data Box ストレージ デバイスを出荷することにより、セキュリティで保護されたデータ転送を高速化します。 各ストレージ デバイスは、最大で 80 TB のストレージ容量を使用でき、地域の運送業者によって皆さんのデータセンターに輸送されます。 デバイスは堅牢な筐体で保護され、転送中のデータはセキュリティで保護されます。

Data Box を使用すると、VMware データをプライベート クラウドに一括移行できます。 オンプレミスの VMware vSphere 環境からのデータは、ネットワーク ファイル システム (NFS) プロトコルを使用して Data Box にコピーされます。 一括データ移行では、仮想マシン、構成、および関連するデータの特定時点のコピーを Data Box に保存した後、それを Azure に手動で配布します。

この記事では、次の内容について説明します。

* Data Box の設定。
* NFS を使用した、オンプレミスの VMware 環境から Data Box へのデータのコピー。
* Data Box の返却の準備。
* Azure VMware ソリューションにコピーするための BLOB データの準備。
* Azure からプライベート クラウドへのデータのコピー。

## <a name="scenarios"></a>シナリオ

Data Box は、一括データ移行のために次のシナリオで使用します。

* オンプレミスから Azure VMware ソリューションに大量のデータを移行する。 この方法により、ベースラインが確立され、ネットワーク上の差分が同期されます。
* オフになっている多数の仮想マシン (コールド仮想マシン) を移行する。
* 開発環境とテスト環境を設定するための仮想マシン データを移行する。
* 多数の仮想マシン テンプレート、ISO ファイル、仮想マシン ディスクを移行する。

## <a name="before-you-begin"></a>開始する前に

* 前提条件を確認し、Azure portal を通じて [Data Box を注文](../databox/data-box-deploy-ordered.md)します。 注文プロセス中に、BLOB ストレージを有効化するストレージ アカウントを選択する必要があります。 Data Box デバイスを受け取ったら、それをオンプレミス ネットワークに接続し vSphere 管理ネットワークから到達可能な IP アドレスを使用して[デバイスを設定](../databox/data-box-deploy-set-up.md)します。

* Azure VMware ソリューションのプロビジョニングが行われるのと同じリージョンに、仮想ネットワークとストレージ アカウントを作成します。

* 「[ExpressRoute を使用して Azure 仮想ネットワークを CloudSimple に接続する](virtual-network-connection.md)」の手順に従って、プライベート クラウドからストレージ アカウントが作成された仮想ネットワークへの [Azure 仮想ネットワーク接続](cloudsimple-azure-network-connection.md)を作成します。

## <a name="set-up-data-box-for-nfs"></a>NFS 用の Data Box を設定する

[「チュートリアル:ケーブルを配線して Azure Data Box に接続する](../databox/data-box-deploy-set-up.md)」の「デバイスに接続する」セクションの手順に従って、Data Box のローカル Web UI に接続します。  次のようにして、NFS クライアントへのアクセスを許可するように Data Box を構成します。

1. ローカル Web UI で、 **[接続とコピー]** ページに移動します。 **[NFS の設定]** で、 **[NFS のクライアント アクセス]** を選択します。 

    ![NFS のクライアント アクセスを構成する 1](media/nfs-client-access.png)

2. VMware ESXi ホストの IP アドレスを入力し、 **[追加]** を選択します。 この手順を繰り返すことで、vSphere クラスター内のすべてのホストに対するアクセスを構成できます。 **[OK]** を選択します。

    ![NFS のクライアント アクセスを構成する 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **常にコピーしようとするファイル用のフォルダーを共有下に作成してから、ファイルをそのフォルダーにコピーします**。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *root* フォルダーに直接ファイルをコピーすることはできません。

ブロック BLOB とページ BLOB の共有では、第 1 レベルのエンティティはコンテナーであり、第 2 レベルのエンティティは BLOB です。 Azure Files の共有では、第 1 レベルのエンティティは共有であり、第 2 レベルのエンティティはファイルです。

次の表は、Data Box 上の共有への UNC パスと、データのアップロード先である Azure Storage のパスの URL を示しています。 Azure Storage の最終的なパスの URL は、UNC 共有パスから導き出すことができます。
 
| BLOB とファイル | パスと URL |
|---------------- | ------------ |
| Azure ブロック BLOB | <li>共有への UNC パス: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure ページ BLOB  | <li>共有への UNC パス: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>共有への UNC パス: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> VMware データをコピーするには、Azure ブロック BLOB を使用してください。

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>オンプレミスの vCenter クラスターにデータストアとして NFS 共有をマウントし、データをコピーする

Data Box からの NFS 共有は、NFS データストアにデータをコピーするために、オンプレミスの vCenter クラスターまたは VMware ESXi ホストにデータストアとしてマウントする必要があります。

1. オンプレミスの vCenter Server にログインします。

2. **[データセンター]** を右クリックして **[ストレージ]** を選択し、 **[新しいデータストア**] を選択した後、 **[次へ]** を選択します。

   ![新しいデータストアの追加](media/databox-migration-add-datastore.png)

3. データストアの追加ウィザードの手順 1 で、 **[種類]** から **[NFS]** を選択します。

   ![新しいデータストアの追加 - 種類](media/databox-migration-add-datastore-type.png)

4. ウィザードの手順 2 で、NFS バージョンとして **[NFS 3]** を選択し、 **[次へ]** を選択します。

   ![新しいデータストアの追加 - NFS バージョン](media/databox-migration-add-datastore-nfs-version.png)

5. ウィザードの手順 3 で、データストアの名前、パス、およびサーバーを指定します。 サーバーには Data Box の IP アドレスを使用できます。 フォルダー パスは `/<StorageAccountName_BlockBlob>/<ContainerName>/` の形式になります。

   ![新しいデータストアの追加 - NFS 構成](media/databox-migration-add-datastore-nfs-configuration.png)

6. ウィザードの手順 4 で、データストアをマウントする ESXi ホストを選択し、 **[次へ]** を選択します。  クラスターでは、すべてのホストを選択して、仮想マシンの移行を確実に行います。

   ![新しいデータストアの追加 - ホストの選択](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. ウィザードの手順 5 で、概要を確認し、 **[完了]** を選択します。

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>データを Data Box NFS データストアにコピーする

仮想マシンは、新しいデータストアに移行または複製することができます。  移行する未使用の仮想マシンは、 **[ストレージ vMotion]** オプションを使用して Data Box NFS データストアに移行することができます。 アクティブな仮想マシンは、Data Box NFS データストアに複製できます。

* **移動**できる仮想マシンを特定して一覧表示します。
* **複製**する必要がある仮想マシンを特定して一覧表示します。

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>仮想マシンを Data Box データストアに移動する

1. Data Box データストアに移動する仮想マシンを右クリックし、 **[移行]** を選択します。

    ![仮想マシンの移行](media/databox-migration-vm-migrate.png)

2. 移行の種類として **[Change storage only]\(ストレージのみ変更\)** を選択し、 **[次へ]** を選択します。

    ![仮想マシンの移行 - ストレージのみ](media/databox-migration-vm-migrate-change-storage.png)

3. コピー先として **[Databox-Datastore]** を選択し、 **[次へ]** を選択します。

    ![仮想マシンの移行 - データストアの選択](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 情報を確認し、 **[完了]** を選択します。

5. 追加の仮想マシンについて、手順 1 から 4 を繰り返します。

> [!TIP]
> 同じ電源状態 (オンまたはオフ) の複数の仮想マシンを選択し、それらを一括して移行することができます。

仮想マシンは、Data Box から NFS データストアに移行されます。 すべての仮想マシンを移行したら、Azure VMware ソリューションへのデータの移行の準備として、アクティブな仮想マシンをオフにする (シャットダウンする) ことができます。

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>仮想マシンまたは仮想マシン テンプレートを Data Box データストアに複製する

1. 複製する仮想マシンまたは仮想マシン テンプレートを右クリックします。 **[複製]**  >  **[Clone to virtual machine]\(仮想マシンへの複製\)** の順に選択します。

    ![仮想マシンの複製](media/databox-migration-vm-clone.png)

2. 複製された仮想マシンまたは仮想マシン テンプレートの名前を選択します。

3. 複製されたオブジェクトを配置するフォルダーを選択し、 **[次へ]** を選択します。

4. 複製されたオブジェクトを配置するクラスターまたはリソース共有元を選択し、 **[次へ]** を選択します。

5. 保存場所として **[Databox-Datastore]** を選択し、 **[次へ]** を選択します。

    ![仮想マシンの複製 - データストアの選択](media/databox-migration-vm-clone-select-datastore.png)

6. 複製されたオブジェクトのオプションをカスタマイズする場合は、カスタマイズ オプションを選択し、 **[次へ]** を選択します。

7. 構成を確認し、 **[完了]** を選択します。

8. 追加の仮想マシンまたは仮想マシン テンプレートについて、手順 1 から 7 を繰り返します。

仮想マシンは複製され、Data Box から NFS データストアに保存されます。 仮想マシンが複製されたら、Azure VMware ソリューションにデータを移行するための準備として、仮想マシンがシャットダウンされていることを確認してください。

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO ファイルを Data Box データストアにコピーする

1. オンプレミスの vCenter Web UI で **[ストレージ]** に移動します。  **[Databox-Datastore]** を選択し、 **[ファイル]** を選択します。 ISO ファイルを保存するための新しいフォルダーを作成します。

    ![ISO のコピー - 新しいフォルダーの作成](media/databox-migration-create-folder.png)

2. ISO ファイルを保存するフォルダーの名前を指定します。

3. 新しく作成したフォルダーをダブルクリックして開きます。

4. **[ファイルのアップロード]** を選択し、アップロードする ISO ファイルを選択します。
    
    ![ISO のコピー - ファイルのアップロード](media/databox-migration-upload-iso.png)

> [!TIP]
> オンプレミスのデータストアに既に ISO ファイルがある場合は、ファイルを選択して **[コピー先]** を選択し、ファイルを Data Box NFS データストアにコピーできます。


## <a name="prepare-data-box-for-return"></a>返却のために Data Box を準備する

すべての仮想マシン データ、仮想マシン テンプレート データ、および ISO ファイルが Data Box NFS データストアにコピーされたら、データストアを vCenter から切断できます。 データストアを切断する前に、すべての仮想マシンと仮想マシン テンプレートをインベントリから削除する必要があります。

### <a name="remove-objects-from-inventory"></a>インベントリからオブジェクトを削除する

1. オンプレミスの vCenter Web UI で **[ストレージ]** に移動します。 **[Databox-Datastore]** を選択し、 **[VM]** を選択します。

    ![インベントリからの仮想マシンの削除 - オフ](media/databox-migration-select-databox-vm.png)

2. すべての仮想マシンがシャットダウンされていることを確認します。

3. すべての仮想マシンを選択し、右クリックして **[Remove from inventory]\(インベントリから削除する\)** を選択します。

    ![インベントリからの仮想マシンの削除](media/databox-migration-remove-vm-from-inventory.png)

4. **[VM Templates in Folders]\(フォルダー内の VM テンプレート\)** を選択し、手順 3 を繰り返します。

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>vCenter から Data Box NFS データストアを削除する

Data Box NFS データストアは、返却の準備をする前に VMware ESXi ホストから切断する必要があります。

1. オンプレミスの vCenter Web UI で **[ストレージ]** に移動します。

2. **[Databox-Datastore]** を右クリックし、 **[Unmount Datastore]\(データストアのマウント解除\)** を選択します。

    ![Data Box データストアのマウント解除](media/databox-migration-unmount-datastore.png)

3. データストアがマウントされているすべての ESXi ホストを選択し、 **[OK]** を選択します。

    ![Data Box データストアのマウント解除 - ホストの選択](media/databox-migration-unmount-datastore-select-hosts.png)

4. 警告を確認して受け入れ、 **[OK]** を選択します。

### <a name="prepare-data-box-for-return-and-then-return-it"></a>返却のために Data Box を準備し、返却する

記事 「[Azure Data Box の返送と Azure へのデータ アップロードの確認](../databox/data-box-deploy-picked-up.md)」で説明されている手順に従い、Data Box を返却します。 Azure ストレージ アカウントへのデータ コピーの状態を確認します。 状態が [完了] と表示されたら、Azure ストレージ アカウント内のデータを確認できます。

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Azure ストレージから Azure VMware ソリューションにデータをコピーする

Data Box デバイスにコピーされたデータは、Data Box の注文状態が完了と表示されると、Azure ストレージ アカウントで利用できるようになります。 これで、Azure VMware ソリューションにデータをコピーできます。 ストレージ アカウントのデータは、NFS プロトコルを使用してプライベート クラウドの vSAN データストアにコピーする必要があります。 

まず、**AzCopy** を使用して、Azure の Linux 仮想マシン上のマネージド ディスクに BLOB ストレージ データをコピーします。 NFS を使用してマネージド ディスクを使用可能にし、NFS 共有をプライベート クラウド上にデータストアとしてマウントして、データをコピーします。 この方法により、プライベート クラウドへのデータのコピーをより迅速に行えます。

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Linux 仮想マシンとマネージド ディスクを使用してプライベート クラウドにデータをコピーし、NFS 共有としてエクスポートする

1. ストレージ アカウントが作成され、かつプライベート クラウドへの Azure 仮想ネットワーク接続があるリージョンと同じリージョンで、Azure で [Linux 仮想マシン](../virtual-machines/linux/quick-create-portal.md)を作成します。

2. BLOB データよりもストレージ容量が大きいマネージド ディスクを作成し、[それを Linux 仮想マシンに接続します](../virtual-machines/linux/attach-disk-portal.md)。  使用可能な最大のマネージド ディスクの容量よりも BLOB データの量が多い場合は、複数の手順または複数のマネージド ディスクを使用してデータをコピーする必要があります。

3. Linux 仮想マシンに接続し、マネージド ディスクをマウントします。

4. [AzCopy を Linux 仮想マシンに](../storage/common/storage-use-azcopy-v10.md)インストールします。

5. AzCopy を使用して Azure BLOB ストレージからマネージド ディスクにデータをダウンロードします。  コマンドの構文: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`。  `<storage-account-name>` を、お使いの Azure ストレージ アカウント名に置き換え、`<container-name>` を、Data Box を使用してコピーしたデータを保持するコンテナーに置き換えます。

6. Linux 仮想マシンに NFS サーバーをインストールします。

    - Ubuntu/Debian ディストリビューションの場合: `sudo apt install nfs-kernel-server`。
    - Enterprise Linux ディストリビューションの場合: `sudo yum install nfs-utils`。

7. Azure BLOB ストレージのデータがコピーされたマネージド ディスク上のフォルダーのアクセス許可を変更します。  NFS 共有としてエクスポートするすべてのフォルダーのアクセス許可を変更します。

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. `/etc/exports` ファイルを編集して、NFS 共有にアクセスするためのクライアント IP アドレスのアクセス許可を割り当てます。

    ```bash
    sudo vi /etc/exports
    ```
    
    プライベート クラウドの各 ESXi ホスト IP のファイルに次の行を入力します。  複数のフォルダーの共有を作成する場合は、すべてのフォルダーを追加します。

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. コマンド `sudo exportfs -a` を使用して NFS 共有をエクスポートします。

10. コマンド `sudo systemctl restart nfs-kernel-server` を使用して NFS カーネル サーバーを再起動します。


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Linux 仮想マシン NFS 共有をデータストアとしてプライベート クラウドの vCenter クラスターにマウントし、データをコピーする

Linux 仮想マシンからの NFS 共有は、プライベート クラウドの vCenter クラスター上にデータストアとしてマウントする必要があります。 マウントされたデータは、NFS データストアからプライベート クラウドの vSAN データストアにコピーできます。

1. お使いのプライベート クラウドの vCenter サーバーにログインします。

2. **[データセンター]** を右クリックして **[ストレージ]** を選択し、 **[新しいデータストア**] を選択した後、 **[次へ]** を選択します。

   ![新しいデータストアの追加](media/databox-migration-add-datastore.png)

3. データストアの追加ウィザードの手順 1 で、種類として **[NFS]** を選択します。

   ![新しいデータストアの追加 - 種類](media/databox-migration-add-datastore-type.png)

4. ウィザードの手順 2 で、NFS バージョンとして **[NFS 3]** を選択し、 **[次へ]** を選択します。

   ![新しいデータストアの追加 - NFS バージョン](media/databox-migration-add-datastore-nfs-version.png)

5. ウィザードの手順 3 で、データストアの名前、パス、およびサーバーを指定します。  サーバーには Linux 仮想マシンの IP アドレスを使用できます。  フォルダー パスは `/<folder>/<subfolder>/` の形式になります。

   ![新しいデータストアの追加 - NFS 構成](media/databox-migration-add-datastore-nfs-configuration.png)

6. ウィザードの手順 4 で、データストアをマウントする ESXi ホストを選択し、 **[次へ]** を選択します。  クラスターでは、すべてのホストを選択して、仮想マシンの移行を確実に行います。

   ![新しいデータストアの追加 - ホストの選択](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. ウィザードの手順 5 で、概要を確認し、 **[完了]** を選択します。

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>仮想マシンと仮想マシンテン プレートを NFS データストアからインベントに追加する

1. プライベート クラウドの vCenter Web UI で **[ストレージ]** に移動します。  Linux 仮想マシン NFS データストアを選択し、 **[ファイル]** を選択します。

    ![NFS データストアからのファイルの選択](media/databox-migration-datastore-select-files.png)

2. 仮想マシンまたは仮想マシン テンプレートが格納されているフォルダーを選択します。  詳細ウィンドウで、仮想マシンの場合は .vmx ファイルを選択し、仮想マシン テンプレートの場合は .vmtx ファイルを選択します。

3. **[Register VM]\(VM の登録\)** を選択して、プライベート クラウドの vCenter に仮想マシンを登録します。

    ![仮想マシンの登録](media/databox-migration-datastore-register-vm.png)

4. 仮想マシンを登録するデータセンター、フォルダー、およびクラスター/リソース共有元を選択します。

4. すべての仮想マシンと仮想マシン テンプレートについて、手順 3 と 4 を繰り返します。

5. ISO ファイルが格納されているフォルダーに移動します。  ISO ファイルを選択し、 **[コピー先]** を選択して、vSAN データストア上のフォルダーにファイルをコピーします。

これで、仮想マシンと仮想マシン テンプレートがプライベート クラウドの vCenter で利用できるようになります。 これらの仮想マシンは、オンにする前に NFS データストアから vSAN データストアに移動する必要があります。 **[ストレージ vMotion]** オプションを使用し、仮想マシンのターゲットとして vSAN データストアを選択できます。

仮想マシン テンプレートは、Linux 仮想マシン NFS データストアから vSAN データストアに複製する必要があります。

### <a name="clean-up-your-linux-virtual-machine"></a>Linux 仮想マシンをクリーンアップする

すべてのデータがプライベート クラウドにコピーされたら、プライベート クラウドから NFS データストアを削除できます。

1. すべての仮想マシンとテンプレートが vSAN データストアに移動および複製されていることを確認します。

2. インベントリから NFS データストアのすべての仮想マシン テンプレートを削除します。

3. プライベート クラウドの vCenter から Linux 仮想マシンのデータストアをマウント解除します。

4. Azure から仮想マシンとマネージド ディスクを削除します。

5. ストレージ アカウントで Data Box によって転送されたデータを保持しない場合は、Azure ストレージ アカウントを削除します。  
    


## <a name="next-steps"></a>次のステップ

* [Data Box](../databox/data-box-overview.md) の詳細を確認します。
* [ワークロードをプライベート クラウドに移行する](migrate-workloads.md)ためのさまざまなオプションの詳細を確認します。
