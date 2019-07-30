---
title: VM の Azure Premium Storage への移行 | Microsoft Docs
description: 既存の VM を Azure Premium Storage に移行します。 Premium Storage は、Azure Virtual Machines で実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 6b6e442ff3333a7fd085f8e452ae056e7daaba8c
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565516"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Azure Premium Storage への移行 (非管理対象ディスク)

> [!NOTE]
> この記事では、非管理対象 Standard ディスクを使用する VM を、非管理対象 Premium ディスクを使用する VM に移行する方法について説明します。 新しい VM には Managed Disks を使用し、以前の非管理対象ディスクをマネージド ディスクに変換することをお勧めします。 Managed Disks によって基になるストレージ アカウントが処理されるため、ユーザーが処理する必要はありません。 詳細については、「[Managed Disks Overview](../../virtual-machines/windows/managed-disks-overview.md)」(Managed Disks の概要) を参照してください。
>

Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 アプリケーションの VM ディスクを Azure Premium Storage に移行することで、これらのディスクの速度とパフォーマンスを最大限に利用することができます。

このガイドでは、Azure Premium Storage の新しいユーザーが現在のシステムから Premium Storage への円滑な移行に向けて周到に準備できるようにすることを目的としています。 このガイドでは、このプロセスを構成する重要な 3 つの部分を説明します。

