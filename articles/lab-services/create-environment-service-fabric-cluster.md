---
title: Azure DevTest Labs で Service Fabric クラスターがある環境を作成する | Microsoft Docs
description: 自己完結型 Service Fabric クラスターがある環境を作成し、そのクラスターをスケジュールを使用して開始および停止する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 1e192a2b27c9d617e43a56766431a0f40e87a752
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325250"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Azure DevTest Labs で Service Fabric クラスターがある環境を作成する
この記事では、Azure DevTest Labs で自己完結型 Service Fabric クラスターがある環境を作成する方法について説明します。 

## <a name="overview"></a>概要
DevTest Labs では、Azure Resource Manager テンプレートに定義されているように自己完結型テスト環境を作成できます。 これらの環境には、IaaS リソース (仮想マシンなど) と PaaS リソース (Service Fabric など) の両方が含まれます。 DevTest Labs では、仮想マシンを制御するためのコマンドを提供することで、環境内の仮想マシンを管理できます。 これらのコマンドによって、スケジュールに従って、仮想マシンを開始または停止できます。 同様に、DevTest Labs は、環境内の Service Fabric クラスターを管理するために役立てることもできます。 環境内の Service Fabric クラスターは、手動で、またはスケジュールを使用して、開始または停止できます。

## <a name="create-a-service-fabric-cluster"></a>Service Fabric クラスターの作成
Service Fabric クラスターは、DevTest Labs の環境を使用して作成されます。 各環境は、Git リポジトリの Azure Resource Manager テンプレートによって定義されます。 DevTest Labs 用の[パブリック Git リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)では、[ServiceFabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) フォルダー内に Service Fabric クラスターを作成するための Resource Manager テンプレートが含まれています。 

1. 最初に、次の記事の手順を使用して Azure DevTest Labs 内にラボを作成します。[ラボを作成する](devtest-lab-create-lab.md)。 **[パブリック環境]** オプションは既定で **[オン]** になっていることに注意してください。 
2. 次の手順に従って、自分のサブスクリプションに Service Fabric プロバイダーが登録されていることを確認します。
    1. 左側のナビゲーション メニューで **[サブスクリプション]** を選択し、自分の**サブスクリプション**を選択します。
    2. **[サブスクリプション]** ページで、左側のメニューの **[設定]** セクションから **[リソース プロバイダー]** を選択します。 
    3. **Microsoft.ServiecFabric** が登録されていない場合は、 **[登録]** を選択します。 
3. ラボの**DevTest Lab**ページで、ツール バーの **+ 追加** を選びます。 
    
    ![ツール バーの [追加] ボタン](./media/create-environment-service-fabric-cluster/add-button.png)
