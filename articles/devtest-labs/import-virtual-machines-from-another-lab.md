---
title: Azure DevTest Labs で別のラボから仮想マシンをインポートする
description: この記事では、Azure DevTest Labs の現在のラボに、別のラボから仮想マシンをインポートする方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85475936"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Azure DevTest Labs で別のラボから仮想マシンをインポートする
この記事では、別のラボから自分のラボに仮想マシンをインポートする方法について説明します。

## <a name="scenarios"></a>シナリオ
一部のシナリオでは、あるラボから別のラボに VM をインポートする必要があります。

- チームにいた人が企業内の別のグループに移動するので、新しいチームの DevTest Labs に開発者のデスクトップを持ち込むことを希望しています。
- そのグループは[サブスクリプションレベルのクォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)に達しており、いくつかのサブスクリプションに分割したいと考えています。
- 会社は Express Route (またはいくつかその他の新しいネットワーク トポロジ) に移行しており、チームはこの新しいインフラストラクチャへの仮想マシンの移動を希望しています。

## <a name="solution-and-constraints"></a>ソリューションと制約
この機能によって、あるラボ (インポート元) から別のラボ (インポート先) に VM をインポートできます。 この過程では、インポート先となる VM に新しい名前を任意で付けることができます。 インポート処理には、ディスク、スケジュール、ネットワーク設定などのすべての依存関係が含まれています。

この処理には時間がかかります。また、次に示す要因の影響を受ます。

- インポート元のコンピューターに接続されているディスクの数/サイズ (これは移動操作ではなく、コピー操作であるため)。
- インポート先までの距離 (例: 米国東部リージョンから東南アジアまで)。

処理が完了すると、インポート元の仮想マシンはシャットダウンしたままとなり、インポート先のラボでは新しい VM が動作します。

VM をラボ間でインポートする際に注意する 2 つの重要な制約があります。

- サブスクリプションやリージョンが異なる仮想マシンのインポートはサポートされているが、サブスクリプションには、同じ Azure Active Directory テナントが関連付けられている必要がある。
- 仮想マシンがインポート元のラボでクレーム可能状態ではない必要がある。
- あなたが、インポート元とインポート先の両方のラボで VM の所有者であること。
- 現在のところ、この機能がサポートされているのは、PowerShell と REST API を介してのみ。

## <a name="use-powershell"></a>PowerShell の使用
[GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) から ImportVirtualMachines.ps1 ファイルをダウンロードします。 スクリプトを使用し、インポート元のラボの 1 つまたは全部の VM をインポート先のラボにインポートできます。

### <a name="use-powershell-to-import-a-single-vm"></a>PowerShell を使用し、1 つの VM をインポートする
この PowerShell スクリプトを実行するには、インポート元の VM とインポート先のラボを特定し、必要であれば、インポート先のコンピューターで新しい名前を付ける必要があります。

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>PowerShell を使用し、インポート元のラボのすべての VM をインポートする
インポート元の仮想マシンが指定されていない場合、このスクリプトでは、DevTest Labs. のすべての VM が自動的にインポートされます。  次に例を示します。

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST を使用し、VM をインストールする
REST の呼び出しは簡単です。 インポート元のリソースとインポート先のリソースを特定するために十分な情報を与えます。 この操作はインポート先のラボのリソースで行われることにご留意ください。

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)
- [よく寄せられる質問](devtest-lab-faq.md)
