---
title: OMS の更新プログラムの展開を Azure に移行する
description: この記事では、既存の OMS の更新プログラムの展開を Azure に移行する方法について説明します
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 08b3f11f3e44c6580df9942aab2a890115c79ba3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849498"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>OMS の更新プログラムの展開を Azure に移行する

Operations Management Suite (OMS) ポータルは[非推奨](../azure-monitor/platform/oms-portal-transition.md)となっています。 Update Management 用の OMS ポータルで使用できたすべての機能が、Azure portal で使用できます。 この記事では、Azure portal に移行するために必要な情報を提供しています。

## <a name="key-information"></a>重要な情報

* 既存の展開は引き続き機能します。 Azure で展開を再作成したら、OMS から以前の展開を削除できます。
* OMS にあった既存のすべての機能は、Azure で使用できます。Update Management に関する詳細については、「[Azure の Update Management ソリューション](automation-update-management.md)」を参照してください。

## <a name="access-the-azure-portal"></a>Azure portal にアクセスする

OMS ワークスペースで、 **[Azure で開く]** をクリックします。 これにより OMS が使用していた Log Analytics ワークスペースに移動します。

![Azure で開く - OMS ポータル](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Azure portal で、 **[Automation アカウント]** をクリックします。

![Azure Monitor ログ](media/migrate-oms-update-deployments/log-analytics.png)

Automation アカウントから、 **[Update Management]** をクリックして、Update Management を開きます。

![更新管理](media/migrate-oms-update-deployments/azure-automation.png)

今後は、 **[すべてのサービス]** の **[管理ツール]** の下で **[Automation アカウント]** を選択し、該当する Automation アカウントを選択し、 **[Update Management]** をクリックすると、直接 Azure portal に移動できるようになります。

## <a name="recreate-existing-deployments"></a>既存の展開を再作成する

OMS ポータルで作成されたすべての更新プログラムの展開には、[保存された検索条件](../azure-monitor/platform/computer-groups.md)があります。これはコンピューター グループとも呼ばれ、存在する更新プログラムの展開と同じ名前を持ちます。 保存された検索条件には、更新プログラムの展開でスケジュールされていたコンピューターの一覧が含まれています。

![更新管理](media/migrate-oms-update-deployments/oms-deployment.png)

この既存の保存された検索条件を使用するには、次の手順に従います。

新しい更新プログラムの展開を作成するには、Azure portal に移動して、使用されている Automation アカウントを選択し、 **[Update Management]** をクリックします。 **[更新プログラムの展開のスケジュール]** をクリックします。

![更新プログラムの展開のスケジュール](media/migrate-oms-update-deployments/schedule-update-deployment.png)

**[新しい更新プログラムの展開]** ウィンドウが開きます。 次の表で説明されているプロパティの値を入力し、 **[作成]** をクリックします。

更新するマシンには、既存の OMS の展開で使用された保存された検索条件を選択します。

| プロパティ | Description |
| --- | --- |
|Name |更新プログラムの展開を識別する一意の名前。 |
|オペレーティング システム| **Linux** か **Windows** を選択します。|
|更新するマシン |保存した検索条件、インポートしたグループを選択するか、ドロップダウンから [マシン] を選択し、個別のマシンを選択します。 **[マシン]** を選択すると、マシンの準備状況が **[エージェントの更新の準備]** 列に示されます。</br> Azure Monitor ログでコンピューター グループを作成するさまざまな方法については、[Azure Monitor ログのコンピューター グループ](../azure-monitor/platform/computer-groups.md)に関するページを参照してください |
|更新プログラムの分類|必要な更新プログラムの分類をすべて選択します。 CentOS ではこれは既定ではサポートされていません。|
|除外する更新プログラム|除外する更新プログラムを入力します。 Windows の場合は、KB 記事を **KB** プレフィックスを付けないで入力します。 Linux の場合は、パッケージ名を入力するか、ワイルドカード文字を使用します。  |
|スケジュール設定|開始する時刻を選択し、繰り返しの設定として、 **[1 回]** または **[定期的]** のいずれかを選択します | 
| メンテナンス期間 |更新プログラムに対して設定された分数です。 30 分未満、あるいは 6 時間を超える値を指定することはできません。 |
| 再起動制御| 再起動の処理方法を決定します。</br>使用できるオプションは次のとおりです。</br>必要に応じて再起動 (既定値)</br>常に再起動</br>再起動しない</br>Only reboot - will not install updates (再起動のみ - 更新プログラムをインストールしない)|

**[スケジュールされた更新プログラムの展開]** をクリックして、新しく作成された更新プログラムの展開の状態を表示します。

![新しい更新プログラムの展開](media/migrate-oms-update-deployments/new-update-deployment.png)

既に説明したように、Azure portal を通じて新しい展開が構成されると、OMS ポータルから既存の展開を削除できます。

## <a name="next-steps"></a>次の手順

Update Management の詳細については、[Update Management](automation-update-management.md) に関する記事を参照してください。