3. **[ベースの選択]** ページで、一覧から **[Service Fabric Lab クラスター]** を選択します。 

    ![一覧から [Service Fabric Lab クラスター] を選択](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. **[設定の構成]** ページで、次の手順に従います。 
    1. 自分のクラスター**環境**の **[名前]** を指定します。 これは、その中に Service Fabric クラスターが作成される Azure 上のリソース グループの名前です。 
    2. クラスターの仮想マシンの**オペレーティング システム (OS)** を選択します。 既定値は**Windows** です。
    3. クラスターの**管理者**の名前を指定します。 
    4. 管理者の**パスワード**を指定します。 
    5. **[証明書]** には、証明書の情報を Base64 でエンコードされた文字列として入力します。 証明書ファイルを作成するには、次の手順を実行します。
        1. [Git リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)から **Create-ClusterCertificate.ps1** ファイルをダウンロードします。 または、お使いのコンピューターにリポジトリを複製します。 
        2. **PowerShell**を起動します。 
        3. `.\Create-ClusterCertificate.ps1` コマンドを使用して **ps1** ファイルを実行します。 メモ帳にテキスト ファイルが開き、このページの証明書関連フィールドに入力するために必要な情報が表示されます 。 
    6. **証明書のパスワード**を入力します。
    7. 証明書の**サムプリント**を指定します。
    8. **[設定の構成]** ページで **[追加]** を選択します。 

        ![クラスター設定の構成](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. クラスターが作成されると、前の手順で指定した環境名を持つリソース グループが表示されます。 展開すると、その中の Service Fabric クラスターが表示されます。 リソース グループの状態が **[作成中]** のままで先に進まない場合は、ツールバーの **[更新]** を選択します。 **Service Fabric クラスター**環境では、Linux または Windows のいずれかで、5 つのノードと 1 つのノードの種類を持つ 1 つのクラスターが作成されます。

    次の例では、**mysfabricclusterrg** が、特に Service Fabric クラスター用に作成されたソース グループの名前です。 ラボ環境は、それが作成されたリソース グループの中で自己完結することに注意することが重要です。 つまり、環境を定義するテンプレートは、新しく作成されたリソース グループまたは[ラボによって使用するように構成された仮想ネットワーク](devtest-lab-configure-vnet.md)内のリソースのみにアクセスできます。 上記の例では、必要なすべてのリソースを同じリソース グループ内に作成しています。

    ![作成されたクラスター](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>クラスターを開始または停止する
DevTest ラボ ページ自体、または DevTest Labs によって提供される Service Fabric Cluster ページから、クラスターを開始または停止できます。 

### <a name="from-the-devtest-lab-page"></a>DevTest ラボ ページから
ラボの DevTest ラボ ページで、クラスターを停止または開始できます。 

1. 次の図に示されている Service Fabric クラスターの **3 つの点 ([...])** を選択します。 

    ![クラスターの開始コマンドと停止コマンド](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. コンテキスト メニューには、2 つのコマンド (クラスターを**開始する**ためのコマンドと**停止する**ためのコマンド) が表示されます。 [開始] コマンドは、クラスター内のすべてのノードを開始します。 [停止] コマンドは、クラスター内のすべてのノードを停止します。 クラスターが停止されると、Service Fabric クラスター自体は準備完了状態になりますが、ラボ内のクラスターに対して [開始] コマンドが再発行されるまで、利用可能なノードはありません。

    テスト環境で Service Fabric クラスターを使用する際に、いくつかの考慮事項があります。 ノードの再開始後に Service Fabric クラスターが完全にリハイドレートするには、多少時間がかかる場合があります。 シャットダウンから起動までの間データを保持するには、仮想マシンにアタッチされたマネージド ディスクにデータを保存する必要があります。 アタッチされたマネージド ディスクの使用はパフォーマンスへの影響が予期されるため、テスト環境でのみ使用することをお勧めします。 データを保存するために使用されるディスクがバックアップされない場合、クラスターに対して [停止] コマンドが発行されたときにデータは失われます。

### <a name="from-the-service-fabric-cluster-page"></a>Service Fabric クラスター ページから 
クラスターを開始または停止する別の方法があります。 

1. DevTest ラボ ページで、ツリー ビューから Service Fabric クラスターを選択します。 

    ![自分のクラスターを選択する](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. クラスターの **Service Fabric クラスター** ページでは、ツールバーにクラスターを開始または停止するコマンドが表示されます。 

    ![Service Fabric クラスター ページの [開始] コマンドと [停止] コマンド](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>自動開始と自動シャットダウンのスケジュールを構成する
Service Fabric クラスターは、スケジュールに従って開始または停止することもできます。 このエクスペリエンスは、ラボの仮想マシンに対するエクスペリエンスに似ています。 コストを削減するために、既定では、ラボで作成されるすべてのクラスターは、ラボの[シャットダウン ポリシー](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)に定義された時刻に自動的にシャットダウンされます。 クラスターをシャットダウンするかどうかを指定する、またはクラスターのシャットダウン時刻を指定することで上書きできます。 

![自動開始と自動シャットダウンを実行する既存のスケジュール](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>自動開始スケジュールをオプトインする
開始スケジュールをオプトインするには、次の手順を実行します。

1. 左側のメニューの **[自動開始]** を選択します。
2. **[この Service Fabric クラスターの自動開始をスケジュールできるようにする]** で **[オン]** を選択します。 このページは、ラボの所有者が、その仮想マシンまたは Service Fabric クラスターの自動開始をユーザーに許可している場合にのみ有効です。
3. ツールバーの **[保存]** を選択します。 

    ![[自動開始] ページ](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>自動シャットダウンの設定を構成する 
シャットダウンの設定を変更するには、次の手順を実行します。

1. 左側のメニューの **[自動シャットダウン]** を選択します。 
2. このページで、 **[有効化]** で **[オフ]** を選択することで、自動シャットダウンをオプトアウトできます。 
3. **[有効化]** で **[オン]** を選択した場合は、次の手順に従います。
    1. シャットダウンする**時刻**を指定します。
    2. 時刻の**タイムゾーン**を指定します。 
    3. 自動シャットダウンする前に DevTest Labs に**通知**を送信させるかどうかを指定します。 
    4. 通知オプションで **[はい]** を選択した場合は、通知を送信する **[Webhook URL]** または **[電子メール アドレス]** あるいはその両方を指定します。 
    5. ツールバーの **[保存]** を選択します。

        ![[自動シャットダウン] ページ](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Service Fabric クラスター内のノードを表示する
前の手順で表示した Service Fabric クラスター ページは、DevTest Labs ページ固有のものです。 クラスター内のノードは表示されません。 クラスターの詳細を表示するには、次の手順を実行します。

1. ラボの **DevTest ラボ** ページで、 **[マイ仮想マシン]** セクションのツリー ビューから**リソース グループ**を選択します。

    ![リソース グループの選択](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. **リソース グループ** ページで、一覧のリソース グループ内のすべてのリソースを表示します。 一覧から自分の **Service Fabric クラスター** を選択します。 

    ![一覧から自分のクラスターを選択する](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 自分のクラスター用の **Service Fabric クラスター** ページが表示されます。 これは、Service Fabric によって提供されるページです。 ノードやノードの種類などのクラスターに関するすべての情報が表示されます。

    ![Service Fabric クラスターのホーム ページ](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>次の手順
環境の詳細については、次の記事を参照してください。 

- [Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs でパブリック環境を構成して使用する](devtest-lab-configure-use-public-environments.md)
