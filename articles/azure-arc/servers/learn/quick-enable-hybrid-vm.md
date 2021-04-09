---
title: Azure Arc 対応サーバーにハイブリッド マシンを接続する
description: Azure Arc 対応サーバーにハイブリッド マシンを接続して登録する方法について説明します。
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: c52b8d1f7098a7a2a88a9770a3b768b7fea31775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646828"
---
# <a name="quickstart-connect-hybrid-machines-with-azure-arc-enabled-servers"></a>クイックスタート: Azure Arc 対応サーバーにハイブリッド マシンを接続する

[Azure Arc 対応サーバー](../overview.md)を使用すると、オンプレミス、エッジ、マルチクラウドの垣根を越え、それらの環境でホストされている Windows マシンや Linux マシンを管理することができます。 このクイックスタートでは、Azure の外部でホストされた Windows マシンまたは Linux マシンを Arc 対応サーバーで管理できるよう、それらのマシンに Connected Machine エージェントをデプロイして構成します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* Arc 対応サーバーの Hybrid Connected Machine エージェントをデプロイするには、マシンにエージェントをインストールして構成するための管理者アクセス許可が必要です。 Linux では root アカウントを使用し、Windows ではローカルの Administrators グループのメンバー アカウントを使用します。

* 開始する前に、必ずエージェントの[前提条件](../agent-overview.md#prerequisites)を確認し、次の点を確認してください。

    * サポートされている[オペレーティング システム](../agent-overview.md#supported-operating-systems)がターゲット マシンで実行されていること。

    * [必要な Azure ロール](../agent-overview.md#required-permissions)への割り当てが、ご使用のアカウントに許可されていること。

    * インターネット経由で通信するためにマシンがファイアウォールやプロキシ サーバーを介して接続する場合、[一覧](../agent-overview.md#networking-configuration)に記載されている URL がブロックされていないこと。

    * Azure Arc 対応サーバーがサポートされるのは、[こちら](../overview.md#supported-regions)に記載されたリージョンのみです。

> [!WARNING]
> Linux ホスト名または Windows コンピューター名には、予約語や商標を使用できません。使用した場合、接続されているマシンを Azure に登録しようとすると失敗します。 予約語の一覧については、「[予約されたリソース名のエラーを解決する](../../../azure-resource-manager/templates/error-reserved-resource-name.md)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Azure リソースプロバイダーを登録する

Azure Arc 対応サーバーは、このサービスを使用するために、サブスクリプション内の次の Azure リソース プロバイダーに依存しています。

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

次のコマンドを使用してこれらを登録します。

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>インストール スクリプトを生成する

ダウンロードとインストールを自動化し、Azure Arc との接続を確立するスクリプトは、Azure portal から入手できます。 そのプロセスを完了するには、次の手順を実行します。

1. Azure portal で、 **[すべてのサービス]** をクリックし、 **[サーバー - Azure Arc]** を検索して選択することで、Azure Arc サービスを起動します。

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="[すべてのサービス] で Arc 対応サーバーを検索する" border="false":::

1. **[サーバー - Azure Arc]** ページで、左上の **[追加]** を選択します。

1. **[メソッドを選択してください]** ページで、 **[Add servers using interactive script]\(対話型スクリプトを使用してサーバーを追加する\)** タイルを選択し、 **[スクリプトの生成]** を選択します。

1. **[スクリプトの生成]** ページで、Azure 内でマシンを管理するサブスクリプションとリソース グループを選択します。 マシンのメタデータが格納される Azure の場所を選択します。 この場所は、リソース グループの場所と同じ場合も異なる場合もあります。

1. **[Prerequisites]\(前提条件\)** ページで情報を確認し、 **[次: リソースの詳細]** を選択します。

1. **[リソースの詳細]** ページで、以下を設定します。

    1. **[リソース グループ]** ドロップダウンの一覧で、マシンを管理するリソース グループを選択します。
    1. **[リージョン]** ドロップダウンの一覧で、サーバーのメタデータを格納する Azure リージョンを選択します。
    1. **[オペレーティング システム]** ドロップダウンの一覧で、スクリプトを実行するように構成されているオペレーティング システムを選択します。
    1. マシンがプロキシ サーバー経由でインターネットに接続して通信を行っている場合は、プロキシ サーバーの IP アドレス、またはマシンでプロキシ サーバーとの通信に使用されている名前とポート番号を指定します。 `http://<proxyURL>:<proxyport>` の形式で値を入力します。
    1. **タグ** を選択します。

1. **[タグ]** ページで、提案されている既定の **物理的な場所のタグ** を確認し、値を入力するか、**カスタム タグ** を 1 つ以上指定して標準をサポートします。

1. **[Next:Download and run script]\(次: スクリプトをダウンロードして実行する\)** を選択します。

1. **[Download and run script]\(次: スクリプトをダウンロードして実行する\)** ページで概要情報を確認し、 **[ダウンロード]** を選択します。 引き続き変更が必要な場合は、 **[前へ]** を選択します。

## <a name="install-the-agent-using-the-script"></a>スクリプトを使用してエージェントをインストールする

### <a name="windows-agent"></a>Windows エージェント

1. サーバーにログインします。

1. 管理者特権の 64 ビット PowerShell コマンド プロンプトを開きます。

1. スクリプトをコピーしたフォルダーまたは共有に移動し、`./OnboardingScript.ps1` スクリプトを実行することでそのスクリプトをサーバー上で実行します。

### <a name="linux-agent"></a>Linux エージェント

1. Azure と直接通信できる Linux エージェントをターゲット マシンにインストールするために、次のコマンドを実行します。

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * ターゲット マシンがプロキシ サーバー経由で通信を行う場合は、次のコマンドを実行します。

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc との接続を検証する

エージェントをインストールし、それを Azure Arc 対応サーバーに接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されたことを確認します。 対象のマシンが [Azure portal](https://aka.ms/hybridmachineportal) に表示されます。

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="マシンの接続に成功" border="false":::

## <a name="next-steps"></a>次の手順

Linux または Windows のハイブリッド マシンを有効にし、サービスへの接続に成功しました。いつでも Azure Policy を有効にして Azure でのコンプライアンスを把握することができます。

Azure Arc 対応サーバーに対応したマシンの中から、Log Analytics エージェントがインストールされていないマシンを特定する方法については、次のチュートリアルにお進みください。

> [!div class="nextstepaction"]
> [準拠していないリソースを識別するためのポリシー割り当てを作成する](tutorial-assign-policy-portal.md)
