---
title: Azure DevTest Labs でのアイテム保持ポリシーの設定 | Microsoft Docs
description: DevTest Labs でアイテム保持ポリシーの構成、ファクトリのクリーンアップ、古いイメージの削除を行う方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85384e88f8d456c7bf67302a57618d7a9703a5ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550027"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Azure DevTest Labs でのアイテム保持ポリシーの設定
この記事では、保持ポリシーの設定、ファクトリのクリーンアップ、および組織内の他のすべての DevTest Labs からの古いイメージの回収を取り上げます。 

## <a name="prerequisites"></a>前提条件
続行する前に以下の記事に従っていることを確認してください。

- [イメージ ファクトリを作成する](image-factory-create.md)
- [Azure DevOps からイメージ ファクトリを実行する](image-factory-set-up-devops-lab.md)
- [カスタム イメージを保存して複数のラボに配布する](image-factory-save-distribute-custom-images.md)

次の項目が既に配置されている必要があります。

- Azure DevTest Labs のイメージ ファクトリ用のラボ
- ファクトリがゴールデン イメージを配布する 1 つ以上のターゲット Azure DevTest Labs
- イメージ ファクトリを自動化するために使用される Azure DevOps プロジェクト。
- スクリプトと構成を含むソース コードの場所 (この例では、上記の同じ DevOps プロジェクト内)
- Azure Powershell タスクを調整するビルド定義
 
## <a name="setting-the-retention-policy"></a>保持ポリシーの設定
クリーンアップの手順を構成する前に、DevTest Labs で保持する履歴イメージの数を定義します。 「[Azure DevOps からイメージ ファクトリを実行する](image-factory-set-up-devops-lab.md)」の記事に従っていれば、さまざまなビルド変数を構成しています。 その 1 つが **ImageRetention** でした。 この変数を `1` に設定します。これは、DevTest Labs がカスタム イメージの履歴を保持しないことを意味します。 最新の配布イメージだけを使用できます。 この変数を `2` に変更すると、最新の配布イメージと以前のイメージが保持されます。 この値を設定して、DevTest Labs で保持する履歴イメージの数を定義できます。

## <a name="cleaning-up-the-factory"></a>ファクトリのクリーンアップ
ファクトリのクリーンアップでは最初に、イメージ ファクトリからゴールデン イメージ VM を削除します。 以前のスクリプトと同様、このタスクを実行するスクリプトがあります。 最初の手順として、次の図に示すように、別の **Azure Powershell** タスクをビルド定義に追加します。

![PowerShell 手順](./media/set-retention-policy-cleanup/powershell-step.png)

一覧に新しいタスクが追加されたら、その項目を選択し、次の図に示すようにすべての詳細を入力します。

![古いイメージのクリーンアップ PowerShell タスク](./media/set-retention-policy-cleanup/configure-powershell-task.png)

スクリプト パラメーターは `-DevTestLabName $(devTestLabName)` です。

## <a name="retire-old-images"></a>古いイメージを回収する 
このタスクは、すべての古いイメージを削除し、**ImageRetention** ビルド変数に一致する履歴だけを保持します。 その他の **Azure Powershell** ビルド タスクをビルド定義に追加します。 追加されたら、タスクを選択し、次の図のように詳細を入力します。 

![古いイメージの回収 PowerShell タスク](./media/set-retention-policy-cleanup/retire-old-image-task.png)

スクリプト パラメーターは `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)` です。

## <a name="queue-the-build"></a>ビルドをキューに配置する
ビルド定義を完了したので、新しいビルドをキューに格納して、すべてが機能していることを確認します。 ビルドが、配布先のラボに表示された新しいカスタム イメージを正常に完了した後、イメージ ファクトリ ラボを調べた場合、プロビジョニングした VM は表示されていません。 さらに、続けてビルドをキューに格納した場合、ビルド変数で設定されたリテンション期間の値に従って、DevTest Labs から古いカスタム イメージを回収するクリーンアップ タスクが表示されます。

> [!NOTE]
> シリーズの前回の記事の終わりにビルド パイプラインを実行した場合は、新しいビルドをキューに配置する前に、イメージ ファクトリ ラボに作成された仮想マシンを手動で削除します。  手動のクリーンアップ手順は、すべてを設定して動作することを確認するときにのみ必要です。



## <a name="summary"></a>まとめ
これで、必要に応じてカスタム イメージを生成してラボに配布できる実行中イメージ ファクトリが用意されました。 この時点では、イメージを正しく設定し、ターゲット ラボを特定するだけです。 前の記事で説明したように、自身の **Configuration** フォルダーに置かれている **Labs.json** ファイルは、ターゲット ラボのそれぞれで使用可能にする必要のあるイメージを指定します。 組織に他の DevTest Labs を追加するときに、新しいラボに対して Labs.json 内のエントリを追加する必要があるだけです。

ファクトリへの新しいイメージの追加も簡単です。 ファクトリに新しいイメージを含めるときに、[Azure portal](https://portal.azure.com) を開き、ファクトリ DevTest Labs に移動し、VM を追加するボタンを選択し、必要なマーケットプレースのイメージおよびアーティファクトを選択します。 **[作成]** ボタンを選択して新しい VM を作成する代わりに、**[Azure Resource Manager テンプレートの表示]** を選択し、リポジトリの **GoldenImages** フォルダー内の任意の場所に .json ファイルとしてテンプレートを保存します。 次回、イメージ ファクトリを実行するときに、カスタム イメージが作成されます。


## <a name="next-steps"></a>次のステップ
1. イメージ ファクトリを定期的に実行するように[ビルド/リリースのスケジュール](/azure/devops/pipelines/build/triggers?tabs=designer)を設定します。 ファクトリによって生成されたイメージを定期的に更新します。
2. ファクトリのより多くのゴールデン イメージを作成します。 追加の VM 設定タスクのスクリプトを作成し、ファクトリ イメージにアーティファクトを含めるように、[アーティファクトを作成](devtest-lab-artifact-author.md)することも検討できます。
4. [別々のビルド/リリース](/azure/devops/pipelines/overview?view=azure-devops-2019)を作成して、**DistributeImages** スクリプトを別々に実行します。 Labs.json に変更を加え、ターゲット ラボにイメージをコピーするときに、このスクリプトを実行でき、すべてのイメージを再作成する必要はありません。

