---
title: Azure Data Box Gateway デバイスを監視する | Microsoft Docs
description: Azure portal とローカル Web UI を使用して Azure Data Box Gateway を監視する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: ed05f3d60f8ba4fbb06327136c7a117ae1d1d2db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581129"
---
# <a name="monitor-your-azure-data-box-gateway"></a>Azure Data Box Gateway を監視する

この記事では、Azure Data Box Gateway を監視する方法について説明します。 このデバイスは Azure portal またはローカル Web UI を使用して監視できます。 デバイスのイベントを確認したり、アラートを構成して管理したり、メトリックを確認したりするには、Azure portal を使用します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * デバイス イベントとそれに対応するアラートを確認する
> * デバイスの容量とトランザクションのメトリックを確認する
> * アラートを構成して管理する

## <a name="view-device-events"></a>デバイスのイベントを確認する

[!INCLUDE [data-box-gateway-view-device-events](../../includes/data-box-gateway-view-device-events.md)]

## <a name="view-metrics"></a>メトリックを表示する

[!INCLUDE [data-box-gateway-view-metrics](../../includes/data-box-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>デバイスのメトリック

このセクションでは、デバイスの監視メトリックについて説明します。 次のメトリックを使用できます。

* 容量メトリック。 容量メトリックは、デバイスの容量に関連しています。

* トランザクション メトリック。 トランザクション メトリックは、Azure Storage に対するすべての読み取り操作と書き込み操作に関連しています。

次の表に、メトリックの全一覧を示します。

|容量メトリック                     |説明  |
|-------------------------------------|-------------|
|**使用可能な容量**               | デバイスに書き込むことができるデータのサイズを示します。 つまり、これはデバイス上で使用できる容量です。 <br></br>デバイスとクラウドの両方にコピーがあるファイルのローカル コピーを削除することで、デバイスの容量を解放できます。        |
|**合計容量**                   | データを書き込むデバイス上の合計バイト数を示します。 これは、ローカル キャッシュの合計サイズとも呼ばれます。 <br></br> データ ディスクを追加することで、既存の仮想デバイスの容量を増やすことができるようになりました。 VM のハイパーバイザー管理からデータ ディスクを追加した後、ご自分の VM を再起動します。 新しく追加されたデータ ディスクに合わせて、ゲートウェイ デバイスのローカル記憶域プールが拡張されます。 <br></br>詳細については、[Hyper-V 仮想マシンのハード ドライブの追加](https://www.youtube.com/watch?v=EWdqUw9tTe4)に関する動画を参照してください。 |

|トランザクション メトリック              | 説明         |
|-------------------------------------|---------|
|**Cloud bytes uploaded (device) \(クラウドのアップロードされたバイト数 (デバイス)\)**    | ご自分のデバイス上にあるすべての共有からアップロードされたすべてのバイト数の合計        |
|**Cloud bytes uploaded (share) \(クラウドのアップロードされたバイト数 (共有)\)**     | 共有あたりのアップロードされたバイト数。 これは以下のマシンで行えます。 <br></br> 平均。これは、(共有あたりのアップロードされたすべてのバイト数の合計 / 共有の数) です  <br></br>最大。これは、1 つの共有からアップロードされたバイトの最大数です <br></br>最小。これは、1 つの共有からアップロードされたバイトの最小数です      |
|**Cloud download throughput (share) \(クラウドのダウンロード スループット (共有)\)**| 共有あたりのダウンロードされたバイト数。 これは以下のマシンで行えます。 <br></br> 平均。これは、(1 つの共有に読み取られた、またはダウンロードされたすべてのバイト数の合計 / 共有の数) です <br></br> 最大。これは、1 つの共有にダウンロードされたバイトの最大数です<br></br> 最小。これは、1 つの共有にダウンロードされたバイトの最小数です  |
|**Cloud read throughput \(クラウドの読み取りスループット\)**            | クラウドからご自分のデバイス上にあるすべての共有に読み取られたすべてのバイト数の合計     |
|**Cloud upload throughput \(クラウドへのアップロード時のスループット\)**          | ご自分のデバイス上にあるすべての共有からクラウドに書き込まれたすべてのバイト数の合計     |
|**Cloud upload throughput (share) \(クラウドへのアップロード時のスループット (共有)\)**  | (共有からクラウドに書き込まれたすべてのバイト数の合計 / 共有の数) が共有あたりの平均で、最大と最小もあります      |
|**Read throughput (network) \(読み取りスループット (ネットワーク)\)**           | クラウドから読み取られたすべてのバイトのシステム ネットワーク スループットが含まれます。 このビューには、共有に限定されないデータが含まれることがあります。 <br></br>分割すると、デバイス上にあるすべてのネットワーク アダプターのトラフィックが表示されます。 これには、接続されていないアダプターと有効にされていないアダプターが含まれます。      |
|**Write throughput (network) \(書き込みスループット (ネットワーク)\)**       | クラウドに書き込まれたすべてのバイトのシステム ネットワーク スループットが含まれます。 このビューには、共有に限定されないデータが含まれることがあります。 <br></br>分割すると、デバイス上にあるすべてのネットワーク アダプターのトラフィックが表示されます。 これには、接続されていないアダプターと有効にされていないアダプターが含まれます。          |

## <a name="manage-alerts"></a>Manage alerts

[!INCLUDE [data-box-gateway-manage-alerts](../../includes/data-box-gateway-manage-alerts.md)]

## <a name="next-steps"></a>次のステップ

[帯域幅を管理する](data-box-gateway-manage-bandwidth-schedules.md)方法について学習します。
