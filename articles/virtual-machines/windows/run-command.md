---
title: Azure 内の Windows VM で PowerShell スクリプトを実行する
description: このトピックでは、実行コマンド機能を使用して Azure Windows 仮想マシン内で PowerShell スクリプトを実行する方法について説明します
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: e2cd8ee4095db235215a2beaa68975e819b474c1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560686"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>実行コマンドを使用して Windows VM で PowerShell スクリプトを実行する

実行コマンド機能は、仮想マシン (VM) エージェントを使用して Azure Windows VM 内で PowerShell スクリプトを実行します。 これらのスクリプトは、マシンやアプリケーションの一般的な管理に使用できます。 これらを使用すれば、VM のアクセスおよびネットワークの問題を迅速に診断して修正し、VM を良好な状態に戻すことができます。



## <a name="benefits"></a>メリット

仮想マシンには複数の方法でアクセスできます。 実行コマンドは、VM エージェントを使用して、仮想マシン上でスクリプトをリモートで実行できます。 実行コマンドは、Azure portal、[REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)、または Windows VM 用の [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) から使用します。

この機能は、仮想マシン内でスクリプトを実行するすべてのシナリオで役立ちます。 これは、ネットワークまたは管理ユーザーの構成が正しくないために RDP または SSH ポートが開かれていない仮想マシンをトラブルシューティングして修正する、限られた方法の 1 つです。

## <a name="restrictions"></a>制限

実行コマンドを使用するときには、次の制限が適用されます。

* 出力は最後の 4,096 バイトに制限されます。
* スクリプトを実行するための最小時間は約 20 秒です。
* スクリプトは Windows 上で System として実行されます。
* 一度に実行できるスクリプトは 1 つです。
* 情報の入力を求めるスクリプト (対話モード) はサポートされていません。
* スクリプトの実行を取り消すことはできません。
* スクリプトを実行できる最大時間は 90 分です。 それを過ぎると、タイムアウトになります。
* スクリプトの結果を返すために、VM からの送信接続が必要
* VM エージェントの停止または更新の原因となるスクリプトを実行することはお勧めしません。 これにより、拡張が遷移状態になり、タイムアウトになります。

> [!NOTE]
> 正常に機能するには、実行コマンドに Azure のパブリック IP アドレスへの接続 (ポート 443) が必要です。 この拡張機能にこれらのエンドポイントへのアクセス権がない場合、スクリプトが正常に実行されても結果が返されないことがあります。 仮想マシン上のトラフィックをブロックしている場合、[サービス タグ](../../virtual-network/network-security-groups-overview.md#service-tags)を使用し、`AzureCloud` タグを使用して Azure パブリック IP アドレスへのトラフィックを許可できます。

## <a name="available-commands"></a>使用可能なコマンド

次の表に、Windows VM で使用可能なコマンドの一覧を示します。 **RunPowerShellScript** コマンドを使用して、任意のカスタム スクリプトを実行できます。 Azure CLI または PowerShell を使用してコマンドを実行する場合、`--command-id` または `-CommandId` パラメーターに指定する値は、次に示す値のいずれかである必要があります。 使用可能なコマンドではない値を指定すると、次のエラーが表示されます。

```error
The entity was not found in this Azure location
```

|**名前**|**説明**|
|---|---|
|**RunPowerShellScript**|PowerShell スクリプトを実行します。|
|**EnableRemotePS**|コンピューターを構成してリモート PowerShell を有効にします。|
|**EnableAdminAccount**|ローカル管理者アカウントが無効になっているかどうかを確認し、無効になっている場合は有効にします。|
|**IPConfig**| TCP/IP にバインドされているアダプターごとに、IP アドレス、サブネット マスク、およびデフォルト ゲートウェイの詳細な情報を表示します。|
|**RDPSettings**|レジストリ設定およびドメインのポリシー設定を確認します。 マシンがドメインの一部である場合はポリシー アクションを提案します。または、設定を既定値に変更します。|
|**ResetRDPCert**|RDP リスナーに関連付けられている TLS または SSL 証明書を削除し、RDP リスナーのセキュリティを既定値に戻します。 証明書に問題がある場合は、このスクリプトを使用します。|
|**SetRDPPort**|リモート デスクトップ接続用の既定またはユーザー指定のポート番号を設定します。 ポートへの受信アクセスに対するファイアウォール規則を有効にします。|

## <a name="azure-cli"></a>Azure CLI

次の例は、[az vm run-command](/cli/azure/vm/run-command#az-vm-run-command-invoke) コマンドを使用して Azure Windows VM 上でシェル スクリプトを実行します。

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure portal

[Azure portal](https://portal.azure.com) 内の VM に移動し、 **[操作]** で **[実行コマンド]** を選択します。 VM 上で実行できるコマンドの一覧が表示されます。

![コマンドの一覧](./media/run-command/run-command-list.png)

実行するコマンドを選択します。 一部のコマンドには、省略可能または必須の入力パラメーターがある場合があります。 そうしたコマンドの場合、パラメーターは、入力値を指定するためのテキスト フィールドとして表示されます。 コマンドごとに、 **[スクリプトの表示]** を展開することによって、実行されるスクリプトを表示できます。 **RunPowerShellScript** は他のコマンドと異なり、独自のカスタム スクリプトを指定することができます。

> [!NOTE]
> 組み込みコマンドは編集できません。

コマンドを選択した後、 **[実行]** を選択してスクリプトを実行します。 スクリプトが完了すると、出力ウィンドウに出力およびエラー (発生した場合) が返されます。 次のスクリーン ショットは、**RDPSettings** コマンドを実行した出力例を示しています。

![実行コマンド スクリプトの出力](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

次の例では、[Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) コマンドレットを使用して Azure VM 上で PowerShell スクリプトを実行します。 このコマンドレットは、`-ScriptPath` パラメーターで参照されるスクリプトが、このコマンドレットの実行場所に対してローカルであることを想定しています。

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>実行コマンドへのアクセスの制限

実行コマンドを一覧表示したり、コマンドの詳細を表示したりするには、サブスクリプション レベルの `Microsoft.Compute/locations/runCommands/read` アクセス許可が必要です。 組み込みの[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロール以上のレベルには、このアクセス許可があります。

コマンドの実行には、`Microsoft.Compute/virtualMachines/runCommand/action` アクセス許可が必要です。 [仮想マシンの共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロール以上のレベルには、このアクセス許可があります。

実行コマンドを使用するには、いずれかの[組み込みロール](../../role-based-access-control/built-in-roles.md)を使用するか、[カスタム ロール](../../role-based-access-control/custom-roles.md)を作成します。

## <a name="next-steps"></a>次のステップ

VM 内でスクリプトやコマンドをリモートで実行するその他の方法については、「[Windows VM でスクリプトを実行する](run-scripts-in-vm.md)」を参照してください。
