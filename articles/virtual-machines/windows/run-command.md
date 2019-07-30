---
title: Azure 内の Windows VM で PowerShell スクリプトを実行する
description: このトピックでは、実行コマンドを使用して Azure Windows 仮想マシン内で PowerShell スクリプトを実行する方法について説明します。
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: de45f2fe6230e48c3cffc999e2c84d6ee0a60edc
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476769"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>実行コマンドを使用して Windows VM で PowerShell スクリプトを実行する

実行コマンドは、VM エージェントを使用して Azure Windows VM 内で PowerShell スクリプトを実行します。 これらのスクリプトは、一般的なコンピューターまたはアプリケーションの管理に使用できるだけでなく、VM のアクセスやネットワークの問題をすばやく診断および修正し、その VM を正常な状態に戻すためにも使用できます。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>メリット

仮想マシンにアクセスするために使用できるオプションは複数あります。 実行コマンドは、VM エージェントを使用して、リモートから仮想マシン上でスクリプトを実行できます。 実行コマンドは、Azure portal、[REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)、または Windows VM 用の [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) から使用できます。

この機能は、仮想マシン内でスクリプトを実行するすべてのシナリオで有効であり、誤ったネットワークまたは管理ユーザーの構成のために RDP または SSH ポートが開かれていない仮想マシンをトラブルシューティングして修正する最適な方法の 1 つです。

## <a name="restrictions"></a>制限

実行コマンドを使用するときには、次の制限が適用されます。

* 出力は最後の 4096 バイトに制限される
* スクリプトを実行するための最小時間は約 20 秒
* スクリプトは Windows 上で System として実行される
* 同時に実行できるスクリプトは 1 つ
* 情報の入力を求めるスクリプト (対話モード) はサポートされていません。
* スクリプトの実行を取り消すことはできない
* スクリプトを実行できる最大時間は 90 分であり、その後タイムアウトになる
* スクリプトの結果を返すために、VM からの送信接続が必要

> [!NOTE]
> 正常に機能するには、実行コマンドに Azure のパブリック IP アドレスへの接続 (ポート 443) が必要です。 拡張機能に、これらのエンドポイントへのアクセス権がない場合、スクリプトが正常に実行しても、結果が返されないことがあります。 仮想マシン上のトラフィックをブロックしている場合、[サービス タグ](../../virtual-network/security-overview.md#service-tags)を使用し、`AzureCloud` タグを使用して、Azure パブリック IP アドレスへのトラフィックを許可できます。

## <a name="run-a-command"></a>コマンドの実行

[Azure](https://portal.azure.com) で VM に移動し、 **[操作]** で **[実行コマンド]** を選択します。 VM 上で実行できるコマンドの一覧が表示されます。

![実行コマンドの一覧](./media/run-command/run-command-list.png)

実行するコマンドを選択します。 コマンドによっては、省略可能または必須の入力パラメーターがある場合があります。 これらのコマンドの場合、パラメーターは、入力値を指定するためのテキスト フィールドとして表示されます。 コマンドごとに、 **[スクリプトの表示]** を展開して実行されているスクリプトを表示できます。 **RunPowerShellScript** は他のコマンドと異なり、独自のカスタム スクリプトを指定することができます。

> [!NOTE]
> 組み込みコマンドは編集できません。

コマンドを選択したら、 **[実行]** をクリックしてスクリプトを実行します。 スクリプトは、実行を完了すると、出力ウィンドウに出力およびエラーを返します。 次のスクリーン ショットは、**RDPSettings** コマンドを実行した出力例を示しています。

![実行コマンド スクリプトの出力](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>command

次の表に、Windows VM で使用可能なコマンドの一覧を示します。 **RunPowerShellScript** コマンドを使用すると、任意のカスタム スクリプトを実行できます。

|**Name**|**説明**|
|---|---|
|**RunPowerShellScript**|PowerShell スクリプトを実行します。|
|**EnableRemotePS**|コンピューターを構成してリモート PowerShell を有効にします。|
|**EnableAdminAccount**|ローカル管理者アカウントが無効になっているかどうかを確認し、無効になっている場合は有効にします。|
|**IPConfig**| TCP/IP にバインドされているアダプターごとに、IP アドレス、サブネット マスク、およびデフォルト ゲートウェイの詳細な情報を表示します。|
|**RDPSettings**|レジストリ設定およびドメインのポリシー設定を確認します。 コンピューターがドメインの一部である場合はポリシー アクションを提案します。または、設定を既定値に変更します。|
|**ResetRDPCert**|RDP リスナーに関連付けられている SSL 証明書を削除し、RDP リスナーのセキュリティを既定値に戻します。 証明書に問題がある場合は、このスクリプトを使用します。|
|**SetRDPPort**|リモート デスクトップ接続のために既定値またはユーザー指定のポート番号を設定します。 ポートへの受信アクセスに対するファイアウォール規則を有効にします。|

## <a name="powershell"></a>PowerShell

[Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) コマンドレットを使用して Azure VM 上で PowerShell スクリプトを実行する例を次に示します。 このコマンドレットは、`-ScriptPath` パラメーターで参照されるスクリプトが、このコマンドレットの実行場所に対してローカルであることを想定しています。


```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>実行コマンドへのアクセスの制限

実行コマンドを一覧表示したり、コマンドの詳細を表示したりするには、組み込みの[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロール以上が持っている `Microsoft.Compute/locations/runCommands/read` アクセス許可がサブスクリプション レベルで必要です。

コマンドを実行するには、[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)ロール以上が持っている `Microsoft.Compute/virtualMachines/runCommand/action` アクセス許可がサブスクリプション レベルで必要です。

実行コマンドを使用するには、いずれかの[組み込み](../../role-based-access-control/built-in-roles.md)ロールを使用するか、または[カスタム](../../role-based-access-control/custom-roles.md) ロールを作成することができます。

## <a name="next-steps"></a>次の手順

VM 内でリモートからスクリプトおよびコマンドを実行するその他の方法については、[Windows VM でスクリプトを実行する](run-scripts-in-vm.md)を参照してください。
