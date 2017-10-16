---
title: "インベントリ収集による Azure VM の管理 |Microsoft Docs"
description: "インベントリ収集による VM の管理"
services: automation
keywords: "インベントリ, オートメーション, 変更, 追跡"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>インベントリ収集による Azure 仮想マシンの管理

Azure 仮想マシンでインベントリの追跡を使用するには、そのマシンのリソース ページから有効にします。 この方法では、ブラウザーベースのユーザー インターフェイスで、インベントリ収集を設定および構成できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
Azure 仮想マシンをお持ちでない場合は、開始する前に[仮想マシン](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)を作成してください。

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>仮想マシン リソース ページからインベントリ収集を有効にする

1. 左側の画面の **[Virtual Machines]** を選択します。
1. 一覧から仮想マシンを選択します。
1. リソース メニューから、**[操作]** の下にある **[Inventory (Preview) (インベントリ (プレビュー))]** を選択します。
1. ページの上部に、このソリューションが有効でないことを通知するバナーが表示されます。 バナーをクリックすると、このソリューションが有効になります。
1. データ ログを格納する Log Analytics ワークスペースを選択します。 そのリージョンで利用可能なワークスペースがない場合は、既定のワークスペースと Automation アカウントを作成するよう求められます。 **[Enable (有効化)]** をクリックして、コンピューターのオンボードを開始します。

   ![オンボード オプションの表示](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. ステータス バーで、ソリューションが有効にされていることが通知されます。 このプロセスには最大 15 分かかることがあります。 この期間中、ブレードは閉じるか開いたままにすることができます。ソリューションが有効になると、そのことがレポートされます。 デプロイの状態は通知ペインから監視することができます。

   ![オンボードの直後にインベントリ ソリューションを表示する](./media/automation-vm-inventory/inventory-onboarded.png)

1. デプロイが完了すると、ステータス バーは表示されなくなります。 この時点では、システムがまだインベントリ データを収集しているため、データが表示されない可能性があります。 データの完全な収集には 24 時間かかることがあります。

## <a name="configure-your-inventory-settings"></a>インベントリ設定を構成する

既定では、ソフトウェア、Windows サービス、および Linux デーモンが収集対象として構成されています。 Windows レジストリとファイル インベントリを収集するには、インベントリ収集設定を構成します。

1. **[Inventory (Preview) (インベントリ (プレビュー))]** ビューで、ページ上部にある **[Edit Settings (設定の編集)]** ボタンをクリックします。
2. 新しい収集設定を追加するには、**[Windows Registry (Windows レジストリ)]**、**[Windows Files (Windows ファイル)]**、および **[Linux Files (Linux ファイル)]** のタブを使用して、追加する設定カテゴリに移動します。 ページの上部にある **[追加]** をクリックします。
3. 各設定プロパティの詳細と説明については、[インベントリのドキュメント ページ](https://aka.ms/configinventorydocs)を参照してください。

## <a name="disconnecting-your-virtual-machine-from-management"></a>仮想マシンを管理から切断する

インベントリ管理からマシンを除外するには、次のようにします。

1. Azure Portal の左側のメニューから **[Log Analytics]** をクリックし、仮想マシンのオンボード時に使用したワークスペースを選択します。
1. [Log Analytics] ページで、リソース メニューの **[ワークスペースのデータ ソース]** の下にある **[仮想マシン]** を選択します。 
1. 切断する仮想マシンを一覧から選択します。 マシンの **[OMS 接続]** 列の "This workspace (このワークスペース)" というテキストの横に、緑色のチェック マークが表示されます。 次のページの上部にある **[切断]** をクリックして、確認ダイアログで **[はい]** をクリックすると、マシンが管理から切断されます。

## <a name="next-steps"></a>次のステップ

* 仮想マシン上のファイルとレジストリ設定内の変更を管理する方法については、「[変更の追跡](../log-analytics/log-analytics-change-tracking.md)」を参照してください。
* 仮想マシン上の Windows とパッケージの更新を管理する方法については、「[更新管理](../operations-management-suite/oms-solution-update-management.md)」を参照してください。

