---
title: Azure 仮想マシン スケール セット インスタンスの通知を終了する
description: Azure 仮想マシン スケール セット インスタンスの終了通知を有効にする方法について説明します
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: c4d6de1b3406e6d82bdac5ff9b5c72a2286da988
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747742"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Azure 仮想マシン スケール セット インスタンスの通知を終了する
スケール セット インスタンスでは、インスタンスの終了通知を受信し、定義済みの遅延タイムアウトを終了操作に設定することをオプトインできます。 終了通知は、Azure Metadata Service の [Scheduled Events](../virtual-machines/windows/scheduled-events.md) を介して送信されます。これにより、再起動や再デプロイなどの影響がある操作の通知と遅延が行われます。 このソリューションでは、別のイベント (Terminate) が Scheduled Events の一覧に追加されます。terminate イベントの関連する遅延は、スケール セット モデルの構成のユーザーによって指定された遅延の制限によって変わります。

この機能に登録されると、スケール セット インスタンスは、インスタンスの削除前に指定されたタイムアウトが期限切れになるまで待つ必要がなくなります。 終了通知を受け取った後、インスタンスでは、終了のタイムアウトが期限切れになる前にいつでも削除することを選択できます。

## <a name="enable-terminate-notifications"></a>終了通知を有効にする
スケール セットのインスタンスで終了通知を有効にするには、次の例で説明するように複数の方法があります。

### <a name="azure-portal"></a>Azure portal

次の手順では、新しいスケール セットを作成するときに終了通知を有効にします。 

1. **[仮想マシン スケール セット]** に移動します。
1. **[+ 追加]** を選択して、新しいスケール セットを作成します。
1. **[管理]** タブに移動します。 
1. **[インスタンスの終了]** セクションを見つけます。
1. **[インスタンスの終了通知]** で、 **[オン]** を選択します。
1. **[終了の遅延 (分)]** で、必要な既定のタイムアウトを設定します。
1. 新しいスケール セットの作成が完了したら、 **[確認と作成]** ボタンを選択します。 

> [!NOTE]
> Azure portal で既存のスケール セットに終了通知を設定することはできません

### <a name="rest-api"></a>REST API

次の例では、スケール セット モデルで終了通知を有効にします。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

上のブロックでは、スケール セット内のすべてのインスタンスに対する任意の終了操作について、(*PT5M* で示されているように) タイムアウトの遅延が 5 分間に指定されています。 フィールド *notBeforeTimeout* は、ISO 8601 形式の 5 から 15 分の範囲の任意の値を受け取ることができます。 前述の *terminateNotificationProfile* の *notBeforeTimeout* プロパティを変更することで、終了操作の既定のタイムアウトを変更できます。

スケール セット モデルで *scheduledEventsProfile* を有効にし、*notBeforeTimeout* を設定してから、変更を反映するように個々のインスタンスを [最新のモデル](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)に更新します。

> [!NOTE]
>スケール セット インスタンスの終了通知は、API バージョン 2019-03-01 以降でのみ有効にすることができます

### <a name="azure-powershell"></a>Azure PowerShell
新しいスケール セットを作成するときに、[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) コマンドレットを使用して、スケール セットの終了通知を有効にすることができます。

このサンプル スクリプトでは、構成ファイルを使用したスケール セットと関連リソースの作成について説明します。[完全な仮想マシン スケール セットを作成します](./scripts/powershell-sample-create-complete-scale-set.md)。 スケール セットを作成するための構成オブジェクトに *TerminateScheduledEvents* および *TerminateScheduledEventNotBeforeTimeoutInMinutes* パラメーターを追加することにより、終了通知の構成を行うことができます。 次の例では、10 分の遅延タイムアウトでこの機能を有効にします。

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

[Update-AzVmss](/powershell/module/az.compute/update-azvmss) コマンドレットを使用して、既存のスケール セットに対する終了通知を有効にします。

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
上の例では、既存のスケール セットに対して終了通知を有効にし、terminate イベントに 15 分のタイムアウトを設定します。

スケール セット モデルでスケジュール化されたイベントを有効にし、タイムアウトを設定した後、変更内容を反映するように個々のインスタンスを[最新のモデル](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)に更新します。

### <a name="azure-cli-20"></a>Azure CLI 2.0

次の例は、新しいスケール セットの作成中に終了通知を有効にするためのものです。

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

上の例では、最初にリソース グループを作成し、次に 10 分の既定のタイムアウトで終了通知を有効にして新しいスケール セットを作成します。

次の例は、既存のスケール セットで終了通知を有効にするためのものです。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>終了通知を取得する

終了通知は、Azure Metadata Service である [Scheduled Events](../virtual-machines/windows/scheduled-events.md) を介して配信されます。 Azure Metadata Service では、VM 内部からアクセスできる REST エンドポイントを使用した Virtual Machine の実行に関する情報が公開されます。 情報は、VM の外部に公開されないように、ルーティング不可能な IP 経由で提供されます。

Scheduled Events は、ユーザーが初めてイベントを要求したときに、スケール セットに対して有効になります。 最初の呼び出しでは、最大 2 分の応答遅延が発生すると予想できます。 定期的にエンドポイントにクエリを実行し、今後のメンテナンス イベントと進行中のメンテナンス アクティビティの状態を検出します。

