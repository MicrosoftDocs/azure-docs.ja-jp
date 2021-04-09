---
title: Azure Stack Edge Pro Preview のリリース ノート | Microsoft Docs
description: プレビュー リリースが実行されている Azure Stack Edge Pro の重大な未解決の問題と解決策について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: dd72865e35318c7ff43dc17b7c92b9cc2f3e9790
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102436857"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>GPU 搭載の Azure Stack Edge Pro Preview のリリース ノート

[!INCLUDE [applies-to-Pro-GPU-sku](../../includes/azure-stack-edge-applies-to-gpu-sku.md)]

次のリリース ノートでは、GPU 搭載の Azure Stack Edge Pro デバイスの 2008 プレビュー リリースについて、重大な未解決の問題と解決済みの問題を説明します。

リリース ノートは継続的に更新されます。対応策を必要とする重大な問題が見つかった場合は、それらの問題が追加されます。 Azure Stack Edge Pro デバイスを配置する前に、リリース ノートに含まれている情報を注意深く確認してください。

この記事は、**Azure Stack Edge Pro 2008** ソフトウェア リリースに適用されます。

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>新機能

Azure Stack Edge 2008 リリースに、次の新機能が追加されました。 実行している特定のプレビュー ソフトウェアのバージョンによっては、これらの機能のサブセットが表示される場合があります。 

- **ストレージ クラス** - 以前のリリースでは、Azure Stack Edge Pro デバイスで実行されている Kubernetes クラスターにデプロイされたステートフル アプリケーションに対して、SMB または NFS 共有を介してストレージを静的にプロビジョニングすることのみができました。 このリリースでは、ストレージを動的にプロビジョニングできるストレージ クラスが追加されました。 詳細については、[Azure Stack Edge Pro GPU デバイスでの Kubernetes ストレージ管理](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning)に関するページを参照してください。 
- **Kubernetes ダッシュボードとメトリック サーバー** - このリリースでは、Kubernetes ダッシュボードがメトリック サーバー アドオンと共に追加されました。 ダッシュボードを使用して Azure Stack Edge Pro デバイス上で実行されているアプリケーションの概要を取得すること、Kubernetes クラスター リソースの状態を表示すること、デバイスで発生したエラーを確認することができます。 メトリック サーバーによって、デバイス上の Kubernetes リソース全体の CPU とメモリの使用量が集計されます。 詳細については、[Kubernetes ダッシュボードを使用した Azure Stack Edge Pro GPU デバイスの監視](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)に関するページを参照してください。
- **Azure Arc for Azure Stack Edge Pro** - このリリースから、Azure Arc を介して Azure Stack Edge Pro デバイスにアプリケーション ワークロードをデプロイできるようになりました。Azure Arc は、Kubernetes クラスターにアプリケーションをデプロイできるようにするためのハイブリッド管理ツールです。 詳細については、[Azure Arc Edge を介して Azure Stack Edge Pro デバイスにワークロードを展開する](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)方法に関するページを参照してください。  

## <a name="known-issues"></a>既知の問題 

Azure Stack Edge Pro デバイスの既知の問題の概要を次の表に示します。