* [Premium Storage への移行を計画する](#plan-the-migration-to-premium-storage)
* [仮想ハード ディスク (VHD) を準備して Premium Storage にコピーする](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Premium Storage を使って Azure 仮想マシンを作成する](#create-azure-virtual-machine-using-premium-storage)

他のプラットフォームから Azure Premium Storage への VM の移行、または Standard Storage から Premium Storage への既存の Azure VM の移行が可能です。 このガイドでは、これら 2 つのシナリオの手順について説明します。 状況に応じて、関連するセクションに示されている手順に従ってください。

> [!NOTE]
> Premium SSDe の機能の概要と価格については、[IaaS VM 用のディスクの種類の選択](../../virtual-machines/windows/disks-types.md#premium-ssd)に関するページを参照してください。 高い IOPS を必要とする仮想マシンのディスクは Azure Premium Storage に移行して、アプリケーションが最高のパフォーマンスを発揮できるようにすることをお勧めします。 ディスクが高い IOPS を必要としない場合は、ディスクを Standard Storage 内に保持することでコストを抑えることができます。Standard Storage の場合、仮想マシンのディスク データは SSD ではなくハード ディスク ドライブ (HDD) に格納されます。
>

移行プロセス全体を完了するには、このガイドで説明する手順の前後で追加の操作が必要になる場合があります。 たとえば、アプリケーションをしばらく停止することが必要になる場合がある、仮想ネットワークやエンドポイントの構成や、アプリケーション内部のコード変更などがあります。 これらの操作は、アプリケーションごとに異なります。Premium Storage への完全な移行をできる限りシームレスに行うには、このガイドで説明する手順と共に、これらの必要な操作を完了してください。

## <a name="plan-the-migration-to-premium-storage"></a>Premium Storage への移行を計画する
このセクションでは、この記事の移行手順を実行できる状態にし、VM およびディスクの種類について最適な決定を行うことができるようにします。

### <a name="prerequisites"></a>前提条件
* Azure サブスクリプションが必要です。 サブスクリプションがない場合は、1 か月間の[無料試用版](https://azure.microsoft.com/pricing/free-trial/)サブスクリプションを作成するか、「[Azure の価格](https://azure.microsoft.com/pricing/)」でさらに多くのオプションを利用することができます。
* PowerShell コマンドレットを実行するには、Microsoft Azure PowerShell モジュールが必要です。 インストール先とインストール方法については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview) 」を参照してください。
* Premium Storage で実行されている Azure VM を使う予定がある場合は、Premium Storage 対応の VM を使う必要があります。 Premium Storage 対応の VM では、Standard Storage ディスクと Premium Storage ディスクの両方を使えます。 Premium Storage ディスクの方が、将来的により多くの VM の種類を使用できます。 使用可能な Azure VM ディスクの種類とサイズの詳細については、「[仮想マシンのサイズ](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」と「[Cloud Services のサイズ](../../cloud-services/cloud-services-sizes-specs.md)」を参照してください。

### <a name="considerations"></a>考慮事項
Azure VM では複数の Premium Storage ディスクをアタッチすることができるので、アプリケーションには VM あたり最大で 256 TB のストレージを設定することができます。 Premium Storage を使用すると、アプリケーションは、VM あたり 80,000 IOPS (1 秒あたりの入力/出力操作) と、VM あたり 2000 MB/秒のディスク スループットを実現でき、読み取り操作の待ち時間も非常に短くなります。 VM とディスクのさまざまなオプションがあります。 このセクションは、ワークロードに最適なオプションを探すのに役立ちます。

#### <a name="vm-sizes"></a>VM サイズ
Azure VM のサイズの仕様は、「 [仮想マシンのサイズ](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に記載されています。 Premium Storage で動作する仮想マシンのパフォーマンス特性を確認し、ワークロードに最適な VM を選択してください。 ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。

#### <a name="disk-sizes"></a>ディスク サイズ
VM で使用できるディスクには 5 種類あり、それぞれに特定の IOPS とスループットの制限があります。 VM のディスクの種類を選択する場合は、容量、パフォーマンス、スケーラビリティ、最大負荷に関するアプリケーションのニーズに基づいて、これらの制限を考慮してください。

| Premium ディスクの種類  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| ディスク サイズ           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| ディスクあたりの IOPS       | 500   | 2300  | 5000           | 7500           | 7500           | 
| ディスクあたりのスループット | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 | 250 MB/秒 | 250 MB/秒 |

ワークロードに応じて、VM にデータ ディスクを追加する必要があるかどうかを判断します。 複数の永続データ ディスクを VM に接続できます。 必要に応じて、ディスク全体をストライピングして容量を増やし、ボリュームのパフォーマンスを高めることができます。 (ディスク ストライピングについては、[こちら](../../virtual-machines/windows/premium-storage-performance.md#disk-striping)をご覧ください)[記憶域スペース][4]を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列で構成する必要があります。 そうしない場合は、ディスク全体のトラフィックの配分が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが低下する可能性があります。 Linux VM の場合は、 *mdadm* ユーティリティを使用すると同じ結果を得ることができます。 詳細については、 [Linux でのソフトウェア RAID の構成](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) に関する記事を参照してください。

#### <a name="storage-account-scalability-targets"></a>ストレージ アカウントのスケーラビリティ ターゲット
Premium Storage アカウントには、[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)に加えて、次のスケーラビリティ ターゲットがあります。 アプリケーションの要件が単一のストレージ アカウントのスケーラビリティ ターゲットを上回った場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築し、それらのストレージ アカウント間でデータを分割します。

| 合計アカウント容量 | ローカル冗長ストレージ アカウントの合計帯域幅 |
|:--- |:--- |
| ディスク容量:35 TB<br />スナップショット容量:10 TB |受信と送信を合わせて最大 50 GB/秒 |

Premium Storage の仕様の詳細については、[Azure Storage のスケーラビリティとパフォーマンスのターゲット](storage-scalability-targets.md#premium-performance-storage-account-scale-limits)に関するページを参照してください。

#### <a name="disk-caching-policy"></a>ディスク キャッシュ ポリシー
既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「*読み取り専用*」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「*読み取り/書き込み*」です。 アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。 書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。 既存のデータ ディスクのキャッシュ設定は、[Azure portal](https://portal.azure.com)、または *Set-AzureDataDisk* コマンドレットの *-HostCaching* パラメーターを使用して更新できます。

#### <a name="location"></a>Location
Azure Premium Storage を使用できる場所を選択します。 使用できる場所に関する最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/#services)」をご覧ください。 VM のディスクを保存するストレージ アカウントと同じリージョンにある VM は、個々のリージョン内にある場合よりも優れたパフォーマンスを提供します。

#### <a name="other-azure-vm-configuration-settings"></a>Azure VM のその他の構成設定
Azure VM を作成するときに、特定の VM の設定を構成するよう求められます。 VM の有効期間中は固定されている設定がいくつかありまりますが、後で変更または追加できる設定もあることを忘れないでください。 このような Azure VM の構成設定を確認し、ワークロードの要件に合わせて適切に構成されていることを確認します。

### <a name="optimization"></a>最適化
「[Azure Premium Storage:高パフォーマンスのための設計](../../virtual-machines/windows/premium-storage-performance.md)」では、Azure Premium Storage を使用して高パフォーマンスのアプリケーションを構築するためのガイドラインが示されています。 ガイドラインは、アプリケーションで使われているテクノロジに適用できるパフォーマンスのベスト プラクティスと組み合わせて使えます。

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>仮想ハード ディスク (VHD) を準備して Premium Storage にコピーする
このセクションでは、VM からの VHD の準備と、Azure Storageへの VHD のコピーに関するガイドラインを提供します。

* [シナリオ 1:"既存の Azure VM を Azure Premium Storage に移行している"](#scenario1)
* [シナリオ 2:"他のプラットフォームの VM を Azure Premium Storage に移行している"](#scenario2)

### <a name="prerequisites"></a>前提条件
移行のために VHD を準備するには、次のものが必要です。

* Azure サブスクリプション、ストレージ アカウント、および VHD のコピー先にできるそのストレージ アカウント内のコンテナー。 コピー先のストレージ アカウントには、要件に応じて Standard Storage アカウントまたは Premium Storage アカウントを使用できることに注意してください。
* VHD から複数の VM インスタンスを作成する予定の場合にその VHD を一般化するツール。 たとえば、Windows の sysprep や Ubuntu の virt-sysprep があります。
* ストレージ アカウントに VHD ファイルをアップロードするツール。 「[AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)」を参照するか、[Azure ストレージ エクスプローラー](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)を使用してください。 このガイドでは、AzCopy ツールを使用して VHD をコピーする手順について説明します。

> [!NOTE]
> AzCopy で同期コピー オプションを選ぶ場合、パフォーマンスを最適化するには、コピー先のストレージ アカウントと同じリージョンにある Azure VM からこれらのツールのいずれかを実行して VHD をコピーします。 別のリージョンの Azure VM から VHD をコピーする場合は、パフォーマンスが減速することがあります。
>
> 制限された帯域幅で大量のデータをコピーするには、[Azure Import/Export サービスを使用してデータを Blob Storage へ転送](../storage-import-export-service.md)することを検討してください。この方法であれば、ハード ディスク ドライブを Azure データセンターへ送付してデータを転送することができます。 Azure Import/Export サービスを使用すると、データを Standard Storage アカウントのみにコピーできます。 データが Standard Storage アカウントに配置されたら、[Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) または AzCopy を使用して、そのデータを Premium Storage アカウントに転送できます。
>
> Microsoft Azure でサポートされているのは、固定サイズの VHD ファイルのみです。 VHDX ファイルまたは動的 VHD はサポートされていません。 動的 VHD を使用している場合は、 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) コマンドレットを使用して固定サイズに変換できます。
>
>

### <a name="scenario1"></a>シナリオ 1:"既存の Azure VM を Azure Premium Storage に移行している"
既存の Azure VM を移行する場合は、VM を停止し、VHD の種類に応じて VJD を準備した後、AzCopy または PowerShell で VHD をコピーします。

クリーンな状態を移行するため、VM を完全にダウンする必要があります。 移行が完了するまではダウンタイムになります。

#### <a name="step-1-prepare-vhds-for-migration"></a>手順 1. 移行のために VHD を準備する
既存の Azure VM を Premium Storage に移行する場合、次のような VHD が考えられます。

* 一般化されたペレーティング システム イメージの作成
* 固有のオペレーティング システム ディスク
* データ ディスク

以下では、これら 3 つのシナリオでの VHD の準備について説明します。

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>一般化されたオペレーティング システム VHD を使って複数の VM インスタンスを作成する
複数の汎用的な Azure VM インスタンスの作成に使用する VHD をアップロードする場合は、最初に、Sysprep ユーティリティを使用して VHD を一般化する必要があります。 これは、オンプレミスまたはクラウド内の VHD に適用されます。 sysprep は、コンピューター固有の情報を VHD から削除します。

> [!IMPORTANT]
> VHD を一般化する前に、スナップショットを取得または VM をバックアップします。 sysprep を実行すると、VM インスタンスは停止し、割り当てを解除されます。 次の手順に従って、Windows OS VHD に Sysprep を実行します。 Sysprep コマンドを実行する場合、仮想マシンをシャットダウンする必要があります。 Sysprep の詳細については、「[Sysprep (システム準備) の概要](https://technet.microsoft.com/library/hh825209.aspx)」または「[Sysprep テクニカル リファレンス](https://technet.microsoft.com/library/cc766049.aspx)」を参照してください。
>
>

1. 管理者としてコマンド プロンプト ウィンドウを開きます。
2. 次のコマンドを入力して、Sysprep を開きます。

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. システム準備ツールで、システムの OOBE (Out-of-Box Experience) を選択し、[一般化する] チェック ボックスをオンにして、 **[シャットダウン]** を選択してから、以下のイメージに示されているように **[OK]** をクリックします。 sysprep はオペレーティング システムを一般化し、システムをシャットダウンします。

    ![][1]

Ubuntu VM でこれを行うには virt-sysprep を使用します。 詳細については、 [virt-sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) に関するページを参照してください。 また、その他の Linux オペレーティング システムについては、オープン ソースの [Linux サーバー プロビジョニング ソフトウェア](https://www.cyberciti.biz/tips/server-provisioning-software.html) に関する記述も参照してください。

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>固有のオペレーティング システム VHD を使って単一の VM インスタンスを作成する
マシン固有の情報を必要とする VM 上で実行中のアプリケーションがある場合は、VHD を一般化しないでください。 一意の Azure VM インスタンスの作成に使用できるのは、一般化されていない VHD です。 たとえば、VHD にドメイン コントローラーがある場合は、Sysprep を実行すると、ドメイン コントローラーとしては無効になります。 VHD を一般化する前に、VM 上で実行中のアプリケーションと、これらに対する sysprep の影響を確認してください。

##### <a name="register-data-disk-vhd"></a>データ ディスク VHD を登録する
移行する Azure にデータ ディスクがある場合は、これらのデータ ディスクを使用する VM を必ずシャットダウンする必要があります。

以下で説明する手順に従って、VHD を Azure Premium Storage にコピーし、プロビジョニングされたデータ ディスクとして登録します。

#### <a name="step-2-create-the-destination-for-your-vhd"></a>手順 2. VHD のコピー先を作成する
VHD を管理するためにストレージ アカウントを作成します。 VHD の保存場所を計画するときは、次の点を考慮します。

* 対象の Premium Storage アカウント。
* ストレージ アカウントの場所は、最終ステージで作成する Premium Storage 対応の Azure VM と同じにする必要があります。 必要に応じて、新しいストレージ アカウントにコピーするか、同じストレージ アカウントの使用を計画することができます。
* 次のステージ用に、移行先ストレージ アカウントのストレージ アカウント キーをコピーして保存します。

データ ディスクの場合、一部のデータ ディスク (たとえば、負荷の軽いストレージのあるディスク) を Standard Storage アカウントで保持することもできますが、運用ワークロード用のすべてのデータを、Premium Storage を使うように移動することを強くお勧めします。

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>手順 3. AzCopy または PowerShell で VHD をコピーする
これら 2 つのオプションを処理するには、コンテナーのパスとストレージ アカウント キーを検索する必要があります。 コンテナーのパスとストレージ アカウント キーは、**Azure Portal** >  **[ストレージ]** で見つかります。 コンテナーの URL は、"https:\//myaccount.blob.core.windows.net/mycontainer/" のようになります。

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>オプション 1:AzCopy を使って VHD をコピーする (非同期コピー)
AzCopy を使うと、インターネット経由で VHD を簡単にアップロードできます。 VHD のサイズによっては、この処理に時間がかかる場合があります。 このオプションを使用する場合は、ストレージ アカウントの送受信制限を確認することを忘れないでください。 詳細については、「 [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md) 」を参照してください。

1. 次のリンクから AzCopy をダウンロードしてインストールします:[AzCopy の最新バージョン](https://aka.ms/downloadazcopy)
2. Azure PowerShell を開き、AzCopy がインストールされているフォルダーに移動します。
3. 次のコマンドを使用して、"Source" から "Destination" に VHD ファイルをコピーします。

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    例:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
        ```

    Here are descriptions of the parameters used in the AzCopy command:

   * **/Source: _&lt;source&gt;:_** Location of the folder or storage container URL that contains the VHD.
   * **/SourceKey: _&lt;source-account-key&gt;:_** Storage account key of the source storage account.
   * **/Dest: _&lt;destination&gt;:_** Storage container URL to copy the VHD to.
   * **/DestKey: _&lt;dest-account-key&gt;:_** Storage account key of the destination storage account.
   * **/Pattern: _&lt;file-name&gt;:_** Specify the file name of the VHD to copy.

For details on using AzCopy tool, see [Transfer data with the AzCopy Command-Line Utility](storage-use-azcopy.md).

##### Option 2: Copy a VHD with PowerShell (Synchronized copy)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

You can also copy the VHD file using the PowerShell cmdlet Start-AzStorageBlobCopy. Use the following command on Azure PowerShell to copy VHD. Replace the values in <> with corresponding values from your source and destination storage account. To use this command, you must have a container called vhds in your destination storage account. If the container doesn't exist, create one before running the command.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

例:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>シナリオ 2:"他のプラットフォームの VM を Azure Premium Storage に移行している"
Azure 以外のクラウド ストレージから Azure へ VHD を移行する場合は、最初に VHD をローカル ディレクトリにエクスポートする必要があります。 VHD が格納されているローカル ディレクトリの完全なソース パスを用意し、AzCopy を使って Azure Storage にアップロードします。

#### <a name="step-1-export-vhd-to-a-local-directory"></a>手順 1. VHD をローカル ディレクトリにエクスポートする
##### <a name="copy-a-vhd-from-aws"></a>AWS から VHD をコピーする
1. AWS を使用している場合は、Amazon S3 バケット内の VHD に EC2 インスタンスをエクスポートします。 Amazon EC2 インスタンスのエクスポートに関する Amazon ドキュメントで説明されている手順に従って、Amazon EC2 コマンド ライン インターフェイス (CLI) ツールをインストールし、create-instance-export-task コマンドを実行して EC2 インスタンスを VHD ファイルにエクスポートします。 **create-instance-export-task** コマンドを実行するときは、DISK&#95;IMAGE&#95;FORMAT 変数に必ず **VHD** を使用してください。 エクスポートされた VHD ファイルは、その処理中に指定した Amazon S3 バケットに保存されます。

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. VHD ファイルを S3 バケットからダウンロードします。 VHD ファイルを選択し、 **[操作]** 、 >  **[ダウンロード]** の順に選択します。

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>他の Azure 以外のクラウドから VHD をコピーする
Azure 以外のクラウド ストレージから Azure へ VHD を移行する場合は、最初に VHD をローカル ディレクトリにエクスポートする必要があります。 VHD が保存されているローカル ディレクトリの完全なソース パスをコピーします。

##### <a name="copy-a-vhd-from-on-premises"></a>オンプレミスから VHD をコピーする
オンプレミス環境から VHD を移行する場合は、VHD の保存場所の完全なソース パスが必要です。 ソース パスは、サーバーの場所の場合もあれば、ファイル共有の場合もあります。

#### <a name="step-2-create-the-destination-for-your-vhd"></a>手順 2. VHD のコピー先を作成する
VHD を管理するためにストレージ アカウントを作成します。 VHD の保存場所を計画するときは、次の点を考慮します。

* 対象のストレージ アカウントは、アプリケーション要件に応じて Standard Storage または Premium Storage になります。
* ストレージ アカウントのリージョンは、最終ステージで作成する Premium Storage 対応の Azure VM と同じにする必要があります。 必要に応じて、新しいストレージ アカウントにコピーするか、同じストレージ アカウントの使用を計画することができます。
* 次のステージ用に、移行先ストレージ アカウントのストレージ アカウント キーをコピーして保存します。

運用ワークロード用のすべてのデータを、Premium Storage を使うように移動することを強くお勧めします。

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>手順 3. VHD を Azure Storage にアップロードする
ローカル ディレクトリに VHD を用意したので、AzCopy または AzurePowerShell を使って .vhd ファイルを Azure Storage にアップロードできます。 ここでは両方のオプションを説明します。

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>オプション 1:Azure PowerShell の Add-AzureVhd を使って .vhd ファイルをアップロードする

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

\<Uri> の例として、 **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_** があります。 \<FileInfo> の例としては、 **_"C:\path\to\upload.vhd"_** があります。

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>オプション 2:AzCopy を使って .vhd ファイルをアップロードする
AzCopy を使うと、インターネット経由で VHD を簡単にアップロードできます。 VHD のサイズによっては、この処理に時間がかかる場合があります。 このオプションを使用する場合は、ストレージ アカウントの送受信制限を確認することを忘れないでください。 詳細については、「 [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md) 」を参照してください。

1. 次のリンクから AzCopy をダウンロードしてインストールします:[AzCopy の最新バージョン](https://aka.ms/downloadazcopy)
2. Azure PowerShell を開き、AzCopy がインストールされているフォルダーに移動します。
3. 次のコマンドを使用して、"Source" から "Destination" に VHD ファイルをコピーします。

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    例:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
        ```

    Here are descriptions of the parameters used in the AzCopy command:

   * **/Source: _&lt;source&gt;:_** Location of the folder or storage container URL that contains the VHD.
   * **/SourceKey: _&lt;source-account-key&gt;:_** Storage account key of the source storage account.
   * **/Dest: _&lt;destination&gt;:_** Storage container URL to copy the VHD to.
   * **/DestKey: _&lt;dest-account-key&gt;:_** Storage account key of the destination storage account.
   * **/BlobType: page:** Specifies that the destination is a page blob.
   * **/Pattern: _&lt;file-name&gt;:_** Specify the file name of the VHD to copy.

For details on using AzCopy tool, see [Transfer data with the AzCopy Command-Line Utility](storage-use-azcopy.md).

##### Other options for uploading a VHD
You can also upload a VHD to your storage account using one of the following means:

* [Azure Storage Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Azure Storage Explorer Uploading Blobs](https://azurestorageexplorer.codeplex.com/)
* [Storage Import/Export Service REST API Reference](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> We recommend using Import/Export Service if estimated uploading time is longer than 7 days. You can use [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) to estimate the time from data size and transfer unit.
>
> Import/Export can be used to copy to a standard storage account. You will need to copy from standard storage to premium storage account using a tool like AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Create Azure VMs using Premium Storage
After the VHD is uploaded or copied to the desired storage account, follow the instructions in this section to register the VHD as an OS image, or OS disk depending on your scenario and then create a VM instance from it. The data disk VHD can be attached to the VM once it is created.
A sample migration script is provided at the end of this section. This simple script does not match all scenarios. You may need to update the script to match with your specific scenario. To see if this script applies to your scenario, see below [A Sample Migration Script](#a-sample-migration-script).

### Checklist
1. Wait until all the VHD disks copying is complete.
2. Make sure Premium Storage is available in the region you are migrating to.
3. Decide the new VM series you will be using. It should be a Premium Storage capable, and the size should be depending on the availability in the region and based on your needs.
4. Decide the exact VM size you will use. VM size needs to be large enough to support the number of data disks you have. E.g. if you have 4 data disks, the VM must have 2 or more cores. Also, consider processing power, memory and network bandwidth needs.
5. Create a Premium Storage account in the target region. This is the account you will use for the new VM.
6. Have the current VM details handy, including the list of disks and corresponding VHD blobs.

Prepare your application for downtime. To do a clean migration, you have to stop all the processing in the current system. Only then you can get it to consistent state which you can migrate to the new platform. Downtime duration will depend on the amount of data in the disks to migrate.

> [!NOTE]
> If you are creating an Azure Resource Manager VM from a specialized VHD Disk, please refer to [this template](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) for deploying Resource Manager VM using existing disk.
>
>

### Register your VHD
To create a VM from OS VHD or to attach a data disk to a new VM, you must first register them. Follow steps below depending on your VHD's scenario.

#### Generalized Operating System VHD to create multiple Azure VM instances
After generalized OS image VHD is uploaded to the storage account, register it as an **Azure VM Image** so that you can create one or more VM instances from it. Use the following PowerShell cmdlets to register your VHD as an Azure VM OS image. Provide the complete container URL where VHD was copied to.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

この新しい Azure VM イメージの名前をコピーして保存します。 上記の例では *OSImageName*です。

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>単一の Azure VM インスタンスを作成するための一意のオペレーティング システム VHD
一意の OS VHD をストレージ アカウントにアップロードした後、その VHD を **Azure OS ディスク**として登録すると、そこから VM インスタンスを作成できます。 次の PowerShell コマンドレットを使用して、VHD を Azure OS ディスクとして登録します。 VHD のコピー先の完全なコンテナー URL を入力します。

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

この新しい Azure OS ディスクの名前をコピーして保存します。 上記の例では *OSDisk*です。

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>新しい Azure VM インスタンスに接続するデータ ディスク VHD
データ ディスク VHD をストレージ アカウントにアップロードした後、その VHD を Azure データ ディスクとして登録すると、それを DS シリーズ、DSv2 シリーズ、または GS シリーズの新しい Azure VM インスタンスに接続できます。

次の PowerShell コマンドレットを使用して、VHD を Azure データ ディスクとして登録します。 VHD のコピー先の完全なコンテナー URL を入力します。

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

この新しい Azure データ ディスクの名前をコピーして保存します。 上記の例では *DataDisk*です。

### <a name="create-a-premium-storage-capable-vm"></a>Premium Storage 対応の VM を作成する
OS イメージまたは OS ディスクが登録されたら、DS シリーズ、DSv2 シリーズ、または GS シリーズの新しい VM を作成します。 登録したオペレーティング システム イメージまたはオペレーティング システム ディスクの名前を使用します。 Premium Storage レベルから VM のタイプを選択します。 次の例では、*Standard_DS2* という VM サイズを使用します。

> [!NOTE]
> ディスク サイズを更新し、容量、パフォーマンス要件、使用可能な Azure ディスク サイズに適していることを確認します。
>
>

次の PowerShell コマンドレットの手順に従い、新しい VM を作成します。 最初に、共通のパラメーターを設定します。

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

最初に、新しい VM をホストするクラウド サービスを作成します。

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

次に、シナリオに応じて、登録した OS イメージまたは OS ディスクから Azure VM インスタンスを作成します。

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>複数の Azure VM インスタンスを作成するために一般化されたオペレーティング システム VHD
登録した **Azure OS イメージ** を使用して、1 つ以上の新しい DS シリーズ Azure VM インスタンスを作成します。 新しい VM を作成する際に、次のように、VM 構成でこの OS イメージの名前を指定します。

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>単一の Azure VM インスタンスを作成するための一意のオペレーティング システム VHD
登録した **Azure OS ディスク** を使用して、新しい DS シリーズ Azure VM インスタンスを作成します。 新しい VM を作成する際に、次のように、VM 構成でこの OS ディスクの名前を指定します。

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

クラウド サービス、リージョン、ストレージ アカウント、可用性セット、キャッシュ ポリシーなど、その他の Azure VM 情報を指定します。 VM インスタンスは、関連付けられたオペレーティング システムまたはデータ ディスクと併置する必要があるため、選択したクラウド サービス、リージョン、ストレージ アカウントはすべて、それらのディスクの基になる VHD と同じ場所に配置する必要がある点に注意してください。

### <a name="attach-data-disk"></a>Attach data disk
最後に、データ ディスク VHD を登録した場合は、それらを新しい Premium Storage 対応の Azure VM に接続します。

次の PowerShell コマンドレットを使用して、新しい VM にデータ ディスクに接続し、キャッシュ ポリシーを指定します。 次の例では、キャッシュ ポリシーは読み取り専用 ( *ReadOnly*) に設定されています。

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> このガイドで説明されていないアプリケーションをサポートするには、追加の手順が必要になる場合があります。
>
>

### <a name="checking-and-plan-backup"></a>バックアップの確認と計画
新しい VM が稼働状態になったら、元の VM と同じログイン ID とパスワードを使用して新しい VM にアクセスし、すべてが正しく動作していることを確認します。 すべての設定 (ストライプ ボリュームを含む) が新しい VM に存在します。

最後に、アプリケーションのニーズに応じて、新しい VM のバックアップとメンテナンスのスケジュールを計画します。

### <a name="a-sample-migration-script"></a>サンプル移行スクリプト
移行する VM が複数ある場合は、PowerShell スクリプトを使用した自動化が便利です。 次に示すサンプルは、VM の移行を自動化するサンプル スクリプトです。 このスクリプトはほんの一例であり、現在の VM ディスクについていくつかの仮定が含まれていることに注意してください。 特定のシナリオに対応するように、スクリプトの更新が必要になる場合があります。

前提条件は次のとおりです。

* クラシック Azure VM を作成しています。
* ソース OS ディスクとソース データ ディスクは、同じストレージ アカウントおよび同じコンテナーにあります。 OS ディスクとデータ ディスクが同じ場所にない場合は、AzCopy または Azure PowerShell を使って、ストレージ アカウントとコンテナーに VHD をコピーできます。 前述の手順「[AzCopy または PowerShell で VHD をコピーする](#copy-vhd-with-azcopy-or-powershell)」をご覧ください。 実際のシナリオに合うようにこのスクリプトを編集することもできますが、AzCopy または PowerShell を使う方が簡単で速いのでお勧めします。

自動化スクリプトを以下に示します。 実際の情報でテキストを置き換え、特定のシナリオに合わせてスクリプトを更新します。

> [!NOTE]
> 既存のスクリプトを使うと、ソース VM のネットワーク構成は保持されません。 移行先の VM でネットワークの設定を再構成する必要があります。
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>最適化
現在の VM の構成は、Standard ディスクで問題なく動作するようにカスタマイズされている可能性があります。 たとえば、パフォーマンスを向上させるために、ストライプ ボリュームで多くのディスクを使用しています。 たとえば、Premium Storage では、4 つのディスクを個別に使うのではなく、1 つのディスクを使うことによって、コストを最適化できる場合があります。 このような最適化は個々のケースに応じて処理する必要があり、移行後にカスタム手順が必要になります。 このプロセスは、セットアップ時に定義されたディスク レイアウトに依存するデータベースおよびアプリケーションではうまく機能しない可能性があることに注意してください。

##### <a name="preparation"></a>準備
1. 前のセクションで説明した単純な移行を完了します。 最適化は、移行後に新しい VM で実行されます。
2. 最適化された構成に必要な新しいディスク サイズを定義します。
3. 新しいディスクの指定に対する現在のディスク/ボリュームのマッピングを決定します。

##### <a name="execution-steps"></a>実行ステップ
1. Premium Storage VM で適切なサイズの新しいディスクを作成します。
2. VM にログインし、現在のボリュームのデータを、そのボリュームにマッピングされる新しいディスクにコピーします。 この操作を、新しいディスクにマッピングする必要のある現在のボリュームすべてに対して実行します。
3. 次に、新しいディスクに切り替えるようにアプリケーションの設定を変更し、以前のボリュームをデタッチします。

ディスクのパフォーマンスを向上するためのアプリケーションの調整については、[高パフォーマンス用の設計](../../virtual-machines/windows/premium-storage-performance.md)に関する記事の最適化アプリケーションのパフォーマンスのセクションを参照してください。

### <a name="application-migrations"></a>アプリケーションの移行
データベースやその他の複雑なアプリケーションを移行する場合は、アプリケーションの提供元が指定する特別な手順が必要になることがあります。 各アプリケーションのドキュメントを参照してください。 例: 通常、データベースは、バックアップと復元を使用して移行できます。

## <a name="next-steps"></a>次の手順
仮想マシンの移行に関する具体的なシナリオについては、次のリソースを参照してください。

* [ストレージ アカウント間での Azure 仮想マシンの移行](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Windows Server VHD の作成と Azure へのアップロード](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Linux VHD の作成と Azure へのアップロード](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Amazon AWS から Microsoft Azure への仮想マシンの移行](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

また、Azure Storage と Azure Virtual Machines の詳細については、次のリソースもご覧ください。

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [IaaS VM 用のディスクの種類の選択](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
