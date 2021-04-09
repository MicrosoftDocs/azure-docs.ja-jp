---
title: Azure Stack Edge Pro FPGA から GPU 物理デバイスへの移行ガイド
description: このガイドでは、Azure Stack Edge Pro FPGA デバイスから Azure Stack Edge Pro GPU デバイスにワークロードを移行する手順について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 24d6528a105d593d1cb4c9c66d981c8787f85633
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573280"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro FPGA から Azure Stack Edge Pro GPU へのワークロードの移行

この記事では、Azure Stack Edge Pro FPGA デバイスから Azure Stack Edge Pro GPU デバイスにワークロードとデータを移行する方法について説明します。 移行プロセスは、2 つのデバイスの比較、移行計画、および移行に関する考慮事項の検討から開始します。 移行手順では、検証とデバイスのクリーンアップで終了する詳細な手順を実行します。

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>移行について

移行は、ある保存場所から別の保存場所にワークロードとアプリケーション データを移動するプロセスです。 これには、できればアクティブなアプリケーションを中断したり無効にしたりすることなく、組織の現在のデータをストレージ デバイス間で正確にコピーし、すべての入出力 (I/O) アクティビティを新しいデバイスにリダイレクトすることが必要となります。 

この移行ガイドでは、Azure Stack Edge Pro FPGA デバイスから Azure Stack Edge Pro GPU デバイスにデータを移行するために必要な手順について詳しく説明します。 このドキュメントは、データセンターで Azure Stack Edge デバイスの運用、展開、管理を担当する情報技術 (IT) プロフェッショナルとナレッジ ワーカーを対象としています。

この記事では、Azure Stack Edge Pro FPGA デバイスを "*ソース*" デバイス、Azure Stack Edge Pro GPU デバイスを "*ターゲット*" デバイスと呼びます。 

## <a name="comparison-summary"></a>比較の概要

