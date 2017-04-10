---
title: "Azure Log Analytics のコンテナー ソリューション | Microsoft Docs"
description: "Log Analytics のコンテナー ソリューションを使用すると、Docker および Windows コンテナー ホストを&1; か所で表示して管理できます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 45b07f5dd29b7e920ac2abe6765020e0337ecf95
ms.lasthandoff: 03/11/2017


---
# <a name="containers-preview-solution-log-analytics"></a>Log Analytics のコンテナー (プレビュー) ソリューション
この記事では、Docker および Windows コンテナー ホストを&1; か所で表示して管理できる、Log Analytics のコンテナー ソリューションを設定して使用する方法について説明します。 Docker は、IT インフラストラクチャへのソフトウェアのデプロイを自動化するコンテナーを作成するために使用されるソフトウェア仮想化システムです。

このソリューションを使用すれば、コンテナー ホストで実行されているコンテナーとコンテナー内で実行されているイメージを確認できます。 コンテナーで使用されるコマンドを示す詳細な監査情報を確認できます。 また、Docker または Windows ホストをリモートで確認しなくても、一元化されたログを表示および検索して、コンテナーのトラブルシューティングを行うことができます。 ホストで余分なリソースを使用しているコンテナーや、ノイズが大きいコンテナーを特定できます。 また、コンテナーについて、CPU、メモリ、ストレージ、ネットワークの使用量とパフォーマンスに関する情報を一元的に確認できます。 Windows を実行しているコンピューターでは、Windows Server、Hyper-V、Docker の各コンテナーのログを一元化して比較できます。

次のダイアグラムは、OMS を使用するさまざまなコンテナー ホストとエージェント間の関係を示しています。

![コンテナー ダイアグラム](./media/log-analytics-containers/containers-diagram.png)

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

コンテナー ソリューションを OMS ワークスペースに追加します。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) から追加するか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページで説明されている手順に従って追加してください。

OMS と共に Docker をインストールして使用する方法はいくつかあります。

* サポートされている Linux オペレーティング システムでは、Docker をインストールして実行し、OMS Agent for Linux をインストールして構成します。
* CoreOS では、OMS Agent for Linux を実行できません。 代わりに、OMS Agent for Linux のコンテナー化されたバージョンを実行します。
* Windows Server 2016 および Windows 10 では、Docker エンジンとクライアントをインストールした後、エージェントを接続して情報を収集し、Log Analytics に送信します。


