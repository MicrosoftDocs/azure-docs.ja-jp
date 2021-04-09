---
title: Azure DevTest Labs でのイメージの保存と配布 | Microsoft Docs
description: この記事では、Azure DevTest Labs で、作成済みの仮想マシン (VM) からカスタム イメージを保存する手順について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476242"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>カスタム イメージを保存して複数のラボに配布する
この記事では、作成済みの仮想マシン (VM) からカスタム イメージを保存する手順について説明します。 また、これらのカスタム イメージを組織内の他の DevTest Labs に配布する方法についても説明します。

## <a name="prerequisites"></a>前提条件
次の項目が既に配置されている必要があります。

- Azure DevTest Labs のイメージ ファクトリ用のラボ。
- イメージ ファクトリを自動化するために使用される Azure DevOps プロジェクト。
- スクリプトと構成を含むソース コードの場所 (この例では、前の手順で説明した DevOps プロジェクト内)。
- Azure Powershell タスクを調整するビルド定義。

必要に応じて、「[Azure DevOps からイメージ ファクトリを実行する](image-factory-set-up-devops-lab.md)」の手順を実行し、これらの項目を作成または設定してください。 

## <a name="save-vms-as-generalized-vhds"></a>一般化された VHD として VM を保存する
既存の VM を一般化された VHD として保存します。  既存の VM を一般化された VHD として保存するためのサンプル PowerShell スクリプトがあります。 それを使用するには、次の図に示すように、まず別の **Azure Powershell** タスクをビルド定義に追加します。

![Azure PowerShell の追加手順](./media/save-distribute-custom-images/powershell-step.png)

一覧に新しいタスクが追加されたら、その項目を選択し、次の図に示すようにすべての詳細を入力します。 

![PowerShell 設定](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>一般化されたカスタム イメージと特殊化されたカスタム イメージ
[Azure portal](https://portal.azure.com) で仮想マシンからカスタム イメージを作成するときに、一般化されたカスタム イメージまたは特殊化されたカスタム イメージを選択できます。

- **特殊化されたカスタム イメージ:** sysprep/プロビジョニング解除がマシンで実行されませんでした。 これは、イメージが既存の仮想マシン (スナップショット) 上の OS ディスクの正確なコピーであることを意味します。  このカスタム イメージから新しいマシンを作成すると、同じファイル、アプリケーション、ユーザー アカウント、コンピューター名などがすべて存在します。
- **一般化されたカスタム イメージ:** sysprep/プロビジョニング解除がマシンで実行されました。  イメージを一般化して、別の仮想マシンを作成するときにカスタマイズできるようにするために、このプロセスの実行時にユーザー アカウント、コンピューター名、ユーザー レジストリ ハイブなどが削除されます。  仮想マシンを (sysprep を実行することで) 一般化すると、現在の仮想マシンは破棄され、機能しなくなります。

イメージ ファクトリ内のカスタム イメージをスナップするスクリプトにより、前の手順で作成された仮想マシンの VHD が保存されます (Azure 内のリソース上のタグに基づいて識別されます)。

## <a name="update-configuration-for-distributing-images"></a>イメージを配布するための構成の更新
このプロセスの次の手順は、イメージ ファクトリのカスタム イメージを、そのイメージを必要とする別のラボにプッシュすることです。 このプロセスの中心となるのは、**labs.json** 構成ファイルです。 このファイルは、イメージ ファクトリに含まれている **Configuration** フォルダー内にあります。

labs.json 構成ファイルには、2 つの重要な事項があります。

- サブスクリプション ID とラボ名を使用した、特定の配布先ラボの一意的な識別。
- 構成ルートへの相対パスでラボにプッシュされる特定のイメージ セット。 フォルダー全体を指定することもできます (そのフォルダ内のすべてのイメージを取得します)。

2 つのラボがリストされた labs.json ファイルの例を次に示します。 この場合、2 つの異なるラボにイメージが配布されます。

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>ビルド タスクを作成する
この記事の前半で説明した手順を使用して、追加の **Azure Powershell** ビルド タスクをビルド定義に追加します。 次の図に示されているように詳細を入力します。 

![イメージを配布するビルド タスク](./media/save-distribute-custom-images/second-build-task-powershell.png)

パラメーターは次のとおりです。`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

このタスクは、イメージ ファクトリ内のカスタム イメージを取得し、Labs.json ファイルに定義されているすべてのラボにプッシュします。

## <a name="queue-the-build"></a>ビルドをキューに配置する
配布ビルド タスクが完了したら、すべてが確実に機能するように、新しいビルドをキューに配置します。 ビルドが正常に完了したら、Labs.json 構成ファイルに入力された配布先のラボに、新しいカスタム イメージが表示されます。

## <a name="next-steps"></a>次のステップ
このシリーズの次の記事、[「アイテム保持ポリシーを設定してクリーンアップ スクリプトを実行する」](image-factory-set-retention-policy-cleanup.md)では、保持ポリシーとクリーンアップ手順を使用してイメージ ファクトリを更新します。
