---
title: StorSimple 8000 シリーズから Azure File Sync への移行
description: StorSimple 8100 または 8600 アプライアンスを Azure File Sync に移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6ad132513c2ec61dd5a290da1a88e50f0ad6eb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510355"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 および 8600 から Azure File Sync への移行

StorSimple 8000 シリーズは、8100 または 8600 の物理オンプレミス アプライアンス、およびそれらのクラウド サービス コンポーネントによって表されます。 これらのアプライアンスのいずれかから Azure File Sync 環境にデータを移行することができます。 Azure File Sync は、StorSimple アプライアンスの移行先となる、既定の戦略的な長期 Azure サービスです。

StorSimple 8000 シリーズは 2022 年 12 月に[提供終了](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series)となります。 できるだけ早く移行の計画を開始することが重要です。 この記事では、Azure File Sync への移行を成功させるために必要な背景知識と移行手順について説明します。 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft はお客様の移行を支援できるよう取り組んでいます。 カスタマイズした移行計画について、および移行中のサポートについては、AzureFilesMigration@microsoft .com にメールをお送りください。

Azure File Sync は、次の 2 つの主要なコンポーネントに基づく Microsoft のクラウド サービスです。

* ファイルの同期とクラウドを使った階層化。
* Azure のネイティブ ストレージとしてのファイル共有。SMB や file REST などの複数のプロトコルを介してアクセスできます。 Azure ファイル共有は、ネットワーク ドライブとしてネイティブにマウントできる Windows サーバー上のファイル共有に相当します。 属性、アクセス許可、タイムスタンプなどの重要なファイルの忠実性がサポートされています。 Azure ファイル共有を使用すると、クラウドに格納されているファイルやフォルダーをアプリケーションやサービスで解釈する必要がなくなります。 使い慣れたプロトコルと Windows エクスプローラーなどのクライアントを使用してネイティブにそれらにアクセスできます。 そのため、Azure ファイル共有は、汎用ファイル サーバー データと一部のアプリケーション データをクラウドに格納するための理想的で最も柔軟性の高いアプローチです。

この記事では、移行手順を中心に説明します。 移行前に Azure File Sync について詳しく知りたい場合は、次の記事をお勧めします。

