---
title: Linux コンピューターを Azure Monitor に接続する | Microsoft Docs
description: この記事では、Linux 用 Log Analytics エージェントを使用して、他のクラウドやオンプレミス内にホストされている Linux コンピューターを Azure Monitor に接続する方法について説明します。
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 965d5dd558d0da7a758db77330c9129ea0e8247c
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543862"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Linux コンピューターを Azure Monitor に接続する

Azure Monitor を使用して、ローカル データ センターやその他のクラウド環境内にある仮想マシンや物理コンピューターを監視および管理するには、Log Analytics エージェントをデプロイし、Log Analytics ワークスペースにレポートを送信するように構成する必要があります。 エージェントでは、Azure Automation 用の Hybrid Runbook Worker ロールもサポートされます。

Linux 用の Log Analytics エージェントは、次のいずれかの方法を使用してインストールできます。 各メソッドの使い方の詳細については、記事の後半で説明します。

* エージェントを[手動でダウンロードしてインストール](#install-the-agent-manually)します。 この方法は、Linux コンピューターがインターネットにアクセスできず、[Log Analytics ゲートウェイ](gateway.md)経由で Azure Monitor または Azure Automation と通信することになる場合に必要です。 
* GitHub でホストされている[ラッパー スクリプトを使用して Linux 用エージェントをインストール](#install-the-agent-using-wrapper-script)します。 これは、コンピューターがインターネットに直接またはプロキシサーバー経由で接続している場合に、エージェントをインストールおよびアップグレードするための推奨される方法です。

サポートされている構成を確認するには、[サポートされている Linux オペレーティング システム](log-analytics-agent.md#supported-linux-operating-systems)と[ネットワーク ファイアウォールの構成](log-analytics-agent.md#network-requirements)に関するページをご覧ください。

>[!NOTE]
>Linux 用 Log Analytics エージェントは、複数の Log Analytics ワークスペースにレポートするように構成することはできません。 System Center Operations Manager 管理グループと Log Analytics ワークスペースの両方に同時にレポートするように構成することも、個別にレポートするように構成することもできます。

## <a name="agent-install-package"></a>エージェントのインストール パッケージ

Linux 用 Log Analytics エージェントは、複数のパッケージで構成されています。 リリース ファイルには、次のパッケージが含まれており、シェル バンドルを `--extract` パラメーターで実行することによって抽出できます。

**Package** | **Version** | **説明**
----------- | ----------- | --------------
omsagent | 1.12.15 | Linux 用 Log Analytics エージェント
omsconfig | 1.1.1 | Log Analytics エージェントの構成エージェント
omi | 1.6.3 | Open Management Infrastructure (OMI) -- 軽量の CIM サーバー。 *OMI では、サービスの機能に必要な cron ジョブを実行するためにルート アクセスが必要となることに注意してください。*
scx | 1.6.3 | オペレーティング システムのパフォーマンス メトリックの OMI CIM プロバイダー
apache-cimprov | 1.0.1 | OMI の Apache HTTP Server パフォーマンス監視プロバイダー。 Apache HTTP Server が検出された場合にのみインストールされます。
mysql-cimprov | 1.0.1 | OMI の MySQL Server パフォーマンス監視プロバイダー。 MySQL/MariaDB サーバーが検出された場合にのみインストールされます。
docker-cimprov | 1.0.0 | OMI の Docker プロバイダー。 Docker が検出された場合にのみインストールされます。

### <a name="agent-installation-details"></a>エージェントのインストールの詳細

Linux 用 Log Analytics エージェントのパッケージをインストールした後、システム全体に関連した構成の変更が別途適用されます。 omsagent パッケージをアンインストールすると、これらのアーティファクトは削除されます。

* `omsagent` という名前の非特権ユーザーが作成されます。 この資格情報でデーモンが実行されます。 
* sudoers *インクルード* ファイルが `/etc/sudoers.d/omsagent` に作成されます。 このファイルで、syslog および omsagent デーモンを再起動する権限が `omsagent` に与えられます。 sudo *インクルード* ディレクティブが、インストールされているバージョンの sudo でサポートされていない場合、それらのエントリは `/etc/sudoers` に書き込まれます。
* イベントの一部をエージェントに転送するよう syslog の構成が変更されます。 詳細については、[Syslog データ コレクションの構成](data-sources-syslog.md)に関する記事を参照してください。

監視対象の Linux コンピューターでは、エージェントは `omsagent` として表示されます。 `omsconfig` は Linux 用 Log Analytics エージェントの構成エージェントであり、5 分ごとに新しいポータル側構成を検索します。 この新しい更新された構成が、`/etc/opt/microsoft/omsagent/conf/omsagent.conf` にあるエージェント構成ファイルに適用されます。

## <a name="obtain-workspace-id-and-key"></a>ワークスペース ID とキーを取得する

Linux 用 Log Analytics エージェントをインストールする前に、Log Analytics ワークスペースのワークスペース ID とキーが必要です。 この情報は、エージェントのセットアップ中にエージェントを適切に構成し、Azure Monitor と正常に通信できるようにするために必要です。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Azure portal の左上隅にある **[すべてのサービス]** を選択します。 検索ボックスに「**Log Analytics**」と入力します。 入力すると、入力内容に基づいて一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。

2. Log Analytics ワークスペースの一覧で、前の手順で作成したワークスペースを選択します。 (**DefaultLAWorkspace** という名前を付けた可能性があります。)

3. **[詳細設定]** を選択します。

    ![Azure portal の Log Analytics ワークスペースの [詳細設定] メニュー](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. **[接続されたソース]** 、 **[Linux サーバー]** の順に選択します。

5. **[ワークスペース ID]** と **[主キー]** の右側に値が表示されます。 両方をコピーしてお使いのエディターに貼り付けます。

## <a name="install-the-agent-manually"></a>エージェントを手動でインストールする

Linux 用 Log Analytics エージェントは、自己解凍型のインストール可能なシェル スクリプト バンドルで提供されます。 このバンドルにはエージェント コンポーネントのそれぞれの Debian および RPM パッケージが含まれており、直接インストールすることも、抽出して個々のパッケージを取得することもできます。 x64 アーキテクチャ用と x86 アーキテクチャ用のバンドルがそれぞれ 1 つ提供されます。 

> [!NOTE]
> Azure VM の場合は、Linux 用の [Azure Log Analytics VM 拡張機能](../../virtual-machines/extensions/oms-linux.md)を使用してエージェントをインストールすることをお勧めします。 

1. scp/sftp を使用して、該当するバンドル (x64 または x86) を Linux VM または物理コンピューターに[ダウンロード](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide)して転送します。

2. `--install` 引数を使用してバンドルをインストールします。 インストール中に Log Analytics ワークスペースにオンボードするには、前にコピーした `-w <WorkspaceID>` と `-s <workspaceKey>` のパラメーターを指定します。

    >[!NOTE]
    >Linux 用 System Center Operations Manager エージェントが既にインストールされている場合と同様に、omi、scx、omsconfig などの依存パッケージがインストールされる場合は、`--upgrade` 引数を使用する必要があります。 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Log Analytics ゲートウェイ経由で Log Analytics ワークスペースをインストールして接続するように Linux エージェントを構成するには、プロキシ、ワークスペース ID、およびワークスペースキーの各パラメーターを指定して次のコマンドを実行します。 この構成は、コマンド ラインで `-p [protocol://][user:password@]proxyhost[:port]`を含めることによって指定できます。 *proxyhost* プロパティは、Log Analytics ゲートウェイ サーバーの完全修飾ドメイン名または IP アドレスを受け取ります。  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    認証が必要な場合は、ユーザー名とパスワードを指定する必要があります。 次に例を示します。 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Azure Government クラウドの Log Analytics ワークスペースに接続できるように Linux コンピューターを構成するには、前にコピーしたワークスペース ID と主キーを指定して次のコマンドを実行します。

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

エージェント パッケージをインストールし、後で特定の Log Analytics ワークスペースにレポートするように構成する場合は、次のコマンドを実行します。

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

エージェント パッケージをインストールせずに、バンドルからパッケージを抽出する場合は、次のコマンドを実行します。

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>ラッパー スクリプトを使用してエージェントをインストールする

次の手順では、直接またはプロキシサーバー経由で通信して、GitHub でホストされているエージェントをダウンロードしてインストールする Linux コンピューター用のラッパー スクリプトを使用して、Azure と Azure Government クラウドで Log Analytics 用エージェントのセットアップを構成します。  

Linux コンピューターと Log Analytics との通信をプロキシ サーバーを介して行う必要がある場合、コマンド ラインから「`-p [protocol://][user:password@]proxyhost[:port]`」を入力することでこの構成を指定できます。 *protocol* プロパティは `http` または `https` を受け取り、*proxyhost* プロパティはプロキシ サーバーの完全修飾ドメイン名または IP アドレスを受け取ります。 

例: `https://proxy01.contoso.com:30443`

いずれも認証が必要な場合は、ユーザー名とパスワードを指定する必要があります。 例: `https://user01:password@proxy01.contoso.com:30443`

1. Log Analytics ワークスペースに接続できるように Linux コンピューターを構成するには、ワークスペース ID と主キーを指定して次のコマンドを実行します。 次のコマンドは、エージェントをダウンロードし、そのチェックサムを検証してインストールします。
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    次のコマンドには、`-p` プロキシ パラメーターと、お使いのプロキシ サーバーで認証が必要な場合の構文例が含まれています。

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Azure Government クラウドの Log Analytics ワークスペースに接続できるように Linux コンピューターを構成するには、前にコピーしたワークスペース ID と主キーを指定した次のコマンドを実行します。 次のコマンドは、エージェントをダウンロードし、そのチェックサムを検証してインストールします。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    次のコマンドには、`-p` プロキシ パラメーターと、お使いのプロキシ サーバーで認証が必要な場合の構文例が含まれています。

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 次のコマンドを実行してエージェントを再起動します。 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="supported-linux-hardening"></a>Linux のセキュリティ強化のサポート
OMS エージェントでは、Linux のカスタマイズが制限されています。 

現在、以下がサポートされています。 
- FIPS

以下は計画されていますが、まだサポートされていません。
- CIS
- SELINUX

その他のセキュリティ強化とカスタマイズの手段は、OMS エージェントではサポートされておらず、その計画もありません。  


## <a name="upgrade-from-a-previous-release"></a>以前のリリースからのアップグレード

バージョン 1.0.0-47 以降の以前のバージョンからのアップグレードは、各リリースでサポートされています。 `--upgrade` パラメーターを使用してインストールを実行すると、エージェントのすべてのコンポーネントを最新バージョンにアップグレードできます。

## <a name="next-steps"></a>次のステップ

- 仮想マシンからのエージェントの再構成、アップグレード、または削除方法については、「[Windows および Linux での Log Analytics エージェントの管理とメンテナンス](agent-manage.md)」をご覧ください。

- エージェントのインストールまたは管理中に問題が発生した場合は、[Linux エージェントのトラブルシューティング](agent-linux-troubleshoot.md)に関する記事を参照してください。
