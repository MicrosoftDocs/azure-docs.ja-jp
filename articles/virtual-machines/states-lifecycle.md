---
title: Azure における VM のライフサイクルと状態
description: Azure における VM のライフサイクルの概要 (VM の任意の時点におけるさまざまな状態の説明を含む)。
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: cd104a31fdba932481889db198ae3cc4998a4466
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261890"
---
# <a name="virtual-machines-lifecycle-and-states"></a>仮想マシンのライフサイクルと状態

Azure Virtual Machines (VM) はさまざまな状態に変化し、それらは "*プロビジョニング状態*" と "*電源の状態*" に分類することができます。 この記事の目的は、各状態について説明し、ユーザーがインスタンスの使用料金を課金されるタイミングを具体的に明示することです。 

## <a name="power-states"></a>電源状態

電源の状態は、最後に検出された VM の状態を表します。

![VM の電源の状態ダイアグラム](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
インスタンスの各状態の説明と、インスタンスの使用料金を課金されるかどうかを、次の表に示します。

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="":::

   **説明**

   :::column-end:::
   :::column span="":::

   **課金されたインスタンスの使用**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **開始中**

   :::column-end:::
   :::column span="":::

   VM を起動中です。

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **課金されません**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **実行中**

   :::column-end:::
   :::column span="":::

   VM の通常の動作状態です

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **課金されます**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **停止中**

   :::column-end:::
   :::column span="":::

   これは過渡的な状態です。 完了すると、**Stopped** と表示されます。

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **課金されます**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **停止**

   :::column-end:::
   :::column span="":::

   ゲスト OS 内から、または PowerOff API によって、VM がシャットダウンされています。

   ハードウェアは引き続き VM に割り当てられ、VM はホスト上に残ります。

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **課金されます***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **割り当て解除中**

   :::column-end:::
   :::column span="":::

   過渡的な状態です。 完了すると、VM は**割り当て解除済み**と表示されます。

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **課金されません***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **割り当て解除済み**

   :::column-end:::
   :::column span="":::

   VM は正常に停止され、ホストから削除されました。

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **課金されません**

   :::column-end:::
:::row-end:::


&#42;ディスクやネットワークなど、一部の Azure リソースでは料金が発生します。 インスタンスのソフトウェア ライセンスには料金は発生しません。

## <a name="provisioning-states"></a>プロビジョニング状態

プロビジョニング状態とは、VM 上でユーザーが開始したコントロールプレーン操作の状態のことです。 これらの状態は、VM の電源の状態から独立しています。

- **作成** – VM の作成。

- **更新** – 既存の VM のモデルを更新します。 また、開始/再起動などの VM に対する非モデル変更も、更新に該当します。

- **削除** – VM の削除。

- **割り当て解除** – VM が停止され、ホストから削除された場所です。 VM の割り当て解除は更新と見なされるため、更新に関連するプロビジョニング状態が表示されます。



ユーザー開始のアクションをプラットフォームが受け入れた後の過渡的な操作状態を次に示します。

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="2":::

   **説明**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **作成**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **更新中**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **削除中**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OS のプロビジョニング状態**
   
   :::column-end:::
   :::column span="2":::

   **説明**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   特殊化されたイメージではなく OS イメージを使用して VM が作成される場合は、次の下位状態を確認できます。

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   VM が実行中で、ゲスト OS のインストールが進行中です。
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   有効期間の短い状態。 何らかの拡張機能をインストールする必要が生じない限り、VM の状態はすぐに**成功**に遷移します。 拡張機能のインストールには時間がかかる場合があります。
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **注**: OS でエラーが発生するか、時間内に OS をインストールできない場合、OS のプロビジョニングが**失敗**に移行する場合があります。 お客様は、インフラストラクチャ上にデプロイした VM に対して課金されます。

   :::column-end:::

:::row-end:::

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

自分のサブスクリプション内のすべての VM の電源状態を取得するには、**statusOnly** パラメーターを *true* に設定して[Virtual Machines の List All API](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) を使用します。

## <a name="next-steps"></a>次のステップ

VM の監視について詳しくは、[Azure で仮想マシンを監視する方法](../azure-monitor/insights/monitor-vm-azure.md)に関するページをご覧ください。