---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125267"
---
Azure Virtual Machines (VM) はさまざまな状態に変化し、それらは "*プロビジョニング状態*" と "*電源の状態*" に分類することができます。 この記事の目的は、各状態について説明し、ユーザーがインスタンスの使用料金を課金されるタイミングを具体的に明示することです。 

## <a name="power-states"></a>電源状態

電源の状態は、最後に検出された VM の状態を表します。

![VM の電源の状態ダイアグラム](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
インスタンスの各状態の説明と、インスタンスの使用料金を課金されるかどうかを、次の表に示します。

<table>
<tr>
<th>
状態
</th>
<th>
説明
</th>
<th>
インスタンスの使用料金の課金
</th>
</tr>
<tr>
<td>
<p><b>開始中</b></p>
</td>
<td>
<p>VM を起動中です。</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>課金されません</b></p>
</td>
</tr>
<tr>
<td>
<p><b>実行中</b></p>
</td>
<td>
<p>VM の通常の動作状態です</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>課金されます</b></p>
</td>
</tr>
<tr>
<td>
<p><b>停止中</b></p>
</td>
<td>
<p>これは過渡的な状態です。 完了すると、**Stopped** と表示されます。</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>課金されます</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Stopped</b></p>
</td>
<td>
<p>ゲスト OS 内から、または PowerOff API によって、VM がシャットダウンされています。</p>
<p>ハードウェアは引き続き VM に割り当てられ、VM はホスト上に残ります。 </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>課金されます&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>割り当て解除中</b></p>
</td>
<td>
<p>過渡的な状態です。 完了すると、VM は**割り当て解除済み**と表示されます。</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>課金されません&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>割り当て解除済み</b></p>
</td>
<td>
<p>VM は正常に停止され、ホストから削除されました。 </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>課金されません</b></p>
</td>
</tr>
</tbody>
</table>


&#42;ディスクやネットワークなど、一部の Azure リソースに対しては料金が発生します。 インスタンスのソフトウェア ライセンスには料金は発生しません。

## <a name="provisioning-states"></a>プロビジョニング状態

プロビジョニング状態とは、VM 上でユーザーが開始したコントロールプレーン操作の状態のことです。 これらの状態は、VM の電源の状態から独立しています。

- **作成** – VM の作成。

- **更新** – 既存の VM のモデルを更新します。 また、開始/再起動などの VM に対する非モデル変更も、更新に該当します。

- **削除** – VM の削除。

- **割り当て解除** – VM が停止され、ホストから削除された場所です。 VM の割り当て解除は更新と見なされるため、更新に関連するプロビジョニング状態が表示されます。



ユーザー開始のアクションをプラットフォームが受け入れた後の過渡的な操作状態を次に示します。

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>状態</b></p>
</td>
<td width="366">
<p>説明</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>作成中</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>更新中</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>削除中</b>。</p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>OS のプロビジョニング状態</b></p>
</td>
<td width="366">
<p>特殊化されたイメージではなく OS イメージを使用して VM が作成される場合は、次の下位状態を確認できます。</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; VM が実行中で、ゲスト OS のインストールが進行中です。 <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; 短期間の状態。 何らかの拡張機能をインストールする必要が生じない限り、VM の状態はすぐに**成功**に遷移します。 拡張機能のインストールには時間がかかる場合があります。 <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>メモ</b>:OS でエラーが発生するか、時間内に OS をインストールできない場合、OS のプロビジョニングが**失敗**に移行する場合があります。 お客様は、インフラストラクチャ上にデプロイした VM に対して課金されます。</p>
</td>
</tr>
</table>


操作が完了すると、VM は次の状態のいずれかに移行します。

- **成功** – ユーザーが開始したアクションが完了しました。

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **失敗** – 操作が失敗したことを表します。 詳細と考えられる解決策については、エラー コードを参照してください。

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>VM インスタンス ビュー

インスタンス ビュー API によって、VM の実行状態に関する情報が提供されます。 詳細については、「[Virtual Machines - Instance View](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview)」(仮想マシン - インスタンス ビュー) の API ドキュメントをご覧ください。

Azure リソース エクスプローラーは、VM の実行状態を表示するためのシンプルな UI を備えています ([リソース エクスプローラー](https://resources.azure.com/))。

プロビジョニング状態は、VM のプロパティとインスタンス ビューから確認できます。 電源の状態は、VM のインスタンス ビューから確認できます。 

