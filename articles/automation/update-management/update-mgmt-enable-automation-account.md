---
title: Automation アカウントから Azure Automation Update Management を有効にする
description: この記事では、Automation アカウントから Update Management を有効にする方法について説明します。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449690"
---
# <a name="enable-update-management-from-an-automation-account"></a>Automation アカウントから Update Management を有効にする

この記事では、Automation アカウントを使用して、環境内の VM の [Update Management](update-mgmt-overview.md) 機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、Update Management を使用して既存の VM を有効にする必要があります。

> [!NOTE]
> Update Management を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../index.yml)。
* [仮想マシン](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-update-management"></a>Update Management の有効化

1. Automation アカウントの **[更新プログラムの管理]** で **[更新プログラムの管理]** を選択します。

2. Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** を選択して、Update Management を有効にします。 セットアップが完了するまでに最大 15 分かかります。

    ![Update Management の有効化](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Azure VM の有効化

1. Automation アカウントから、 **[更新プログラムの管理]** で **[更新プログラムの管理]** を選択します。

2. **[+ Azure VM の追加]** を選択し、リストから 1 つ以上の VM を選択します。 有効にできない仮想マシンは灰色で表示され、選択できません。 Azure VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。

3. **[有効にする]** を選択すると、選択された VM が、この機能の "保存した検索条件" コンピューター グループに追加されます。

    ![Azure VM の有効化](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Azure 以外の VM の有効化

Azure に存在しないマシンは手動で追加する必要があります。

1. Automation アカウントから、 **[更新プログラムの管理]** で **[更新プログラムの管理]** を選択します。

2. **[Azure 以外のマシンを追加する]** を選択します。 このアクションにより、新しいブラウザー ウィンドウが開きます。このウィンドウには、マシンが Update Management へのレポートを開始できるように、[Windows 用の Log Analytics エージェントをインストールして構成する方法](../../azure-monitor/platform/log-analytics-agent.md)が示されます。 Operations Manager によって現在管理されているマシンを有効にする場合、新しいエージェントは必要ありません。 ワークスペース情報は、既存のエージェントに入力されます。

## <a name="enable-machines-in-the-workspace"></a>ワークスペースでのマシンの有効化

手動でインストールしたマシンや、既にワークスペースへのレポートを行っているマシンは、Azure Automation に追加して Update Management を有効にする必要があります。

1. Automation アカウントから、 **[更新プログラムの管理]** で **[更新プログラムの管理]** を選択します。

2. **[マシンの管理]** を選択します。 前に **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択している場合、 **[マシンの管理]** ボタンはグレー表示される可能性があります。

    ![保存した検索条件](media/update-mgmt-enable-automation-account/managemachines.png)

3. 使用可能なすべてのマシンの Update Management を有効にするには、[マシンの管理] ページで **[使用可能なすべてのマシンで有効にします]** を選択します。 このアクションにより、マシンを個別に追加するコントロールが無効になります。 このタスクにより、ワークグループへのレポートを行うすべてマシンの名前が、"保存した検索クエリ" コンピューター グループに追加されます。 選択されている場合は、 **[マシンの管理]** ボタンが無効になります。

4. 使用可能なすべてのマシンと今後のマシンに対してこの機能を有効にするには、 **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択します。 このオプションを選択すると、保存した検索条件とスコープ構成がワークスペースから削除され、ワークスペースへのレポートを行うすべての Azure マシンと Azure 以外のマシンに対して機能が開放されます。 選択された場合は、残されたスコープ構成がないため、 **[マシンの管理]** ボタンが永続的に無効になります。

5. 必要であれば、最初に保存した検索条件を再度追加することで、スコープ構成を追加し直すことができます。 詳細については、[Update Management の展開スコープの制限](update-mgmt-scope-configuration.md)に関するページを参照してください。

6. 1 つ以上のマシンに対して機能を有効にするには、 **[選択したマシンで有効にします]** を選択し、各マシンの横にある **[追加]** を選択します。 このタスクにより、選択したマシン名が、機能の "保存した検索クエリ" コンピューター グループに追加されます。

## <a name="next-steps"></a>次のステップ

* VM の Update Management を使用するには、「[VM の更新プログラムとパッチの管理](update-mgmt-manage-updates-for-vm.md)」を参照してください。

* Update Management の一般的なエラーのトラブルシューティングについては、[Update Management の問題のトラブルシューティング](../troubleshoot/update-management.md)に関する記事を参照してください。