このセクションでは、Azure Stack Edge Pro GPU デバイスと Azure Stack Edge Pro FPGA デバイスの機能の比較概要を示します。 ソースとターゲットの両デバイスのハードウェアはほぼ同じであり、異なるのは、ハードウェア アクセラレータ カードとストレージ容量に関してのみです。<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    機能  | Azure Stack Edge Pro GPU (ターゲット デバイス)  | Azure Stack Edge Pro FPGA (ソース デバイス)|
|----------------|-----------------------|------------------------|
| ハードウェア       | ハードウェア アクセラレータ: 1 つまたは 2 つの Nvidia T4 GPU <br> コンピューティング、メモリ、ネットワーク インターフェイス、電源装置、電源コードの仕様は、FPGA を搭載したデバイスと同じです。  | ハードウェア アクセラレータ: Intel Arria 10 FPGA <br> コンピューティング、メモリ、ネットワーク インターフェイス、電源装置、電源コードの仕様は、GPU を搭載したデバイスと同じです。          |
| 使用可能なストレージ | 4.19 TB <br> パリティの回復性と内部使用のための領域の予約後 | 12.5 TB <br> 内部使用のための領域の予約後 |
| セキュリティ       | 証明書 |                                                     |
| ワークロード      | IoT Edge ワークロード <br> VM ワークロード <br> Kubernetes ワークロード| IoT Edge ワークロード |
| 価格        | [料金](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [料金](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>移行計画

移行計画を作成するには、次の情報を考慮してください。

- 移行のスケジュールを作成します。 
- データの移行時に、ダウンタイムが発生する可能性があります。 プロセスが中断されるので、ダウンタイムのメンテナンス期間中に移行をスケジュールすることをお勧めします。 このドキュメントで後述するように、このダウンタイム中に構成を設定して復元します。
- ダウンタイムの合計時間を把握し、それをすべての関係者に伝えます。
- ソース デバイスから移行する必要があるローカル データを特定します。 念のため、既存のストレージの全データに最新のバックアップがあることを確認してください。 


## <a name="migration-considerations"></a>移行に関する注意事項 

移行を進める前に、次の情報を考慮してください。 

- Azure Stack Edge Pro GPU デバイスは、Azure Stack Edge Pro FPGA リソースに対してアクティブにすることはできません。 [Azure Stack Edge Pro GPU の注文の作成](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)に関するセクションの説明に従って、Azure Stack Edge Pro GPU デバイス用に新しいリソースを作成する必要があります。
- FPGA を使用していたソース デバイスにデプロイされた機械学習モデルは、GPU を搭載したターゲット デバイス用に変更する必要があります。 このモデルの詳細については、Microsoft サポートにお問い合わせください。 FPGA を使用していなかった (CPU のみを使用していた) ソース デバイスにデプロイされたカスタム モデルは、ターゲット デバイス (CPU を使用) でそのまま動作します。
- ソース デバイスにデプロイされた IoT Edge モジュールをターゲット デバイスに正常にデプロイできるようにするには、事前に変更が必要になる場合があります。 
- ソース デバイスでは、NFS 3.0 および 4.1 プロトコルがサポートされています。 ターゲット デバイスでは、NFS 3.0 プロトコルのみがサポートされています。
- ソース デバイスでは、SMB および NFS プロトコルがサポートされています。 ターゲット デバイスでは、共有用の SMB および NFS プロトコルに加えて、ストレージ アカウントを使用する際の REST プロトコル経由での保存もサポートされています。
- ソース デバイスでの共有アクセスには IP アドレスを使用しますが、ターゲット デバイスでの共有アクセスにはデバイス名を使用します。

## <a name="migration-steps-at-a-glance"></a>移行手順の概要

次の表は、移行の全体的なフローをまとめたものです。移行に必要な手順と、これらの手順を実行する場所を示しています。

| フェーズ | 実行する手順| 手順を実行するデバイス |
|---------------|-------------|----------------|
| ソース デバイスの準備*       | 1. 構成データを記録する <br>2. 共有のデータをバックアップする <br>3. IoT Edge ワークロードを準備する| ソース デバイス  |
| ターゲット デバイスの準備*       |1. 新しい注文を作成する <br>2. 構成してアクティブにする| ターゲット デバイス  |
| データの移行       | 1. 共有からデータを移行する <br>2. IoT Edge ワークロードを再デプロイする| ターゲット デバイス  |
| データの確認            |移行されたデータを確認する |ターゲット デバイス  |
| クリーンアップ、返却              |データを消去して返却する| ソース デバイス  |

*"*ソース デバイスとターゲット デバイスは並行して準備できます。* "

## <a name="prepare-source-device"></a>ソース デバイスの準備

準備には、Edge クラウド共有、Edge ローカル共有に加え、デバイスにデプロイされている IoT Edge モジュールの特定が含まれます。 

### <a name="1-record-configuration-data"></a>1. 構成データを記録する

これらの手順は、ローカル UI を使用してソース デバイスで実行します。

"*ソース*" デバイスで構成データを記録します。 [デプロイ チェックリスト](azure-stack-edge-gpu-deploy-checklist.md)を使用すると、デバイス構成の記録に役立ちます。 移行中に、この構成情報を使用して新しいターゲット デバイスを構成します。 

### <a name="2-back-up-share-data"></a>2. 共有のデータをバックアップする

デバイス データは次のいずれかの種類です。

- Edge クラウド共有内のデータ
- ローカル共有内のデータ

#### <a name="data-in-edge-cloud-shares"></a>Edge クラウド共有内のデータ

Edge クラウド共有では、デバイスから Azure にデータが階層化されます。 次の手順は、Azure portal を使用して "*ソース*" デバイスで実行します。 

- ソース デバイスのすべての Edge クラウド共有とユーザーのリストを作成します。
- 作成済みのすべての帯域幅スケジュールのリストを作成します。 これらの帯域幅スケジュールは、ターゲット デバイスで再作成します。
- 使用可能なネットワーク帯域幅に応じて、クラウドに階層化されるデータが最大化されるようにデバイスの帯域幅スケジュールを構成します。 これにより、デバイス上のローカル データが最小限に抑えられます。
- 共有がクラウドに完全に階層化されていることを確認します。 階層化は、Azure portal で共有の状態を調べることで確認できます。  

#### <a name="data-in-edge-local-shares"></a>Edge ローカル共有内のデータ

Edge ローカル共有内のデータはデバイスに残ります。 次の手順は、Azure portal を使用して "*ソース*" デバイスで実行します。 

- デバイスの Edge ローカル共有のリストを作成します。
- 1 回限りのデータの移行を行うため、Edge ローカル共有のデータのコピーを別のオンプレミス サーバーに作成します。 `robocopy` (SMB) や `rsync` (NFS) などのコピー ツールを使用してデータをコピーできます。 必要に応じて、ローカル共有内のデータをバックアップするために、サードパーティのデータ保護ソリューションを既にデプロイしている場合があります。 Azure Stack Edge Pro FPGA デバイスでの使用については、次のサードパーティ ソリューションがサポートされています。

    | サードパーティ製ソフトウェア           | ソリューションへの参照                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 詳細については、Cohesity にお問い合わせください。          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 詳細については、Commvault にお問い合わせください。          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 詳細については、Veritas にお問い合わせください。   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 詳細については、Veeam にお問い合わせください。 |


### <a name="3-prepare-iot-edge-workloads"></a>3. IoT Edge ワークロードを準備する

- IoT Edge モジュールのデプロイが完了し、FPGA アクセラレーションを使用している場合は、GPU デバイスで実行される前にモジュールを変更することが必要になる可能性があります。 [IoT Edge モジュールの変更](azure-stack-edge-gpu-modify-fpga-modules-gpu.md)に関するページの手順に従ってください。 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>ターゲット デバイスの準備

### <a name="1-create-new-order"></a>1. 新しい注文を作成する

"*ターゲット*" デバイスの新しい注文 (および新しいリソース) を作成する必要があります。 このターゲット デバイスは、FPGA リソースに対してではなく、GPU リソースに対してアクティブにする必要があります。

注文するには、Azure portal で[新しい Azure Stack Edge リソースを作成](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)します。


### <a name="2-set-up-activate"></a>2. セットアップしてアクティブにする

"*ターゲット*" デバイスをセットアップし、先ほど作成した新しいリソースに対してアクティブにする必要があります。 

次の手順に従って、Azure portal で "*ターゲット*" デバイスを構成します。

1. 「[展開のチェックリスト](azure-stack-edge-gpu-deploy-checklist.md)」で必要な情報を収集します。 ソース デバイスの構成から保存した情報を使用できます。 
1. [開梱](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)し、[ラックに取り付け](azure-stack-edge-gpu-deploy-install.md#rack-the-device)、[デバイスのケーブル接続](azure-stack-edge-gpu-deploy-install.md#cable-the-device)を行います。 
1. [デバイスのローカル UI に接続](azure-stack-edge-gpu-deploy-connect.md)します。
1. 古いデバイスに使用していたものとは異なる IP アドレスのセットを使用して (静的 IP を使用している場合) ネットワークを構成します。 [ネットワーク設定を構成する](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)方法を参照してください。
1. 古いデバイスと同じデバイス名を割り当て、DNS ドメインを指定します。 方法については、[デバイス設定の構成](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)に関する記事を参照してください。
1. 新しいデバイスで証明書を構成します。 方法については、[証明書の構成](azure-stack-edge-gpu-deploy-configure-certificates.md)に関する記事を参照してください。
1. Azure portal からアクティブ化キーを取得し、新しいデバイスをアクティブ化します。 方法については、[デバイスのアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関する記事を参照してください。

これで、共有のデータを復元し、古いデバイスで実行していたワークロードをデプロイする準備ができました。

## <a name="migrate-data"></a>データの移行

次に、ソース デバイスから "*ターゲット*" デバイス上の Edge クラウド共有と Edge ローカル共有にデータをコピーします。

### <a name="1-from-edge-cloud-shares"></a>1. Edge クラウド共有から

次の手順に従って、ターゲット デバイスで Edge クラウド共有のデータを同期します。

1. ソース デバイスに作成された共有名に対応する[共有を追加](azure-stack-edge-j-series-manage-shares.md#add-a-share)します。 共有を作成するときに、 **[BLOB コンテナーを選択]** が **[既存のものを使用]** に設定されていることを確認した後、前のデバイスで使用されていたコンテナーを選択します。
1. 以前のデバイスへのアクセス権を持っていた[ユーザーを追加](azure-stack-edge-j-series-manage-users.md#add-a-user)します。
1. Azure から[共有のデータを更新します](azure-stack-edge-j-series-manage-shares.md#refresh-shares)。 共有を更新することにより、既存のコンテナーから共有にすべてのクラウド データが取り込まれます。
1. 共有に関連付ける帯域幅スケジュールを再作成します。 詳細な手順については、[帯域幅スケジュールの追加](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule)に関するセクションを参照してください。


### <a name="2-from-edge-local-shares"></a>2. Edge ローカル共有から

ローカル共有にある IoT ワークロードのデータを保護するために、サードパーティのバックアップ ソリューションをデプロイしている場合があります。 ここで、そのデータを復元する必要があります。

交換用デバイスを完全に構成したら、ローカル ストレージ用にデバイスを有効にします。 

次の手順のようにして、ローカル共有からデータを回復します。

1. [デバイスでコンピューティングを構成します](azure-stack-edge-gpu-deploy-configure-compute.md)。
1. ターゲット デバイスですべてのローカル共有を追加します。 詳細な手順については、「[ローカル共有を追加する](azure-stack-edge-gpu-manage-shares.md#add-a-local-share)」を参照してください。
1. ソース デバイスでの SMB 共有へのアクセスには IP アドレスを使用しますが、ターゲット デバイスではデバイス名を使用します。 [Azure Stack Edge Pro GPU での SMB 共有への接続](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-smb-share)に関するセクションを参照してください。 ターゲット デバイスで NFS 共有に接続するには、そのデバイスに関連付けられた新しい IP アドレスを使用する必要があります。 [Azure Stack Edge Pro GPU での NFS 共有への接続](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-nfs-share)に関するセクションを参照してください。 

    SMB または NFS を介して、共有のデータを中間サーバーにコピーした場合は、中間サーバーからのデータをターゲット デバイス上の共有にコピーできます。 ソースとターゲットのデバイスが両方とも "*オンライン*" の場合は、ソース デバイスから直接データをコピーすることもできます。

    ローカル共有内のデータをバックアップするために、サードパーティ製ソフトウェアを使用している場合は、選択したデータ保護ソリューションに用意されている復旧手順を実行する必要があります。 次の表で参照先を確認してください。

    | サードパーティ製ソフトウェア           | ソリューションへの参照                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 詳細については、Cohesity にお問い合わせください。          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 詳細については、Commvault にお問い合わせください。 |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 詳細については、Veritas にお問い合わせください。   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 詳細については、Veeam にお問い合わせください。 |

### <a name="3-redeploy-iot-edge-workloads"></a>3. IoT Edge ワークロードを再デプロイする

IoT Edge モジュールの準備ができたら、IoT Edge ワークロードをターゲット デバイスにデプロイする必要があります。 IoT Edge モジュールのデプロイ時にエラーが発生した場合は、次を参照してください。

- 「[Azure IoT Edge での一般的な問題と解決](../iot-edge/troubleshoot-common-errors.md)」。 
- [IoT Edge ランタイム エラー](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors)。

## <a name="verify-data"></a>データの確認

移行後、すべてのデータが移行され、ワークロードがターゲット デバイスにデプロイされていることを確認します。

## <a name="erase-data-return"></a>データの消去、返却

データ移行が完了したら、ローカル データを消去し、ソース デバイスを返却します。 「[Azure Stack Edge Pro デバイスを返却する](azure-stack-edge-return-device.md)」の手順に従います。


## <a name="next-steps"></a>次の手順

[Azure Stack Edge Pro GPU デバイスに IoT Edge ワークロードをデプロイする方法を学習する](azure-stack-edge-gpu-deploy-compute-module-simple.md)
