---
title: Azure 以外のマシンを Azure Security Center に接続する
description: Azure 以外のマシンを Azure Security Center に接続する方法について説明します
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 68fcf8a8feb046fca2c26041d92264dd8b3a638e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465499"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Azure 以外のマシンを Security Center に接続する

Security Center では、Azure 以外のコンピューターのセキュリティの状況を監視できますが、最初にそれらを Azure に接続する必要があります。

Azure 以外のコンピューターは、次のいずれかの方法で接続できます。

- Azure Arc 対応サーバーを使用する (**推奨**)
- Azure portal の Security Center のページから ( **[作業の開始]** と **[インベントリ]** )

このページではそれぞれについて説明します。

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Azure Arc を使用して Azure 以外のマシンを追加する

Azure Arc 対応サーバーは、Azure 以外のマシンを Azure Security Center に追加する方法として推奨されます。

Azure Arc 対応サーバーがあるマシンは Azure リソースになり、他の Azure リソースのような推奨事項と共に、Security Center に表示されます。

さらに、Azure Arc 対応サーバーには、マシンでゲスト構成ポリシーを有効にするオプション、Log Analytics エージェントを拡張機能としてデプロイするオプション、他の Azure サービスを使用してデプロイを簡素化するオプションなど、強化された機能が用意されています。 利点の概要については、「[サポートされるシナリオ](../azure-arc/servers/overview.md#supported-scenarios)」を参照してください。

[Azure Arc 対応サーバー](../azure-arc/servers/overview.md)についての詳細を参照してください。

**Azure Arc をデプロイするには:**

- マシン 1 台の場合は、「[クイックスタート: Azure Arc 対応サーバーにハイブリッド マシンを接続する](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)」の手順に従います。
- 複数のマシンを大規模に Arc 対応サーバーに接続するには、「[ハイブリッド マシンを大規模に Azure に接続する](../azure-arc/servers/onboard-service-principal.md)」を参照してください

> [!TIP]
> AWS で実行されているマシンをオンボードしようとしている場合、Security Center の AWS 用コネクタにより、Azure Arc のデプロイは自動的かつ透過的に処理されます。 詳細については、「[Azure Security Center への AWS アカウントの接続](quickstart-onboard-aws.md)」を参照してください。

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Azure portal から Azure 以外のコンピューターを追加する

1. Security Center メニューから、 **[作業の開始]** ページを開きます。
1. **[作業の開始]** タブを選択します。

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="[作業の開始] ページの [作業の開始] タブ" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. **[非 Azure サーバーの追加]** の下で **[構成]** を選択します。

    > [!TIP]
    > **[インベントリ]** ページの **[非 Azure サーバーの追加]** から、マシンを追加するページを開くこともできます。
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="資産インベントリのページから Azure 以外のマシンを追加する":::

    Log Analytics ワークスペースの一覧が表示されます。 該当する場合、一覧には、自動プロビジョニングが有効になっているときに Security Center によって作成された既定のワークスペースが含まれます。 このワークスペースまたは使用する別のワークスペースを選択します。

    コンピューターを既存のワークスペースに追加することも、新しいワークスペースを作成することもできます。

1. 必要に応じて、新しいワークスペースを作成するには、 **[新しいワークスペースの作成]** を選択します。

1. ワークスペースの一覧から、関連するワークスペースの **[サーバーの追加]** を選択します。
    **[エージェント管理]** ページが表示されます。

    ここから、オンボードするマシンの種類に応じて、以下の関連する手順を選択します。

    - [Azure Stack Hub VM のオンボード](#onboard-your-azure-stack-hub-vms)
    - [Linux マシンのオンボード](#onboard-your-linux-machines)
    - [Windows マシンのオンボード](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-hub-vms"></a>Azure Stack Hub VM のオンボード

Azure Stack Hub VM を追加するには、 **[エージェント管理]** ページの情報が必要です。また、Azure Stack Hub インスタンス上で実行されている仮想マシンで、**Azure Monitor, Update and Configuration Management** という仮想マシンの拡張機能を構成する必要があります。

1. **[エージェント管理]** ページで、**ワークスペース ID** および **主キー** をメモ帳にコピーします。
1. **Azure Stack Hub** ポータルにログインし、 **[仮想マシン]** ページを開きます。
1. Security Center で保護する仮想マシンを選択します。
    >[!TIP]
    > Azure Stack Hub 上に仮想マシンを作成する方法については、[Windows 仮想マシン用のこちらのクイック スタート](/azure-stack/user/azure-stack-quick-windows-portal)または [Linux 仮想マシン用のこちらのクイック スタート](/azure-stack/user/azure-stack-quick-linux-portal)を参照してください。
1. **[拡張機能]** を選択します。 この仮想マシンにインストールされている仮想マシン拡張機能の一覧が表示されます。
1. **[追加]** タブを選択します。 **[新しいリソース]** メニューに、使用可能な仮想マシン拡張機能の一覧が表示されます。
1. **Azure Monitor, Update and Configuration Management** 拡張機能を選択し、 **[作成]** を選択します。 **[拡張機能のインストール]** 構成ページが開きます。
    >[!NOTE]
    > **Azure Monitor, Update and Configuration Management** 拡張機能がマーケットプレースに一覧表示されていない場合は、該当の Azure Stack Hub オペレーターに連絡して使用可能にしてください。
1. **[拡張機能のインストール]** 構成ページに、前の手順でメモ帳にコピーしておいた **ワークスペース ID** と **ワークスペース キー (主キー)** を貼り付けます。
1. 構成が完了したら、 **[OK]** を選択します。 拡張機能の状態は、**プロビジョニング成功** として表示されます。 Security Center に仮想マシンが表示されるまでに、最大で 1 時間かかる場合があります。

### <a name="onboard-your-linux-machines"></a>Linux マシンのオンボード

Linux マシンを追加するには、 **[エージェント管理]** ページの WGET コマンドが必要です。

1. **[エージェント管理]** ページで、**WGET** コマンドをメモ帳にコピーします。 このファイルを、Linux コンピューターからアクセスできる場所に保存します。
1. お使いの Linux コンピューターで、WGET コマンドを使用してファイルを開きます。 コンテンツ全体を選択してコピーし、ターミナル コンソールに貼り付けます。
1. インストールの完了後、*omsagent* がインストールされていることを検証するには、*pgrep* コマンドを実行します。 コマンドによって、*omsagent* PID が返されます。
    エージェントのログは、 */var/opt/microsoft/omsagent/\<workspace id>/log/* にあります。Security Center に新しい Linux マシンが表示されるまでに、最大で 30 分かかる場合があります。

### <a name="onboard-your-windows-machines"></a>Windows マシンのオンボード

Windows マシンを追加するには、 **[エージェント管理]** ページの情報が必要です。また、適切なエージェント ファイル (32/64 ビット) をダウンロードする必要があります。
1. ご使用のコンピューターのプロセッサの種類に適用できる **[Windows エージェントのダウンロード]** リンクを選択してセットアップ ファイルをダウンロードします。
1. **[エージェント管理]** ページで、**ワークスペース ID** および **主キー** をメモ帳にコピーします。
1. ダウンロードしたセットアップ ファイルをセットアップ先のコンピューターにコピーし、実行します。
1. インストール ウィザードの指示に従います ( **[次へ]** 、 **[同意する]** 、 **[次へ]** 、 **[次へ]** )。
    1. **[Azure Log Analytics]** ページで、メモ帳にコピーした **ワークスペース ID** と **ワークスペース キー (主キー)** を貼り付けます。
    1. コンピューターが Azure Government クラウド内の Log Analytics ワークスペースに報告する必要がある場合は、 **[Azure クラウド]** ドロップダウン リストから **[Azure US Government]** を選択します。
    1. コンピューターがプロキシ サーバーを介して Log Analytics サービスと通信する必要がある場合は、 **[詳細]** を選択し、プロキシ サーバーの URL とポート番号を指定します。
    1. すべての構成設定を入力したら、 **[次へ]** を選択します。
    1. **[インストールの準備完了]** ページで、適用する設定を確認し、 **[インストール]** を選択します。
    1. **[構成は正常に終了しました]** ページで **[完了]** を選択します。

完了すると、**コントロール パネル** に **Microsoft Monitoring Agent** が表示されます。 構成を検証して、エージェントが接続されていることを確認できます。

エージェントのインストールと構成の詳細については、[Windows コンピューターの接続](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)に関するページをご覧ください。

::: zone-end

## <a name="verifying"></a>確認中

おめでとうございます。 これで Azure と Azure 以外のマシンを 1 か所で確認できるようになりました。 [資産インベントリ](asset-inventory.md) ページを開いて、関連するリソースの種類にフィルターを適用します。 以下のアイコンで種類が区別されます。

  ![Azure 以外のマシンを表す ASC アイコン](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Azure 以外のマシン

  ![Azure のマシンを表す ASC アイコン](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

  ![Azure Arc サーバーの ASC アイコン](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Azure Arc 対応サーバー

## <a name="next-steps"></a>次の手順

このページでは、ご自身の Azure 以外のマシンを Azure Security Center に追加する方法について説明しました。 これらの状態を監視するには、次のページの説明に従って、インベントリ ツールを使用します。

- [資産インベントリを使用してリソースの調査と管理を行う](asset-inventory.md)