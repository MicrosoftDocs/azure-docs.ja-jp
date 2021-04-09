---
title: Automation アカウントから Azure Automation の変更履歴とインベントリを有効にする
description: この記事では、Automation アカウントで変更履歴とインベントリを有効にする方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 32fb95c88d632cc2c51cd2390f0244e9c1927051
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585887"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Automation アカウントで変更履歴とインベントリを有効にする

この記事では、Automation アカウントを使用して、お客様の環境内の VM に対して[変更履歴とインベントリ](overview.md)を有効にする方法について説明します。 Azure VM を大規模に有効にするには、変更履歴とインベントリを使用して既存の VM を有効にする必要があります。

> [!NOTE]
> 変更履歴とインベントリを有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../automation-security-overview.md)。
* [仮想マシン](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

https://portal.azure.com で Azure にサインインします。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

1. Automation アカウントに移動し、 **[構成管理]** で、 **[インベントリ]** または **[変更の追跡]** を選択します。

2. Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** をクリックして、変更履歴とインベントリを有効にします。 セットアップが完了するまでに最大 15 分かかります。

    ![Change Tracking と Inventory の有効化](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Azure VM の有効化

1. Automation アカウントから、 **[構成管理]** の **[インベントリ]** または **[Change tracking]\(変更履歴\)** を選択します。

2. **[+ Azure VM の追加]** をクリックし、リストから 1 つ以上の VM を選択します。 有効にできない仮想マシンは灰色で表示され、選択できません。 Azure VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。 

3. **[Enable]\(有効にする\)** をクリックすると、選択された VM が、この機能の "保存した検索条件" コンピューター グループに追加されます。 詳細については、「[Change Tracking とインベントリのデプロイのスコープを制限する](manage-scope-configurations.md)」を参照してください。

      [ ![Azure VM の有効化](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Azure 以外の VM の有効化

Azure に存在しないマシンは手動で追加する必要があります。 最初にマシンを [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)に接続し、次に Azure Policy を使用して [*Linux* または *Windows* Azure Arc マシンに Log Analytics エージェントをデプロイする](../../governance/policy/samples/built-in-policies.md#monitoring)組み込みポリシーに割り当てることにより、Windows または Linux 用の Log Analytics エージェントをインストールすることをお勧めします。 Azure Monitor for VMs を使用してマシンの監視も行う場合は、代わりに [Azure Monitor for VMs を有効にする](../../governance/policy/samples/built-in-initiatives.md#monitoring)イニシアティブを使用します。

1. Automation アカウントから、 **[構成管理]** の **[インベントリ]** または **[Change tracking]\(変更履歴\)** を選択します。

2. **[Add non-Azure machine]\(Azure 以外のマシンを追加する\)** をクリックします。 このアクションにより、新しいブラウザー ウィンドウが開きます。このウィンドウには、変更履歴とインベントリの操作に対するレポートをマシンが開始できるように、[Windows 用の Log Analytics エージェントをインストールして構成する方法](../../azure-monitor/agents/log-analytics-agent.md)が示されます。 Operations Manager によって現在管理されているマシンを有効にする場合、新しいエージェントは必要ありません。ワークスペース情報は、既存のエージェントに入力されます。

## <a name="enable-machines-in-the-workspace"></a>ワークスペースでのマシンの有効化

手動でインストールしたマシンや、既にワークスペースへのレポートを行っているマシンは、Azure Automation に追加して変更履歴とインベントリを有効にする必要があります。

1. Automation アカウントから、 **[構成管理]** の **[インベントリ]** または **[Change tracking]\(変更履歴\)** を選択します。

2. **[マシンの管理]** を選択します。 前に **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択している場合、 **[マシンの管理]** オプションはグレー表示される可能性があります。

    ![保存した検索条件](media/enable-from-automation-account/manage-machines.png)

3. 使用可能なすべてのマシンに対して変更履歴とインベントリを有効にするには、 **[マシンの管理]** ページの **[使用可能なすべてのマシンで有効にします]** を選択します。 この操作によって、コンピューターを個別に追加するコントロールが無効になり、ワークスペースに報告するすべてのコンピューターが、コンピューター グループの保存された検索条件のクエリに追加されます。 選択されている場合は、 **[マシンの管理]** オプションが無効になります。

4. 使用可能なすべてのマシンと今後のマシンに対してこの機能を有効にするには、 **[使用可能なマシンと今後のマシンすべてで有効にします]** を選択します。 このオプションを選択すると、保存した検索条件とスコープ構成がワークスペースから削除され、ワークスペースへのレポートを行うすべての Azure マシンと Azure 以外のマシンに対して機能が開放されます。 選択された場合は、残されたスコープ構成がないため、 **[マシンの管理]** オプションが永続的に無効になります。

    > [!NOTE]
    > このオプションで Log Analytics 内の保存済み検索とスコープ構成が削除されるため、このオプションを選択する前に、Log Analytics Workspace で削除ロックを削除することが重要です。 そうしない場合、オプションを選択しても構成を削除できず、手動で削除する必要があります。

5. 必要であれば、最初に保存した検索を再度追加することで、スコープ構成を追加し直すことができます。 詳細については、「[Change Tracking とインベントリのデプロイのスコープを制限する](manage-scope-configurations.md)」を参照してください。

6. 1 つ以上のマシンに対して機能を有効にするには、 **[選択したマシンで有効にします]** を選択し、機能を有効にする各マシンの横にある **[追加]** をクリックします。 このタスクにより、選択したマシン名が、機能の "保存した検索クエリ" コンピューター グループに追加されます。

## <a name="next-steps"></a>次のステップ

* この機能の操作の詳細については、[変更履歴の管理](manage-change-tracking.md)と[インベントリの管理](manage-inventory-vms.md)に関するページをご覧ください。

* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](../troubleshoot/change-tracking.md)」を参照してください。