| いいえ。 | 特徴量 | 問題 | 対応策/コメント |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | SQL Database を作成するには、管理者アクセス権が必要です。   |[https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) の手順 1 から 2 の代わりに、次の手順を実行します。 <ul><li>デバイスのローカル UI で、コンピューティング インターフェイスを有効にします。 **[コンピューティング] > [ポート番号] > [Enable for compute]\(コンピューティングを有効にする\) > [適用]** を選択します。</li><li>https://docs.microsoft.com/sql/tools/sqlcmd-utility からクライアント マシンに `sqlcmd` をダウンロードします </li><li>コンピューティング インターフェイスの IP アドレス (有効なポート) に接続し、アドレスの末尾に ",1401" を追加します。</li><li>最終的なコマンドは次のようになります。sqlcmd -S {Interface IP},1401 -U SA -P "Strong!Passw0rd"</li>この後、現在のドキュメントの手順 3 から 4 は同じにする必要があります。 </li></ul> |
| **2.** |更新| **[更新]** によって復元された BLOB の増分変更はサポートされていません |BLOB エンドポイントの場合、[更新] 後に BLOB を部分的に更新すると、更新がクラウドにアップロードされない可能性があります。 たとえば、次のような一連のアクションがあります。<ul><li>クラウドに BLOB を作成します。 または、以前にアップロードした BLOB をデバイスから削除します。</li><li>更新機能を使用して、クラウドからアプライアンスに BLOB を更新します。</li><li>Azure SDK REST API を使用して、BLOB の一部のみを更新します。</li></ul>これらのアクションの結果、BLOB の更新されたセクションがクラウドで更新されない可能性があります。 <br>**回避策**:robocopy などのツール、またはエクスプローラーやコマンド ラインを介した通常のファイル コピーを使用して、BLOB 全体を置き換えます。|
|**3.**|Throttling|調整中に、デバイスへの新しい書き込みが許可されていない場合、NFS クライアントによって行われた書き込みは "アクセス許可が拒否されました" エラーで失敗します。| 次のようなエラーが表示されます。<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: cannot create directory 'test': (mkdir: ディレクトリ 'test' を作成できません:)アクセス許可は拒否されました|
|**4.**|Blob Storage のインジェスト|Blob storage のインジェストに AzCopy バージョン 10 を使用する場合は、次の引数を指定して AzCopy を実行します。`Azcopy <other arguments> --cap-mbps 2000`| AzCopy にこれらの制限を指定しない場合、デバイスに大量の要求が送信され、サービスに問題が発生する可能性があります。|
|**5.**|階層型ストレージ アカウント|階層型ストレージ アカウントを使用する場合は、以下が適用されます。<ul><li> ブロック BLOB のみがサポートされています。 ページ BLOB はサポートされていません。</li><li>スナップショットまたはコピー API はサポートされていません。</li><li> `distcp` を使用した Hadoop ワークロードの取り込みは、コピー操作が頻繁に使用されるため、サポートされていません。</li></ul>||
|**6.**|NFS 共有接続|複数のプロセスによって同じ共有に対してコピーが実行され、`nolock` 属性が使用されていない場合、コピー中にエラーが発生する可能性があります。|ファイルを NFS 共有にコピーするには、`nolock` 属性を mount コマンドに渡す必要があります。 たとえば、 `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`と指定します。|
|**7.**|Kubernetes クラスター|Kubernetes クラスターを実行しているデバイスに更新プログラムを適用すると、Kubernetes 仮想マシンが再起動し、リブートされます。 このインスタンスでは、レプリカを指定して展開されたポッドのみが、更新後に自動的に復元されます。  |レプリケーション コントローラーの外部でレプリカ セットを指定せずに個別のポッドを作成した場合、これらのポッドはデバイスの更新後に自動的に復元されません。 これらのポッドを復元する必要があります。<br>レプリカ セットによって、ノードの障害や中断を伴うノードのアップグレードなど、何らかの理由で削除または中止されたポッドが置き換えられます。 このため、アプリケーションに必要なポッドが 1 つだけの場合でも、レプリカ セットを使用することをお勧めします。|
|**8.**|Kubernetes クラスター|Azure Stack Edge Pro 上の Kubernetes は、Helm v3 以降でのみサポートされています。 詳細については、「[Frequently asked questions: Removal of Tiller (よく寄せられる質問: Tiller の削除)](https://v3.helm.sh/docs/faq/)」を参照してください。|
|**9.**|Azure Arc + Azure Stack Edge Pro|Azure Stack Edge Pro デバイスで Web プロキシが構成されている場合、Azure Arc のデプロイはサポートされません。||
|**10.**|Kubernetes |ポート 31000 は Kubernetes ダッシュボード用に予約されています。 同様に、既定の構成では、IP アドレス 172.28.0.1 と 172.28.0.10 は、それぞれ Kubernetes サービスと Core DNS サービス用に予約されています。|予約済みの IP は使用しないでください。|
|**11.**|Kubernetes |現在、Kubernetes ではマルチプロトコルの LoadBalancer サービスは許可されていません。 たとえば、TCP と UDP の両方をリッスンする必要がある DNS サービスです。 |MetalLB を使用して Kubernetes のこの制限を回避するには、同じポッド セレクター用に 2 つのサービス (TCP 用と UDP 用) を作成します。 これらのサービスでは、同じ共有キーと spec.loadBalancerIP を使用して、同じ IP アドレスを共有します。 使用可能な IP アドレスよりも多くのサービスがある場合は、IP を共有することもできます。 <br> 詳細については、「[IP address sharing (IP アドレスの共有)](https://metallb.universe.tf/usage/#ip-address-sharing)」を参照してください。|
|**12.**|Kubernetes クラスター|既存の Azure IoT Edge マーケットプレース モジュールは、Azure Stack Edge デバイス上の IoT Edge 用ホスティング プラットフォームとしての Kubernetes クラスターでは実行されません。|モジュールは、Azure Stack Edge デバイスに展開する前に変更する必要があります。 詳細については、Azure Stack Edge デバイス上で実行するようにマーケットプレースの Azure IoT Edge モジュールを変更する方法に関するページを参照してください。<!-- insert link-->|
|**13.**|Kubernetes |ファイルベースのバインド マウントは、Azure Stack Edge デバイス上の Kubernetes の Azure IoT Edge ではサポートされていません。|IoT Edge では、変換レイヤーを使用して、`ContainerCreate` オプションを Kubernetes コンストラクトに変換します。 `Binds` を作成すると、`hostpath` ディレクトリまたは create にマップされます。そのため、ファイルベースのバインド マウントを IoT Edge コンテナーのパスにバインドすることはできません。|
|**14.**|Kubernetes |IoT Edge 用の独自の証明書を持ち込み、Azure Stack Edge デバイスにそれらを追加した場合、新しい証明書は Helm チャートの更新の一部として取得されません。|この問題を回避するには、[デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md)。 `iotedged` および `edgehub` ポッドを再起動します。|
|**15.**|証明書 |場合によっては、ローカル UI の証明書の状態が更新されるまでに数秒かかることがあります。 |ローカル UI の次のシナリオが影響を受ける可能性があります。<ul><li>**[証明書]** ページの **[状態]** 列。</li><li>**[作業の開始]** ページの **[セキュリティ]** タイル。</li><li>**[概要]** ページの **[構成]** タイル。</li></ul>  |

## <a name="next-steps"></a>次のステップ

- [GPU 搭載の Azure Stack Edge Pro デバイスの配置を準備する](azure-stack-edge-gpu-deploy-prep.md)