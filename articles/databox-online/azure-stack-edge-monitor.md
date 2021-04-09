---
title: Azure Stack Edge Pro デバイスを監視する | Microsoft Docs
description: Azure portal とローカル Web UI を使用して Azure Stack Edge Pro を監視する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: aae64cad3603725a4062d5afb42df974bbf8ac40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438790"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro を監視する

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

この記事では、Azure Stack Edge Pro を監視する方法について説明します。 このデバイスは Azure portal またはローカル Web UI を使用して監視できます。 デバイスのイベントを確認したり、アラートを構成して管理したり、メトリックを確認したりするには、Azure portal を使用します。 さまざまなデバイス コンポーネントのハードウェアの状態を確認するには、物理デバイスのローカル Web UI を使用します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * デバイス イベントとそれに対応するアラートを確認する
> * デバイス コンポーネントのハードウェアの状態を確認する
> * デバイスの容量とトランザクションのメトリックを確認する

## <a name="view-device-events"></a>デバイスのイベントを確認する

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>ハードウェアの状態を確認する

デバイス コンポーネントのハードウェアの状態を確認するには、ローカル Web UI から次の手順を実行します。

1. お使いのデバイスのローカル Web UI に接続します。
2. **[メンテナンス] > [ハードウェアの状態]** に移動します。 さまざまなデバイス コンポーネントの正常性を確認できます。

    ![ハードウェアの状態を確認する](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>メトリックを表示する

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>デバイスのメトリック

このセクションでは、デバイスの監視メトリックについて説明します。 次のメトリックを使用できます。

* 容量メトリック。 容量メトリックは、デバイスの容量に関連しています。

* トランザクション メトリック。 トランザクション メトリックは、Azure Storage に対するすべての読み取り操作と書き込み操作に関連しています。

* Edge コンピューティング メトリック。 Edge コンピューティング メトリックは、デバイスでの Edge コンピューティングの使用に関連しています。

次の表に、メトリックの全一覧を示します。

|容量メトリック                     |説明  |
|-------------------------------------|-------------|
|**使用可能な容量**               | デバイスに書き込むことができるデータのサイズを示します。 つまり、このメトリックはデバイス上で使用できる容量です。 <br></br>デバイスとクラウドの両方にコピーがあるファイルのローカル コピーを削除することで、デバイスの容量を解放できます。        |
|**合計容量**                   | データを書き込むデバイス上の合計バイト数を示します。これは、ローカル キャッシュの合計サイズとも呼ばれます。 <br></br> データ ディスクを追加することで、既存の仮想デバイスの容量を増やすことができるようになりました。 VM のハイパーバイザー管理からデータ ディスクを追加した後、ご自分の VM を再起動します。 新しく追加されたデータ ディスクに合わせて、ゲートウェイ デバイスのローカル記憶域プールが拡張されます。 <br></br>詳細については、[Hyper-V 仮想マシンのハード ドライブの追加](https://www.youtube.com/watch?v=EWdqUw9tTe4)に関する動画を参照してください。 |

|トランザクション メトリック              | 説明         |
|-------------------------------------|---------|
|**Cloud bytes uploaded (device) \(クラウドのアップロードされたバイト数 (デバイス)\)**    | ご自分のデバイス上にあるすべての共有からアップロードされたすべてのバイト数の合計        |
|**Cloud bytes uploaded (share) \(クラウドのアップロードされたバイト数 (共有)\)**     | 共有あたりのアップロードされたバイト数。 このメトリックは次のいずれかになります。 <br></br> 平均。これは、(共有あたりのアップロードされたすべてのバイト数の合計 / 共有の数) です  <br></br>最大。これは、1 つの共有からアップロードされたバイトの最大数です <br></br>最小。これは、1 つの共有からアップロードされたバイトの最小数です      |
|**Cloud download throughput (share) \(クラウドのダウンロード スループット (共有)\)**| 共有あたりのダウンロードされたバイト数。 このメトリックは次のいずれかになります。 <br></br> 平均。これは、(1 つの共有に読み取られた、またはダウンロードされたすべてのバイト数の合計 / 共有の数) です <br></br> 最大。これは、1 つの共有にダウンロードされたバイトの最大数です<br></br> 最小。これは、1 つの共有にダウンロードされたバイトの最小数です  |
|**Cloud read throughput \(クラウドの読み取りスループット\)**            | クラウドからご自分のデバイス上にあるすべての共有に読み取られたすべてのバイト数の合計     |
|**Cloud upload throughput \(クラウドへのアップロード時のスループット\)**          | ご自分のデバイス上にあるすべての共有からクラウドに書き込まれたすべてのバイト数の合計     |
|**Cloud upload throughput (share) \(クラウドへのアップロード時のスループット (共有)\)**  | (共有からクラウドに書き込まれたすべてのバイト数の合計 / 共有の数) が共有あたりの平均で、最大と最小もあります      |
|**Read throughput (network) \(読み取りスループット (ネットワーク)\)**           | クラウドから読み取られたすべてのバイトのシステム ネットワーク スループットが含まれます。 このビューには、共有に限定されないデータが含まれることがあります。 <br></br>分割は、デバイス上のすべてのネットワーク アダプターのトラフィックが示されます。これには、接続されていない、または有効になっていないアダプターも含まれます。      |
|**Write throughput (network) \(書き込みスループット (ネットワーク)\)**       | クラウドに書き込まれたすべてのバイトのシステム ネットワーク スループットが含まれます。 このビューには、共有に限定されないデータが含まれることがあります。 <br></br>分割は、デバイス上のすべてのネットワーク アダプターのトラフィックが示されます。これには、接続されていない、または有効になっていないアダプターも含まれます。          |

| Edge コンピューティング メトリック              | 説明         |
|-------------------------------------|---------|
|**Edge コンピューティング - メモリ使用量**      |           |
|**Edge コンピューティング - CPU 使用率**    |         |

## <a name="next-steps"></a>次のステップ

[帯域幅を管理する](azure-stack-edge-manage-bandwidth-schedules.md)方法について学習します。
[デバイス イベントのアラート通知を管理する](azure-stack-edge-gpu-manage-device-event-alert-notifications.md)方法について説明します。
