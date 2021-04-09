---
title: Automation アカウントから Azure Automation Update Management を有効にする
description: この記事では、Automation アカウントから Update Management を有効にする方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089d5d70d8ad8060455e5c1bee45e0bee4a12fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575839"
---
# <a name="enable-update-management-from-an-automation-account"></a>Automation アカウントから Update Management を有効にする

この記事では、Automation アカウントを使用して、環境内の VM の [Update Management](overview.md) 機能を有効にする方法について説明します。これには [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)で登録されたマシンやサーバーも含まれます。 Azure VM を大規模に有効にするには、Update Management を使用して既存の Azure VM を有効にする必要があります。

> [!NOTE]
> Update Management を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../automation-security-overview.md)。
* Arc 対応サーバーで登録された [Azure 仮想マシン](../../virtual-machines/windows/quick-create-portal.md) (VM) またはサーバー。 Azure 以外の VM やサーバーの場合、Windows または Linux 向けの [Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md)をインストールしておく必要があり、Update Management が有効になっている Automation アカウントにリンクされているワークスペースに報告させる必要があります。 最初にマシンを [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)に接続し、次に Azure Policy を使用して [*Linux* または *Windows* Azure Arc マシンに Log Analytics エージェントをデプロイする](../../governance/policy/samples/built-in-policies.md#monitoring)組み込みポリシーに割り当てることにより、Windows または Linux 用の Log Analytics エージェントをインストールすることが推奨されます。 また、Azure Monitor for VMs を使用してマシンの監視も行う場合は、代わりに [Azure Monitor for VMs を有効にする](../../governance/policy/samples/built-in-initiatives.md#monitoring)イニシアティブを使用します。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-update-management"></a>Update Management の有効化

1. Automation アカウントの **[更新プログラムの管理]** で **[更新プログラムの管理]** を選択します。

2. Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** を選択して、Update Management を有効にします。 セットアップが完了するまでに最大 15 分かかります。

    ![Update Management の有効化](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Azure VM の有効化

1. Automation アカウントから、 **[更新プログラムの管理]** で **[更新プログラムの管理]** を選択します。

2. **[+ Azure VM の追加]** を選択し、リストから 1 つ以上の VM を選択します。 有効にできない仮想マシンは灰色で表示され、選択できません。 Azure VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。

3. **[有効にする]** を選択すると、選択された VM が、この機能の "保存した検索条件" コンピューター グループに追加されます。

    ![Azure VM の有効化](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Azure 以外の VM の有効化

Azure の外部でホストされているマシンまたはサーバー (Azure Arc 対応サーバーに登録されたものを含む) で Update Management を有効にするには、次の手順を実行します。  

1. Automation アカウントから、 **[更新プログラムの管理]** で **[更新プログラムの管理]** を選択します。

2. **[Azure 以外のマシンを追加する]** を選択します。 このアクションにより、新しいブラウザー ウィンドウが開きます。このウィンドウには、マシンが Update Management へのレポートを開始できるように、[Windows 用の Log Analytics エージェントをインストールして構成する方法](../../azure-monitor/agents/log-analytics-agent.md)が示されます。 Operations Manager によって現在管理されているマシンを有効にする場合、新しいエージェントは必要ありません。 ワークスペース情報は、既存のエージェントに入力されます。

## <a name="enable-machines-in-the-workspace"></a>ワークスペースでのマシンの有効化

手動でインストールしたマシンや、既にワークスペースへのレポートを行っているマシンは、Azure Automation に追加して Update Management を有効にする必要があります。

1. Automation アカウントから、 **[更新プログラムの管理]** で **[更新プログラムの管理]** を選択します。

2. **[マシンの管理]** を選択します。 前に **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択している場合、 **[マシンの管理]** ボタンはグレー表示される可能性があります。

    ![保存した検索条件](media/enable-from-automation-account/managemachines.png)

3. ワークスペースに報告するマシンのうち、使用可能なすべてのマシンで Update Management を有効にするには、[マシンの管理] ページで **[使用可能なすべてのマシンで有効にします]** を選択します。 この操作によって、コンピューターを個別に追加するコントロールが無効になり、ワークスペースに報告するすべてのコンピューターが、コンピューター グループの保存された検索条件のクエリ `MicrosoftDefaultComputerGroup` に追加されます。 選択されている場合は、 **[マシンの管理]** オプションが無効になります。

4. 使用可能なすべてのマシンと今後のマシンに対してこの機能を有効にするには、 **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択します。 このオプションを選択すると、保存した検索条件とスコープ構成がワークスペースから削除され、現在あるいは将来ワークスペースに報告するすべての Azure マシンと Azure 以外のマシンを含めることがこの機能に許可されます。 選択された場合は、利用できるスコープ構成がないため、 **[マシンの管理]** オプションが永続的に無効になります。

    > [!NOTE]
    > このオプションで Log Analytics 内の保存済み検索とスコープ構成が削除されるため、このオプションを選択する前に、Log Analytics Workspace で削除ロックを削除することが重要です。 そうしない場合、オプションを選択しても構成を削除できず、手動で削除する必要があります。

5. 必要であれば、最初に保存した検索クエリを再度追加することで、スコープ構成を追加し直すことができます。 詳細については、[Update Management の展開スコープの制限](scope-configuration.md)に関するページを参照してください。

6. 1 つ以上のマシンに対して機能を有効にするには、 **[選択したマシンで有効にします]** を選択し、各マシンの横にある **[追加]** を選択します。 このタスクにより、選択したマシン名が、機能の "保存した検索クエリ" コンピューター グループに追加されます。

## <a name="next-steps"></a>次のステップ

* VM の Update Management を使用するには、「[VM の更新プログラムとパッチの管理](manage-updates-for-vm.md)」を参照してください。

* Update Management で VM やサーバーを管理する必要がなくなったら、「[Update Management から VM を削除する](remove-vms.md)」を参照してください。

* Update Management の一般的なエラーのトラブルシューティングについては、[Update Management の問題のトラブルシューティング](../troubleshoot/update-management.md)に関する記事を参照してください。