* [Azure File Sync - 概要](https://aka.ms/AFS "概要")
* [Azure File Sync - デプロイ ガイド](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>移行の目標

目標は、運用データの整合性と可用性を保証することです。 後者については、ダウンタイムを最小限に抑えて、通常のメンテナンス期間に収まるか、わずかに超えるだけで済むようにする必要があります。

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 シリーズの Azure File Sync への移行パス

Azure File Sync エージェントを実行するには、ローカル Windows サーバーが必要です。 Windows サーバーは、2012R2 サーバー以降であれば使用できますが、理想的なのは Windows Server 2019 です。

代わりに採用できる移行パスは多数あり、そのすべてを説明し、この記事でベスト プラクティスとして推奨されているルートと比較してそれらのリスクや欠点が大きい理由を説明しようとすると、記事があまりに長くなります。

![StorSimple 8000 シリーズの移行フェーズの概要](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 シリーズの移行ルートでは、この記事で後述する各フェーズの概要を説明しています。")

上の図は、この記事のセクションに対応するフェーズを示したものです。
ここでは、クラウド側の移行を使用して、ローカルの StorSimple アプライアンスに対する不要なファイルの呼び戻しを回避します。 このアプローチを使うと、ローカルのキャッシュ動作やネットワーク帯域幅の使用に対する影響を回避できます。これらのいずれかがあると、運用ワークロードに影響する可能性があります。
クラウド側の移行は、データのスナップショット (ボリューム クローン) に対して動作します。 そのため、移行の終了時にカットオーバーされるまで、運用データはこのプロセスから分離されます。 本質的にはバックアップであるものが処理対象になるので、問題が発生した場合でも、安全かつ簡単に移行を繰り返すことができるようになります。

## <a name="considerations-around-existing-storsimple-backups"></a>既存の StorSimple バックアップに関する考慮事項

StorSimple を使うと、ボリューム クローンの形式でバックアップを作成できます。 この記事では、新しいボリューム クローンを使用してライブ ファイルを移行します。
ライブ データだけでなくバックアップも移行する必要がある場合でも、この記事のすべてのガイダンスが適用されます。 唯一の違いは、新しいボリューム クローンから始めるのではなく、移行する必要がある最も古いバックアップ ボリューム クローンから始めることです。

順序は次のとおりです。

* 移行する必要があるボリューム クローンの最小セットを決定します。 移行するバックアップが多くなるほど、移行プロセス全体にかかる時間が長くなるため、この一覧を可能な限り最小限に抑えることをお勧めします。
* 移行プロセスを実行するときは、移行対象の最も古いボリューム クローンから始め、それ以降の移行では、その次に古いものを使用します。
* 各ボリューム クローンの移行が完了したら、Azure ファイル共有スナップショットを作成する必要があります。 [Azure ファイル共有スナップショット](storage-snapshots-files.md)は、Azure ファイル共有に関するファイルとフォルダー構造の特定時点のバックアップを保持する方法です。 移行の完了後にこれらのスナップショットが必要になります。これは、移行の進行中に各ボリューム クローンのバージョンを確実に保持するためです。
* 同じ StorSimple ボリュームによって処理されるすべての Azure ファイル共有について、Azure ファイル共有スナップショットを作成します。 ボリューム クローンはボリューム レベルにあり、Azure ファイル共有スナップショットは共有レベルにあります。 ボリューム クローンの移行が完了したら、(各 Azure ファイル共有の) 共有スナップショットを作成する必要があります。
* ライブ データのスナップショットが作成されるまで、ボリューム クローンの移行プロセスを繰り返し、各ボリューム クローンの後に共有スナップショットを作成します。 ボリューム クローンを移行するプロセスについては、以下のフェーズで説明します。 

バックアップをまったく移行する必要がなく、ライブ データのみの移行が完了した後に Azure ファイル共有側で新しいバックアップ チェーンを開始できる場合、移行の複雑さと移行にかかる時間を減らすために役立ちます。 StorSimple では、バックアップを移行するかどうかと、(各共有ではなく) 各ボリュームの数を決定することができます。

## <a name="phase-1-get-ready"></a>フェーズ 1:準備

:::row:::
    :::column:::
        ![記事のこのサブセクションに焦点を当てるために役立つ、以前の概要画像の一部を示す画像。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        移行の基礎は、ボリューム クローンと、StorSimple 8020 と呼ばれる仮想クラウド アプライアンスです。
このフェーズでは、Azure のこれらのリソースのデプロイに焦点を当てます。
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>StorSimple 8020 仮想アプライアンスをデプロイする

クラウド アプライアンスのデプロイは、セキュリティ、ネットワーク、およびその他のいくつかの考慮事項を必要とするプロセスです。

> [!IMPORTANT]
> 次のガイドには、不要なセクションがいくつか含まれています。 この記事を最初から「手順 3」まで読んで従います。 その後、この記事に戻ります。 現時点では、このガイドで「手順 3」を完了する必要はありません。

[StorSimple 8020 仮想アプライアンスのデプロイ](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>使用するボリューム クローンを決定する

移行を開始する準備ができたら、最初の手順は、バックアップの場合と同様に、StorSimple クラウド ストレージの現在の状態をキャプチャする新しいボリューム クローンを作成することです。 使用している StorSimple ボリュームごとにクローンを作成します。
バックアップを移行する必要がある場合、最初に使用するボリューム クローンは、新しく作成したクローンではなく、移行する必要がある最も古いボリューム クローン (最も古いバックアップ) です。
詳細なガイダンスについては、セクション「[既存の StorSimple バックアップに関する考慮事項](#considerations-around-existing-storsimple-backups)」を参照してください。

> [!IMPORTANT]
> 次のガイドには、不要なセクションがいくつか含まれています。 リンク先のセクションで説明されている手順のみを読んで実行します。 その後、この記事に戻ります。 「次のステップ」セクションに従う必要はありません。

[ボリューム クローンを作成する](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>ボリューム クローンを使用する

フェーズ 1 の最後のフェーズでは、選択したボリューム クローンを Azure の 8020 仮想アプライアンスで利用できるようにします。

> [!IMPORTANT]
> 次のガイドには必要な手順が含まれていますが、最後にボリュームをフォーマットする手順もあります。 **ボリュームはフォーマットしないでください** リンク先の始めから "セクション 7" の「10. シンプル ボリュームをフォーマットするには、...」の手順の前まで実行します。この手順を実行する前に止め、この記事に戻ります。

[Azure の 8020 仮想アプライアンスにボリューム クローンをマウントする](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>フェーズ 1 の概要

フェーズ 1 が完了すると、以下を完了することになります。

* StorSimple 8020 仮想アプライアンスを Azure にデプロイします。
* 移行を開始するボリューム クローンを決定します。
* ボリューム クローン (ライブ ボリュームごとに 1 つ) を Azure の StorSimple 仮想アプライアンスにマウントし、そのデータを他の用途に使用できるようにします。

## <a name="phase-2-cloud-vm"></a>フェーズ 2:クラウド VM

:::row:::
    :::column:::
        ![記事のこのサブセクションに焦点を当てるために役立つ、以前の概要画像の一部を示す画像。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Azure の StorSimple 8020 仮想アプライアンス上で最初のクローンを使用できるようになったら、VM をプロビジョニングし、iSCSI を介してその VM にその (または複数の) ボリューム クローンを公開します。
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Azure VM をデプロイする

Azure の Windows Server 仮想マシンは、StorSimple 8020 と似ており、移行中にのみ必要な一時的なインフラストラクチャです。
デプロイする VM の構成は、主に同期する項目 (ファイルとフォルダー) の数によって変わります。 懸念がある場合は、より高いパフォーマンス構成を使用することをお勧めします。

1 台の Windows Server で、最大 30 個の Azure ファイル共有を同期できます。
決定する仕様には、すべての共有およびパスまたは StorSimple ボリュームのルートを含め、項目 (ファイルとフォルダー) をカウントする必要があります。

データの全体的なサイズはあまりボトルネックにはなりません。マシンの仕様を調整するために必要な項目の数がそうなります。

* [同期する必要がある項目 (ファイルとフォルダー) の数に基づいて Windows サーバーのサイズを変更する方法を参照してください。](storage-sync-files-planning.md#recommended-system-resources)

    **注意:** 前記のリンク先の記事では、サーバー メモリ (RAM) の範囲に関する表が示されています。 Azure VM については、数をなるべく大きくするようにしてください。 オンプレミス コンピューターについては、数を減らすよう検討することもできます。

* [Windows Server VM をデプロイする方法を参照してください。](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> VM が StorSimple 8020 仮想アプライアンスと同じ Azure リージョンにデプロイされていることを確認します。 この移行の一環として、クラウド データのリージョンも現在格納されているリージョンから変更する必要がある場合は、後の手順で Azure ファイル共有をプロビジョニングするときに変更できます。

> [!IMPORTANT]
> 多くの場合、お使いのオンプレミスの StorSimple アプライアンスに対応してオンプレミスの Windows Server が使用されます。 そのような構成においては、その Windows サーバーで "[データ重複除去](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" 機能を有効にすることができます。 **StorSimple データでデータ重複除去を使用した場合には、この Azure VM でもデータ重複除去を確実に有効にしてください。** このファイルレベルの重複除去と、StorSimples 組み込みのブロックレベルの重複除去とを混同しないでください。後者に関しては、いかなるアクションも必要ありません。

> [!IMPORTANT]
> パフォーマンスを最適化するには、クラウド VM 用に**高速 OS ディスク**をデプロイしてください。 同期データベースは、すべてのデータ ボリュームについて、OS ディスクに保存することになります。 また、**サイズの大きな OS ディスク**を作成するようにしてください。 StorSimple ボリュームの項目 (ファイルとフォルダー) の数によっては、同期データベースを格納するために、OS ディスクに**数百 GiB** の容量が必要になる場合があります。

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>StorSimple 8020 ボリュームを Azure VM に公開する

このフェーズでは、8020 仮想アプライアンスから、プロビジョニングした Windows Server VM に 1 つまたは複数の StorSimple ボリュームを iSCSI 経由で接続します。

> [!IMPORTANT]
> 次の記事については、「**クラウド アプライアンスのプライベート IP を取得する**」と **iSCSI 経由の接続**に関するセクションのみを完了して、この記事に戻ります。

1. [クラウド アプライアンスのプライベート IP を取得する](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [iSCSI 経由で接続する](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>フェーズ 2 の概要

フェーズ 2 が完了すると、以下を完了することになります。 

* 8020 仮想 StorSimple アプライアンスと同じリージョンに Windows Server VM をプロビジョニングします
* すべての該当するボリュームを 8020 から iSCSI 経由で Windows Server VM に公開します。
* マウントされたボリュームのサーバー VM 上でエクスプローラーを使用すると、ファイルとフォルダーの内容が表示されるようになります。

移行が必要なすべてのボリュームに対してこれらの手順を完了した場合にのみ、フェーズ 3 に進みます。

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>フェーズ 3: Azure ファイル共有を設定し、Azure File Sync の準備をする

:::row:::
    :::column:::
        ![記事のこのサブセクションに焦点を当てるために役立つ、以前の概要画像の一部を示す画像。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        このフェーズでは、多数の Azure ファイル共有を決定してプロビジョニングし、StorSimple アプライアンスの代替としてオンプレミスで Windows サーバーを作成し、そのサーバーを Azure File Sync 用に構成します。 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>既存の名前空間を Azure ファイル共有にマップする

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Azure ファイル共有をデプロイする

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> StorSimple データが存在する現在のリージョンから Azure リージョンを変更する必要がある場合は、使用する新しいリージョンで Azure ファイル共有をプロビジョニングします。 リージョンを決定するには、Azure ファイル共有を保持するストレージ アカウントをプロビジョニングするときに選択します。 以下でプロビジョニングする Azure File Sync リソースも、同じ新しいリージョンにあることを確認します。

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Azure File Sync クラウド リソースをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> StorSimple データが存在する現在のリージョンから Azure リージョンを変更する必要がある場合は、新しいリージョンで Azure ファイル共有のストレージ アカウントをプロビジョニングします。 このストレージ同期サービスをデプロイするときに、必ず同じリージョンを選択します。

### <a name="deploy-an-on-premises-windows-server"></a>オンプレミスの Windows サーバーをデプロイする

* 仮想マシンまたは物理サーバーとして、Windows Server 2019 (最低でも 2012R2) のサーバーを作成します。 Windows Server フェールオーバー クラスターもサポートされています。 StorSimple 8100 または 8600 の前面にある可能性があるサーバーを再利用しないでください。
* 直接接続記憶域 (NAS ではなく DAS、NAS はサポートされていません) をプロビジョニングまたは追加します。

StorSimple 8100 または 8600 アプライアンスがローカルでキャッシュに使用できる容量以上のストレージを新しい Windows サーバーに用意することをお勧めします。 StorSimple アプライアンスを使用したものと同じ方法で Windows サーバーを使用します。アプライアンスと同じ容量のストレージがある場合、キャッシュ エクスペリエンスは同じでなくても似ているはずです。
Windows Server のストレージは、いつでも追加または削除できます。 これにより、ローカル ボリューム サイズとキャッシュに使用できるローカル ストレージの容量をスケールできます。

### <a name="prepare-the-windows-server-for-file-sync"></a>ファイル同期のために Windows サーバーを準備する

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Windows サーバー上で Azure File Sync を構成する

このプロセスのために、登録済みのオンプレミス Windows サーバーを準備し、インターネットに接続しておく必要があります。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **クラウドを使った階層化を必ず有効にしておきます。** クラウドを使った階層化は、ローカル サーバーが、クラウドに格納されているよりもストレージ容量が少なくても、完全な名前空間を使用できるようになる AFS 機能です。 ローカルの関心のあるデータもローカルにキャッシュされ、高速でローカルのアクセス パフォーマンスが実現します。 この手順でクラウドを使った階層化を有効にするもう 1 つの理由は、この段階ではファイル コンテンツを同期せず、この時点で名前空間のみを移行するようにするためです。

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>フェーズ 4: 同期のために Azure VM を構成する

:::row:::
    :::column:::
        ![記事のこのサブセクションに焦点を当てるために役立つ、以前の概要画像の一部を示す画像。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        このフェーズは、iSCSI のマウントされた最初のボリューム クローンを使用する Azure VM に関するものです。 このフェーズでは、Azure File Sync を介して VM を接続し、StorSimple ボリューム クローンからファイルを移行する最初のラウンドを開始します。
        
    :::column-end:::
:::row-end:::

Azure File Sync 用に、StorSimple 8100 または 8600 アプライアンスを置き換えるオンプレミス サーバーは既に構成しました。 

Azure VM の構成は、ほぼ同じプロセスであり、追加の手順が 1 つあります。 次の手順に従ってプロセスを実行します。

> [!IMPORTANT]
> **クラウドを使った階層化を有効にして Azure VM を構成しない**ことが重要です。 移行中に、このサーバーのボリュームを新しいボリューム クローンと交換します。 クラウドを使った階層化には、CPU 使用率に関して利点と避けるべきオーバーヘッドはありません。

1. [AFS エージェントをデプロイします (前のセクションを参照してください)。](#prepare-the-windows-server-for-file-sync)
2. [Azure File Sync 用に VM を準備をします。](#get-the-vm-ready-for-azure-file-sync)
3. [同期を構成します](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Azure File Sync 用に VM を準備する

Azure File Sync は、マウントされた iSCSI StorSimple ボリュームからターゲット Azure ファイル共有にファイルを移動するために使用されます。
この移行プロセスでは、同じドライブ文字で複数のボリューム クローンを VM にマウントします。 ファイルとフォルダーの新しいバージョンとしてマウントした次のボリューム クローンを確認し、Azure File Sync を介して接続された Azure ファイル共有を更新するように Azure File Sync を構成する必要があります。 

> [!IMPORTANT]
> これが機能するには、Azure File Sync を構成する前に、サーバーにレジストリ キーを設定する必要があります。

1. VM のシステム ドライブに新しいディレクトリを作成します。 Azure File Sync 情報は、マウントされたボリューム クローンではなく、ここに保持する必要があります。 例: `"C:\syncmetadata"`
2. regedit を開き、`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync` のレジストリ ハイブを見つけます。
3. 次の名前の文字列型の新しいキーを作成します。***MetadataRootPath***
4. システム ボリュームに作成したディレクトリへの完全なパスを設定します。例: `C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Azure VM 上で Azure File Sync を構成する

この手順は、オンプレミス サーバー上で AFS を構成する方法を説明した前のセクションと似ています。

違いは、このサーバーでクラウドを使った階層化を有効にしてはならない点と、適切なフォルダーが適切な Azure ファイル共有に接続されていることを確認する必要がある点です。 そうしないと、Azure ファイル共有とデータ コンテンツの名前が一致せず、クラウド リソースまたはローカル フォルダーの名前を変更するには、同期を再構成するしかなくなります。

[Windows サーバー上で Azure File Sync を構成する方法については、前のセクション](#configure-azure-file-sync-on-the-windows-server)を参照してください。

### <a name="step-4-summary"></a>手順 4 の概要

この時点で、iSCSI を介して StorSimple ボリューム クローンをマウントした Azure VM 上で Azure File Sync を正常に構成できるようになります。
データは Azure VM からさまざまな Azure ファイル共有に流れるようになりました。また、そこからオンプレミスの Windows サーバー上に完全に階層化された名前空間が表示されます。

> [!IMPORTANT]
> この時点で、変更が行われていないこと、または Windows Server にユーザー アクセス権が付与されていないことを確認します。

Azure VM を介して Azure ファイル共有に移行する最初のボリューム クローン データには時間がかかる可能性があります (場合によっては数週間)。 この処理にかかる時間の見積もりは注意が必要であり、多くの要因によって変わります。 特に注目する点は、Azure VM から StorSimple ボリューム上のファイルにアクセスできる速度と、同期が必要なファイルとフォルダーを Azure File Sync で処理できる速度です。 

経験上、帯域幅 (したがって実際のデータ サイズ) が従属的な役割を果たすと想定できます。 この移行ラウンドまたは後続の移行ラウンドにかかる時間は、1 秒間に処理できる項目数に大きく左右されます。 そのため、たとえば、100,000 個のファイルとフォルダーがある 1 TiB は、わずか 50,000 個のファイルとフォルダーがある 1 TiB よりも遅く完了する可能性があります。

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>フェーズ 5:複数のボリューム クローンを反復処理する

:::row:::
    :::column:::
        ![記事のこのサブセクションに焦点を当てるために役立つ、以前の概要画像の一部を示す画像。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        前のフェーズで説明したように、初回の同期には長い時間がかかることがあります。 ユーザーとアプリケーションは、引き続きオンプレミスの StorSimple 8100 または 8600 アプライアンスにアクセスしています。 これは、変更が蓄積されていることを意味します。また、ライブ データと初回のボリューム クローンとの間に毎日大きな差分があり、現在、フォームを移行しています。 このセクションでは、複数のボリューム クローンを使用して、同期が完了したタイミングを通知することで、ダウンタイムを最小限に抑える方法について説明します。
    :::column-end:::
:::row-end:::

残念ながら、移行プロセスは瞬時に完了しません。 つまり、前述のライブ データとの差分は避けられない結果になります。 幸いなことに、新しいボリューム クローンをマウントするプロセスを繰り返すことができます。 各ボリューム クローンの差分は、徐々に小さくなります。 そのため、最終的には、ユーザーとアプリをオフラインにしてオンプレミスの Windows サーバーにカットオーバーする際に許容される期間内に同期が完了します。

ユーザーとアプリをオフラインにしても問題ない考えられる短時間で同期が完了するまで、次の手順を繰り返します。

1. [特定のボリューム クローンの同期が完了したことを確認します。](#determine-when-sync-is-done)
2. [新しいボリューム クローンを作成し、8020 仮想アプライアンスにマウントします。](#the-next-volume-clones)
3. [同期が完了したタイミングを判断します。](#determine-when-sync-is-done)
4. [カットオーバー戦略](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>次のボリューム クローン

ボリューム クローンの作成については、この記事で既に説明しました。
このフェーズには、次の 2 つのアクションがあります。

1. [ボリューム クローンを作成する](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [そのボリューム クローンをマウントする (上記を参照してください)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>同期が完了したタイミングを判断する

同期が完了したら、時間測定を停止し、ボリューム クローンを作成してマウントするプロセスを繰り返す必要があるか、または最後のボリューム クローンの同期にかかった時間が十分に短かったかを判断できます。

同期が完了したことを判断するには:

1. イベント ビューアーを開き、 **[アプリケーションとサービス]** に移動します
2. **Microsoft\FileSync\Agent\Telemetry** に移動して開きます
3. 完了した同期セッションに対応する、最新の**イベント 9102** を探します。
4. **[詳細]** を選択し、**SyncDirection** の値が **Upload** であることを確認します
5. **HResult** を確認し、**0** が表示されていることを確認します。 これは、同期セッションが正常に完了したことを意味します。 HResult が 0 以外の値の場合、同期中にエラーが発生しました。**PerItemErrorCount** が 0 より大きい場合は、一部のファイルまたはフォルダーが正しく同期されていません。 HResult が 0 であるのに、PerItemErrorCount が 0 より大きい場合もあります。 この時点では、PerItemErrorCount について心配する必要はありません。 これらのファイルは後でキャッチします。 このエラー数が非常に多い (数千項目) 場合は、カスタマー サポートに連絡して、最適な次のフェーズに関するガイダンスについては Azure File Sync 製品グループにお問い合わせください。
6. 行に HResult 0 を含む複数の 9102 イベントがあるかどうかを確認します。 これは、このボリューム クローンの同期が完了したことを示します。

### <a name="cut-over-strategy"></a>カットオーバー戦略

1. 現時点でボリューム クローンからの同期が十分に高速かどうかを判断します (差分が十分に小さいか)。
2. StorSimple アプライアンスをオフラインにします。
3. 最終の RoboCopy。

時間を測定し、最近のボリューム クローンからの同期が、システムのダウンタイムとして十分に許容できる期間内で完了できるかどうかを判断します。

次は、StorSimple アプライアンスへのユーザー アクセスを無効にします。 その他の変更はありません。 ダウンタイムが始まりました。
アプライアンスのオンラインと接続の状態を維持する必要がありますが、ここではその変更を防ぐ必要があります。

フェーズ 6 では、最後のボリューム クローン以降のライブ データの差分を取得します。

## <a name="phase-6-a-final-robocopy"></a>フェーズ 6:最終的な RoboCopy

この時点で、オンプレミスの Windows サーバーと StorSimple 8100 または 8600 アプライアンスには 2 つの違いがあります。

1. 同期されていないファイルが存在する可能性があります (上記のイベント ログの **PerItemErrors** を参照してください)
2. StorSimple アプライアンスにはキャッシュがありますが、Windows Server は名前空間のみであり、現時点ではファイル コンテンツはローカルに格納されていません。

![記事のこのサブセクションに焦点を当てるために役立つ、以前の概要画像の一部を示す画像。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Windows Server のキャッシュをアプライアンスの状態にし、最終的な RoboCopy でファイルが残らないようにします。

> [!CAUTION]
> 続ける RoboCopy コマンドは、次に示すとおりにすることをお勧めします。 ローカルにあるファイルと、ボリューム クローンと同期のアプローチで以前に移行したことがないファイルのみをコピーします。 移行が完了した後、同期されなかった問題を解決できます (「[Azure File Sync のトラブルシューティング](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)」を参照してください。 多くの場合、削除しても問題のない、印刷できない文字がファイル名に含まれています)。

RoboCopy コマンド:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      RoboCopy でマルチスレッドを実行できるようにします。 既定値は 8 です。最大値は 128 です。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      状態を UNICODE 形式でログ ファイルに出力します (既存のログを上書きします)。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      コンソール ウィンドウに出力します。 ログ ファイルへの出力と組み合わせて使用されます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      バックアップ アプリケーションが使用するのと同じモードで RoboCopy を実行します。 これにより、現在のユーザーがアクセス許可を持っていないファイルを、RoboCopy で移動できます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      RoboCopy がソース (StorSimple アプライアンス) とターゲット (Windows Server ディレクトリ) 間の差分のみを考慮できるようにします。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      ファイル コピーの忠実性 (既定値は /COPY:DAT)、コピー フラグ: D = データ、A = 属性、T = タイムスタンプ、S = セキュリティ = NTFS ACL、O = 所有者情報、U = 監査情報
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      すべてのファイル情報をコピーします (/COPY:DATSOU と同等)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      ディレクトリのコピーの忠実性 (既定値は /DCOPY:DA)、コピー フラグ: D = データ、A = 属性、T = タイムスタンプ
   :::column-end:::
:::row-end:::

この RoboCopy コマンドは、Azure ファイルへのファイル同期を使用して構成した Windows サーバー上の各ディレクトリをターゲットとして実行する必要があります。

これらの複数のコマンドは並行して実行できます。
この RoboCopy の手順が完了したら、前述の StorSimple アプライアンスの場合と同様に、Windows Server へのアクセスをユーザーとアプリに許可できるようになります。

robocopy のログ ファイルを調べて、ファイルが残っているかどうかを確認します。 問題が存在する場合、ほとんどの場合は、移行が完了し、ユーザーとアプリのホームをこの Windows サーバーに変更した後に解決できます。 問題を解決する必要がある場合は、フェーズ 7 より前に実行してください。

場合によっては、以前に StorSimple データで保持していた SMB 共有を Windows サーバー上に作成する必要があります。 ここで時間を無駄にしないために、この手順を前倒しし、早期に実行することができます。ただし、この時点より前に、Windows サーバー上のファイルが変更されないようにする必要があります。

DFS-N のデプロイがある場合、DFN 名前空間を新しいサーバー フォルダーの場所に指すようにすることができます。 DFS-N のデプロイがなく、Windows サーバーがインストールされた 8100 8600 アプライアンスをローカルに配置した場合、そのサーバーをドメインから削除し、AFS を使用して新しい Windows サーバーをドメインに参加させ、古いサーバーと同じサーバー名を付け、同じ共有名を付けることができます。また、新しいサーバーへのカットオーバーは、ユーザー、グループ ポリシー、またはスクリプトに対して透過的なままです。

## <a name="phase-7-deprovision"></a>フェーズ 7: プロビジョニング解除

先ほどのフェーズでは、複数のボリューム クローンを反復処理し、StorSimple アプライアンスをオフラインにした後で、最終的にユーザー アクセスを新しい Windows Server へカットオーバーできるようになりました。

これで、不要なリソースのプロビジョニング解除を開始できるようになりました。
始める前に、運用環境の新しい Azure File Sync のデプロイを少し観察することをお勧めします。 こうすることで、発生する可能性のある問題を修正できる選択肢がわかります。

AFS のデプロイに問題がないことを確認し、少なくとも数日間観察したら、リソースのプロビジョニング解除を次の順序で開始できます。

1. ファイルの同期を介してボリューム クローンから Azure ファイル共有にデータを移行するために使用した Azure VM をオフにします。
2. Azure の ストレージ同期サービス リソースに移動し、Azure VM の登録を解除します。 これで、すべての同期グループから削除されます。

    > [!WARNING]
    > **適切なマシンを選択していることを確認してください。** クラウド VM をオフにしました。つまり、登録済みサーバーの一覧で唯一のオフライン サーバーとして表示されます。 この手順では、オンプレミスの Windows Server を選択しないでください。選択すると、登録が解除されます。

3. Azure VM とそのリソースを削除します。
4. 8020 仮想 StorSimple アプライアンスを無効にします。
5. Azure 内のすべての StorSimple リソースのプロビジョニングを解除します。
6. StorSimple 物理アプライアンスをデータ センターから取り外します。

移行が完了しました。

## <a name="next-steps"></a>次のステップ

Azure File Sync の詳細を確認してください。特に、クラウドを使った階層化ポリシーの柔軟性があります。

Azure portal で、または以前のイベントで、一部のファイルが完全に同期していないことがわかった場合は、その問題を解決する手順についてトラブルシューティング ガイドを確認してください。

* [Azure File Sync の概要: aka.ms/AFS](https://aka.ms/AFS)
* [クラウドを使った階層化](storage-sync-cloud-tiering.md) 
* [Azure File Sync のトラブルシューティング ガイド](storage-sync-files-troubleshoot.md)
