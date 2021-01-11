---
title: Azure Arc 対応サーバー (プレビュー) にハイブリッド マシンを接続する
description: Azure Arc 対応サーバー (プレビュー) にハイブリッド マシンを接続して登録する方法について説明します。
ms.topic: quickstart
ms.date: 08/12/2020
ms.openlocfilehash: eacf75871b1f7cc7fc3b703d8859338578e43456
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213614"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers-preview"></a>クイック スタート:Azure Arc 対応サーバー (プレビュー) にハイブリッド マシンを接続する

[Azure Arc 対応サーバー](../overview.md) (プレビュー) を使用すると、オンプレミス、エッジ、マルチクラウドの垣根を越え、それらの環境でホストされている Windows マシンや Linux マシンを管理することができます。 このクイックスタートでは、Azure の外部でホストされた Windows マシンまたは Linux マシンを Arc 対応サーバー (プレビュー) で管理できるよう、それらのマシンに Connected Machine エージェントをデプロイして構成します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* Arc 対応サーバー (プレビュー) の Hybrid Connected Machine エージェントをデプロイするには、マシンにエージェントをインストールして構成するための管理者アクセス許可が必要です。 Linux では root アカウントを使用し、Windows ではローカルの Administrators グループのメンバー アカウントを使用します。

* 開始する前に、必ずエージェントの[前提条件](../agent-overview.md#prerequisites)を確認し、次の点を確認してください。

    * サポートされている[オペレーティング システム](../agent-overview.md#supported-operating-systems)がターゲット マシンで実行されていること。

    * [必要な Azure ロール](../agent-overview.md#required-permissions)への割り当てが、ご使用のアカウントに許可されていること。

    * インターネット経由で通信するためにマシンがファイアウォールやプロキシ サーバーを介して接続する場合、[一覧](../agent-overview.md#networking-configuration)に記載されている URL がブロックされていないこと。

    * Azure Arc 対応サーバー (プレビュー) でサポートされるのは、[こちら](../overview.md#supported-regions)に記載されたリージョンのみです。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Azure リソースプロバイダーを登録する

Azure Arc 対応サーバー (プレビュー) は、このサービスを使用するために、サブスクリプション内の次の Azure リソースプロバイダーに依存します。

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

次のコマンドを使用してこれらを登録します。

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>インストール スクリプトを生成する

ダウンロードとインストールを自動化し、Azure Arc との接続を確立するスクリプトは、Azure portal から入手できます。 そのプロセスを完了するには、次の手順を実行します。

1. Azure portal で Azure Arc サービスを起動します。 **[すべてのサービス]** をクリックし、 **[マシン - Azure Arc]** を探して選択してください。

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="[すべてのサービス] で Arc 対応サーバーを検索する" border="false":::

1. **[マシン - Azure Arc]** ページで、左上にある **[追加]** か、中央のペインの下部にある **[Create machine - Azure Arc]\(マシンの作成 - Azure Arc\)** オプションを選択します。

1. **[メソッドの選択]** ページで、 **[Add machines using interactive script]\(対話型スクリプト タイルを使用してマシンを追加\)** タイルを選択し、 **[スクリプトの生成]** を選択します。

1. **[スクリプトの生成]** ページで、Azure 内でマシンを管理するサブスクリプションとリソース グループを選択します。 マシンのメタデータが格納される Azure の場所を選択します。

1. **[スクリプトの生成]** ページの **[オペレーティング システム]** ボックスの一覧で、スクリプトを実行するオペレーティング システムを選択します。

1. マシンがインターネットに接続するためにプロキシ サーバー経由で通信している場合は、 **[Next: Proxy Server]\(次へ: プロキシ サーバー\)** を選択します。

1. **[プロキシ サーバー]** タブで、プロキシ サーバーの IP アドレスか、マシンがプロキシ サーバーとの通信に使用する名前およびポート番号を指定します。 `http://<proxyURL>:<proxyport>` の形式で値を入力します。

1. **[確認と生成]** を選択します。

1. **[確認と生成]** タブで概要情報を確認し、 **[ダウンロード]** を選択します。 引き続き変更が必要な場合は、 **[前へ]** を選択します。

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

エージェントをインストールし、Azure Arc 対応サーバー (プレビュー) に接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されていることを確認します。 対象のマシンが [Azure portal](https://aka.ms/hybridmachineportal) に表示されます。

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="マシンの接続に成功" border="false":::

## <a name="next-steps"></a>次の手順

Linux または Windows のハイブリッド マシンを有効にし、サービスへの接続に成功しました。いつでも Azure Policy を有効にして Azure でのコンプライアンスを把握することができます。

Azure Arc 対応サーバー (プレビュー) に対応したマシンの中から、Log Analytics エージェントがインストールされていないマシンを特定する方法については、次のチュートリアルにお進みください。

> [!div class="nextstepaction"]
> [準拠していないリソースを識別するためのポリシー割り当てを作成する](tutorial-assign-policy-portal.md)