お使いのコンテナー ホストでサポートされている Docker および Linux オペレーティング システムのバージョンは、[GitHub](https://github.com/Microsoft/OMS-docker) で確認できます。

Windows を実行しているコンピューターに Docker エンジンをインストールして構成する方法の詳細については、「[Windows 上の Docker エンジン](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)」をご覧ください。

> [!IMPORTANT]
> Docker は、[OMS Agent for Linux](log-analytics-linux-agents.md) をコンテナー ホストにインストールする**前**に実行しておく必要があります。 Docker をインストールするより先にエージェントをインストールしてある場合は、OMS Agent for Linux を再インストールする必要があります。 Docker の詳細については、[Docker の Web サイト](https://www.docker.com)を参照してください。
>
>

コンテナーを監視するには、コンテナー ホストで次の設定を構成しておくことが必要です。

## <a name="configure-settings-for-a-linux-container-host"></a>Linux コンテナー ホストの設定の構成

次の x64 Linux ディストリビューションは、コンテナー ホストとしてサポートされます。

- Ubuntu 14.04 LTS、16.04 LTS
- CoreOS (Stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- CentOS 7
- SLES 12
- RHEL 7.2

Docker をインストールした後で、コンテナー ホストの次の設定を使用して、Docker で使用するためにエージェントを構成します。 [OMS ワークスペース ID とキー](log-analytics-linux-agents.md)が必要になります。


### <a name="for-all-linux-container-hosts-except-coreos"></a>CoreOS を除くすべての Linux コンテナー ホスト

- 「[Steps to install the OMS Agent for Linux (OMS Agent for Linux のインストール手順)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)」に従って操作します。

### <a name="for-all-linux-container-hosts-including-coreos"></a>CoreOS を含むすべての Linux コンテナー ホスト

監視する OMS コンテナーを起動します。 次の例に変更を加えて使用してください。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>インストール済みの Linux エージェントからコンテナー内のエージェントの使用への切り替え
これまで直接インストールされたエージェントを使用しており、今後はコンテナーで実行されているエージェントを使用したい場合は、まず OMSAgent を削除する必要があります。 [OMS Agent for Linux のインストール手順](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)に関するページを参照してください。

## <a name="supported-windows-versions"></a>サポートされている Windows バージョン

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional または Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Windows でサポートされている Docker のバージョン

- Docker 1.12 ～ 1.13

### <a name="preparation-before-installing-agents"></a>エージェントをインストールする前の準備

Windows を実行しているコンピューターにエージェントをインストールする前に、Docker サービスを構成する必要があります。 構成により、Windows エージェントまたは Log Analytics 仮想マシン拡張機能は、エージェントが Docker デーモンにリモートでアクセスできるように Docker TCP ソケットを使用できるようになり、監視用のデータをキャプチャすることが可能になります。

Windows を実行しているコンピューターでは、パフォーマンス データはサポートされていません。

Windows での Docker デーモンの構成方法の詳細については、「[Windows 上の Docker エンジン](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)」をご覧ください。

#### <a name="to-start-docker-and-verify-its-configuration"></a>Docker を起動して構成を確認するには

1.    Windows PowerShell で、TCP パイプと名前付きパイプを有効にします。

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd -H npipe:// -H 0.0.0.0:2375 --register-service
    Start-Service docker
    ```

2.    netstat を使用して構成を確認します。 ポート 2375 が表示されます。

    ```
    PS C:\Users\User1> netstat -a | sls 2375

    TCP    127.0.0.1:2375         Win2016TP5:0           LISTENING
    TCP    127.0.0.1:2375         Win2016TP5:49705       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49706       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49707       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49708       ESTABLISHED
    TCP    127.0.0.1:49705        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49706        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49707        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49708        Win2016TP5:2375        ESTABLISHED
    ```

### <a name="install-windows-agents"></a>Windows エージェントのインストール

Windows および Hyper-V コンテナーの監視を有効にするには、コンテナー ホストである Windows コンピューターにエージェントをインストールします。 Windows を実行しているオンプレミス環境のコンピューターの場合、[Log Analytics への Windows コンピューターの接続](log-analytics-windows-agents.md)に関する記事をご覧ください。 Azure で実行されている仮想マシンの場合、[仮想マシン拡張機能](log-analytics-azure-vm-extension.md)を使用して Log Analytics に接続します。

コンテナー ソリューションが正しく設定されていることを確認するには、次の手順を実行します。

- 管理パックが正常にダウンロードされているかどうかを確認し、*ContainerManagement.xxx* を探します。
    - ファイルは C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs フォルダーにあります。
- **[コントロール パネル]** > **[システムとセキュリティ]** に移動して、OMS ワークスペース ID が正しいことを確認します。
    - **Microsoft Monitoring Agent** を開き、ワークスペース情報が正しいことを確認します。


## <a name="containers-data-collection-details"></a>コンテナーのデータ収集の詳細
コンテナー ソリューションでは、有効化されたエージェントを使用して、コンテナー ホストとコンテナーからさまざまなパフォーマンス メトリックとログ データを収集します。

次の表は、コンテナーのデータ収集手段とデータ収集方法に関する各種情報をまとめたものです。

| プラットフォーム | [OMS Agent for Linux](log-analytics-linux-agents.md) | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![[はい]](./media/log-analytics-containers/oms-bullet-green.png) |![なし](./media/log-analytics-containers/oms-bullet-red.png) |![いいえ](./media/log-analytics-containers/oms-bullet-red.png) |![いいえ](./media/log-analytics-containers/oms-bullet-red.png) |![なし](./media/log-analytics-containers/oms-bullet-red.png) |3 分おき |

| プラットフォーム | [Windows エージェント](log-analytics-windows-agents.md) | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![[はい]](./media/log-analytics-containers/oms-bullet-green.png) |![なし](./media/log-analytics-containers/oms-bullet-red.png) |![いいえ](./media/log-analytics-containers/oms-bullet-red.png) |![いいえ](./media/log-analytics-containers/oms-bullet-red.png) |![なし](./media/log-analytics-containers/oms-bullet-red.png) |3 分おき |

| プラットフォーム | [Log Analytics VM 拡張機能](log-analytics-azure-vm-extension.md) | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![はい](./media/log-analytics-containers/oms-bullet-green.png) |![なし](./media/log-analytics-containers/oms-bullet-red.png) |![いいえ](./media/log-analytics-containers/oms-bullet-red.png) |![いいえ](./media/log-analytics-containers/oms-bullet-red.png) |![なし](./media/log-analytics-containers/oms-bullet-red.png) |3 分おき |

次の表に、コンテナー ソリューションによって収集されるデータ型およびログ検索と結果に使用されるデータ型の例を示します。 ただし、Windows を実行しているコンピューターでは、パフォーマンス データはまだサポートされていません。

| データ型 | ログ検索のデータ型 | フィールド |
| --- | --- | --- |
| ホストとコンテナーのパフォーマンス | `Type=Perf` | Computer、ObjectName、CounterName (%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network)、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| コンテナー インベントリ | `Type=ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContinerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| コンテナー イメージ インベントリ | `Type=ContainerImageInventory` | TimeGenerated、Computer、Image、ImageTag、ImageSize、VirtualSize、Running、Paused、Stopped、Failed、SourceSystem、ImageID、TotalContainer |
| コンテナー ログ | `Type=ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| コンテナー サービス ログ | `Type=ContainerServiceLog`  | TimeGenerated、Computer、TimeOfCommand、Image、Command、SourceSystem、ContainerID |

## <a name="monitor-containers"></a>コンテナーの監視
OMS ポータルでソリューションを有効にすると、コンテナー ホストとホストで実行されているコンテナーに関する情報が **[コンテナー]** タイルに表示されます。

![[コンテナー] タイル](./media/log-analytics-containers/containers-title.png)

このタイルには、環境内に存在するコンテナーの数と、それらのコンテナーの状態 (失敗、実行中、停止) の概要が示されます。

### <a name="using-the-containers-dashboard"></a>コンテナー ダッシュボードの使用
**[コンテナー]** タイルをクリックします。 ここに表示される情報は、次の項目で整理されます。

* コンテナーのイベント
* エラー
* コンテナーの状態
* コンテナー イメージ インベントリ
* CPU とメモリのパフォーマンス

ダッシュボードの各ペインは、収集されたデータに対して実行された検索を視覚的に表したものです。

![コンテナー ダッシュボード](./media/log-analytics-containers/containers-dash01.png)

![コンテナー ダッシュボード](./media/log-analytics-containers/containers-dash02.png)

**[Container Status (コンテナーの状態)]** ブレードで、次に示すように最上部をクリックします。

![コンテナーの状態](./media/log-analytics-containers/containers-status.png)

[ログ検索] が開き、ホストとホストで実行されているコンテナーに関する情報が表示されます。

![コンテナーの [ログ検索]](./media/log-analytics-containers/containers-log-search.png)

ここで検索クエリを編集して、関心のある情報のみが見つかるように変更できます。 ログ検索の詳細については、「[Log Analytics におけるログの検索](log-analytics-log-searches.md)」を参照してください。

たとえば、検索クエリの **[実行中]** を **[停止]** に変更して、実行中のコンテナーの代わりに停止している全コンテナーが表示されるようにすることもできます。

## <a name="troubleshoot-by-finding-a-failed-container"></a>失敗したコンテナーを特定してトラブルシューティングを行う
ゼロ以外の終了コードで終了したコンテナーは、OMS によって **[失敗]** とマークされます。 **[失敗したコンテナー]** ブレードで、環境におけるエラーと失敗の概要を確認できます。

### <a name="to-find-failed-containers"></a>失敗したコンテナーを特定するには
1. **[Container Events (コンテナー イベント)]** ブレードをクリックします。  
   ![コンテナー イベント](./media/log-analytics-containers/containers-events.png)
2. [ログ検索] が開き、次のようにコンテナーの状態が表示されます。  
   ![コンテナーの状態](./media/log-analytics-containers/containers-container-state.png)
3. 次に、失敗の値をクリックして、停止または失敗したイメージのサイズや数などの追加情報を表示できます。 **[表示数を増やす]** を展開してイメージ ID を表示します。  
   ![失敗したコンテナー](./media/log-analytics-containers/containers-state-failed.png)
4. 次に、このイメージを実行しているコンテナーを検索します。 検索クエリに以下を入力します。
   `Type=ContainerInventory <ImageID>` これによりログが表示されます。 スクロールして失敗したコンテナーを表示できます。  
   ![失敗したコンテナー](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>コンテナー データのログの検索
特定のエラーのトラブルシューティングを実行する際には、環境のどこでそのエラーが発生しているのかを確認すると役立つ場合があります。 次のログの種類は、目的の情報を返すクエリを作成するうえで役立ちます。

* **ContainerInventory** – この種類は、コンテナーの場所、コンテナーの名前、実行中のイメージに関する情報が必要な場合に使用します。
* **ContainerImageInventory** – この種類は、イメージ別に整理された情報を見つける場合や、イメージの ID やサイズなどのイメージ情報を確認する場合に使用します。
* **ContainerLog** – この種類は、特定のエラー ログの情報やエントリを見つける場合に使用します。
* **ContainerServiceLog** – この種類は、開始、停止、削除、プルのコマンドなど、Docker デーモンの監査証跡情報を見つける場合に使用します。

### <a name="to-search-logs-for-container-data"></a>コンテナー データのログを検索するには
* 最近失敗したことがわかっているイメージを選択し、そのエラー ログを見つけます。 まず、**ContainerInventory** 検索で、そのイメージを実行しているコンテナー名を特定します。 たとえば、`Type=ContainerInventory ubuntu Failed` を検索します。  
    ![Ubuntu コンテナーの検索](./media/log-analytics-containers/search-ubuntu.png)

  **[名前]** の横にあるコンテナーの名前をメモし、そのログを検索します。 この例では `Type=ContainerLog adoring_meitner` です。

**パフォーマンス情報の表示**

クエリの作成を始めたばかりの段階では、何が可能かを先に確認しておくと効率的です。 たとえば、すべてのパフォーマンス データを確認するには、次の検索クエリを入力して広範なクエリを試してみてください。

```
Type=Perf
```

![コンテナーのパフォーマンス](./media/log-analytics-containers/containers-perf01.png)

結果の **[メトリック]** という単語をクリックすると、よりグラフィカルな形式で表示できます。

![コンテナーのパフォーマンス](./media/log-analytics-containers/containers-perf02.png)

表示されているパフォーマンス データのスコープを特定のコンテナーに制限するには、その名前をクエリの右側に入力します。

```
Type=Perf <containerName>
```

そうすると、個々のコンテナーについて収集されたパフォーマンス メトリックの一覧が表示されます。

![コンテナーのパフォーマンス](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>検索クエリの例
クエリの作成の際には、多くの場合、1 ～&2; 個の例で始め、その後環境に合わせて変更するとうまくいきます。 まず、**[注目すべきクエリ]** ブレードを試してみると、より高度なクエリを作成するのに役立ちます。

![コンテナーのクエリ](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>ログ検索クエリの保存
クエリの保存は、Log Analytics の標準的な機能です。 クエリを保存しておけば、後で使えるように、便利なクエリを取っておくことができます。

作成したクエリが便利であることがわかったら、**[ログ検索]** ページの上部にある [お気に入り] をクリックして保存してください。 後で **[マイ ダッシュボード]** ページで簡単にアクセスできます。

## <a name="next-steps"></a>次のステップ
* [ログを検索](log-analytics-log-searches.md) して、詳細なコンテナー データ レコードを確認します。