スケール セット インスタンスで要求が 24 時間行われない場合、スケール セットの Scheduled Events は無効になります。

### <a name="endpoint-discovery"></a>エンドポイントの検出
VNET が有効な VM の場合は、静的でルーティング不可能な IP アドレス 169.254.169.254 から Metadata Service を利用できます。

スケジュールされたイベントの最新バージョンのフル エンドポイントは次のとおりです。
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>クエリ応答
応答には、スケジュールされたイベントの配列が含まれています。 空の配列は、現在スケジュールされているイベントがないことを意味します。

スケジュールされたイベントがある場合は、応答にイベントの配列が含まれます。 "Terminate" イベントの場合、応答は次のようになります。
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentIncarnation* は ETag であり、前回のクエリ以降にイベントのペイロードが変化したかどうかを簡単に検査する方法を提供します。

上の各フィールドの詳細については、[Windows](../virtual-machines/windows/scheduled-events.md#event-properties) および [Linux](../virtual-machines/linux/scheduled-events.md#event-properties) の Scheduled Events ドキュメントを参照してください。

### <a name="respond-to-events"></a>イベントに応答する
今後予定されているイベントを確認し、グレースフル シャットダウンのロジックを完了すると、EventId のメタデータ サービスに POST 呼び出しを行うことにより、未処理のイベントを承認できます。 POST 呼び出しでは、VM の削除を続行できることが Azure に示されます。

POST 要求の本文には、次のような json が含まれます。 要求には、StartRequests の一覧が含まれています。 各 StartRequest には、高速化するイベントの EventId が含まれています。
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

スケール セット内のすべての VM が、その VM に関連する EventID のみを承認していることを確認します。 VM は、[インスタンス メタデータ](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)を介して自身の VM 名を取得できます。 この名前の形式は "{scale-set-name}_{instance-id}" であり、上記のクエリ応答の "Resources" セクションに表示されます。

[Python](../virtual-machines/linux/scheduled-events.md#python-sample) を使用してイベントに対してクエリを実行し応答するサンプル スクリプトを参照することもできます。

## <a name="tips-and-best-practices"></a>ヒントとベスト プラクティス
-   "削除" 操作に対して通知を終了する - スケール セットで *scheduledEventsProfile* が有効な場合は、すべての削除操作 (手動の削除または自動スケーリングによって開始されたスケールイン) で Terminate イベントが生成されます。 再起動、再イメージ化、再デプロイ、終了/割り当て解除などの他の操作では、Terminate イベントは生成されません。 優先順位の低い VM に対して終了通知を有効にすることはできません。
-   タイムアウトを待機する必要なし - イベントが受信された後、イベントの *NotBefore* 時間が期限切れになる前であればいつでも終了操作を開始できます。
-   タイムアウト時の削除は必須 - イベントの生成後にタイムアウト値を延長する機能は提供されません。 タイムアウトが期限切れになると、保留中の terminate イベントが処理され、VM が削除されます。
-   変更可能なタイムアウト値 - スケール セット モデルの *notBeforeTimeout* プロパティを変更し、VM インスタンスを最新のモデルに更新することで、インスタンスが削除される前にいつでもタイムアウト値を変更できます。
-   保留中のすべての削除を承認する - 承認されていない VM_1 に保留中の削除があり、VM_2 で別の terminate イベントを承認した場合は、VM_1 の terminate イベントが承認されるか、タイムアウトが経過するまで、VM_2 は削除されません。 VM_1 の terminate イベントを承認すると、VM_1 と VM_2 の両方が削除されます。
-   すべての同時削除を承認する - 上記の例を拡張します。VM_1 と VM_2 の *NotBefore* 時間が同じである場合、両方の terminate イベントを承認するか、タイムアウトの期限が切れる前にどちらの VM も削除する必要があります。

## <a name="troubleshoot"></a>トラブルシューティング
### <a name="failure-to-enable-scheduledeventsprofile"></a>ScheduledEventsProfile を有効にできない
"タイプ 'VirtualMachineProfile' のオブジェクトにメンバー 'scheduledEventsProfile' が見つかりませんでした" というエラー メッセージの "BadRequest" エラーが発生する場合は、スケール セットの操作に使用されている API バージョンを確認してください。 コンピューティング API バージョン **2019-03-01** 以降が必要です。 

### <a name="failure-to-get-terminate-events"></a>Terminate イベントを取得できない
Scheduled Events を介して **Terminate** イベントを取得していない場合は、イベントの取得に使用された API バージョンを確認します。 Terminate イベントには Metadata Service API バージョン **2019-01-01** 以降が必要です。
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>無効な NotBefore 時間での Terminate イベントを受け取る  
スケール セット モデルで *scheduledEventsProfile* を有効にし、*notBeforeTimeout* を設定してから、変更を反映するように個々のインスタンスを [最新のモデル](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)に更新します。

## <a name="next-steps"></a>次のステップ
仮想マシン スケール セットに[ご自身のアプリケーションをデプロイする](virtual-machine-scale-sets-deploy-app.md)方法を学習します。
