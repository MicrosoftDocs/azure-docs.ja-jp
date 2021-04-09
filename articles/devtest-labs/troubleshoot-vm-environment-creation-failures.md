---
title: Azure DevTest Labs での VM および環境の失敗に関するトラブルシューティング
description: Azure DevTest Labs での仮想マシン (VM) および 環境の作成の失敗に関するトラブルシューティングの方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476480"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Azure DevTest Labs での仮想マシン (VM) および 環境の作成の失敗に関するトラブルシューティング
DevTest Labs は、マシン名が無効であるか、ラボ ポリシーに違反する可能性がある場合に警告を表示します。 問題が発生したことを伝えるラボ VM または環境のステータスの隣に、赤色の `X` が表示されることがあります。  この記事では、根本的な問題を検索し、できれば今後の問題を回避するために使用できるいくつかのこつを示します。

## <a name="portal-notifications"></a>Azure portal の通知
Azure portal を使用している場合、最初に表示される場所が **通知パネル** です。  **ベルのアイコン** をクリックすることによりメイン コマンド バーで使用できる通知パネルには、ラボ VM または環境の作成が成功したかどうかが表示されます。  失敗した場合、作成エラーに関連付けられているエラー メッセージを参照してください。 多くの場合、問題解決に役立つ追加情報はこの詳細から得られます。 次の例では、仮想マシンの作成は、コアを使い果たしたために失敗しました。 詳細メッセージには、問題を修正し、コア クォータの引き上げを要求する方法が示されています。

![Microsoft Azure portal 通知](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>破損状態の VM
ラボ内の VM の状態が **[破損]** の場合、基になる VM が **[仮想マシン]** ページから削除されている可能性があります。ユーザーはこのページに **[仮想マシン]** ページから移動できます ([DevTest Labs] ページからではありません)。 [DevTest Labs] で、ラボから VM を削除してクリーンアップします。 次に、ラボで VM を再作成します。 

![破損状態の VM](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>アクティビティ ログ
VM または環境の作成を試みてからしばらくの間、失敗を調査している場合は、アクティビティ ログを見てください。 このセクションには、VM と環境のログを検索する方法が示されています。

## <a name="activity-logs-for-virtual-machines"></a>仮想マシンのアクティビティ ログ

1. ラボのホーム ページで VM を選択して **[仮想マシン]** ページを起動します。
2. **[仮想マシン]** ページの左メニューの **[監視]** セクションで、**[アクティビティ ログ]** を選択して、VM に関連付けられているすべてのログを表示します。
3. アクティビティ ログ項目では、失敗した操作を選択します。 通常、失敗した操作は、`Write Virtualmachines` と呼ばれます。
4. 右ウィンドウで [JSON] タブに切り替えます。ログの JSON ビューで詳細を確認します。

    ![VM のアクティビティ ログ](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. `statusMessage` プロパティが見つかるまで、JSON ログを調べます。 ここには、該当する場合、主要なエラー メッセージと詳細情報が表示されます。 次の JSON は、この記事で前述した、コア クォータ超過エラーの例です。

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>環境のアクティビティ ログ

環境の作成のアクティビティ ログを表示するには、次の手順に従います。

1. ラボのホーム ページで、左側のメニューから **[構成とポリシー]** を選択します。
2. **[構成とポリシー]** ページで、メニューから **[アクティビティ ログ]** を選択します。
3. ログのアクティビティの一覧でエラーを探し、これを選択します。
4. 右ウィンドウで、[JSON] タブに切り替え、**statusMessage** を探します。

    ![環境のアクティビティ ログ](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager テンプレートのデプロイ ログ
環境または仮想マシンが自動化を通じて作成された場合、エラー情報が見つかる最後の場所が 1 か所あります。 それが Azure Resource Manager のテンプレート デプロイ ログです。 ラボ リソースが自動化を通じて作成されるとき、多くの場合、Azure Resource Manager テンプレートのデプロイを通じて行われます。 DevTest Labs リソースを作成するサンプルの Azure Resource Manager テンプレートについては、[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) を参照してください。

ラボ テンプレートのデプロイ ログを表示するには、次の手順に従います。

1. ラボが存在するリソース グループのページを起動します。
2. **[設定]** の下の左側のメニューで **[デプロイ]** を選択します。
3. 失敗した状態のデプロイを検索して選択します。
4. **[デプロイ]** ページで、失敗した操作の **[操作の詳細]** リンクを選択します。
5. **[操作の詳細]** ウィンドウで、失敗した操作の詳細を確認します。

## <a name="next-steps"></a>次のステップ
[アーティファクトの失敗に関するトラブルシューティング](devtest-lab-troubleshoot-artifact-failure.md)を参照してください
