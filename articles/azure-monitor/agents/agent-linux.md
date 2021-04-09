---
title: Linux コンピューターに Log Analytics エージェントをインストールする
description: この記事では、Linux 用 Log Analytics エージェントを使用して、他のクラウドやオンプレミス内にホストされている Linux コンピューターを Azure Monitor に接続する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: dae9ab56ca1349d288aee02f7e2dfa4eccf1508c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199159"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Linux コンピューターに Log Analytics エージェントをインストールする
この記事では、次の方法を使用して Linux コンピューターに Log Analytics エージェントをインストールする方法の詳細を説明します。

* GitHub でホストされている[ラッパー スクリプトを使用して Linux 用エージェントをインストール](#install-the-agent-using-wrapper-script)します。 これは、コンピューターがインターネットに直接またはプロキシサーバー経由で接続している場合に、エージェントをインストールおよびアップグレードするための推奨される方法です。
* エージェントを[手動でダウンロードしてインストール](#install-the-agent-manually)します。 この方法は、Linux コンピューターがインターネットにアクセスできず、[Log Analytics ゲートウェイ](./gateway.md)経由で Azure Monitor または Azure Automation と通信することになる場合に必要です。 

>[!IMPORTANT]
> この記事で説明するインストール方法は、通常、オンプレミスまたは他のクラウド内の仮想マシンで使用されます。 Azure 仮想マシンに使用できるより効率的なオプションについては、「[インストール オプション](./log-analytics-agent.md#installation-options)」を参照してください。



## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Log Analytics エージェントでサポートされている Linux ディストリビューションの一覧については、「[Azure Monitor エージェントの概要](agents-overview.md#supported-operating-systems)」を参照してください。

>[!NOTE]
>OpenSSL 1.1.0 は x86_x64 プラットフォーム (64-bit) 上のみでサポートされ、1.x より前の OpenSSL は、どのプラットフォーム上でもサポートされません。

>[!NOTE]
>コンテナーでの Log Analytics Linux エージェントの実行はサポートされていません。 コンテナーを監視する必要がある場合は、Docker ホストでは[コンテナー監視ソリューション](../containers/containers.md)、Kubernetes では [Container Insights](../containers/container-insights-overview.md) を利用してください。

2018 年 8 月以降にリリースされたバージョンからは、サポート モデルに次の変更を加えています。  

* クライアントではなく、サーバー バージョンのみがサポートされます。  
* [Azure Linux 動作保証済みディストリビューション](../../virtual-machines/linux/endorsed-distros.md)のサポートに重点が置かれています。 新しいディストリビューションやバージョンが Azure Linux 動作保証済みになってから、Log Analytics Linux エージェントでサポートされるまでの間に、遅延が発生する場合があることに注意してください。
* 記載されている各メジャー バージョンのマイナー リリースは、すべてサポートされます。
* 製造元のサポート終了日を超過したバージョンは、サポートされません。
* VM イメージのみをサポートします。公式のディストリビューション発行元のイメージから派生したコンテナーでもサポートされません。
* AMI の新しいバージョンはサポートされません。  
* 既定で OpenSSL 1.x を実行するバージョンのみがサポートされます。

>[!NOTE]
>現在サポートされていないディストリビューションまたはバージョンを使用しており、サポート モデルに準拠していない場合、Microsoft サポートは、支援機能にフォークされたエージェント バージョンを提供していることを認識したうえで、このレポジトリをフォークすることをお勧めします。

### <a name="python-requirement"></a>Python の要件

エージェント バージョン 1.13.27 以降では、Linux エージェントで Python 2 と 3 の両方がサポートされます。 常に最新のエージェントを使用することをお勧めします。 

以前のバージョンのエージェントを使用している場合、既定では仮想マシンで python 2 が使用されている必要があります。 既定で Python 2 が含まれないディストリビューションが仮想マシンで使用されている場合、それをインストールする必要があります。 次のサンプル コマンドでは、異なるディストリビューションに Python 2 がインストールされます。

 - Red Hat、CentOS、Oracle: `yum install -y python2`
 - Ubuntu、Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

python2 実行可能ファイルに *python* という別名を付ける必要があります。 この別名を設定するために使用できる 1 つの方法を次に示します。

1. 次のコマンドを実行して、既存の別名を削除します。
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 次のコマンドを実行して、別名を作成します。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Linux のセキュリティ強化のサポート
OMS エージェントでは、Linux のカスタマイズが制限されています。 

現在、以下がサポートされています。 
- FIPS

以下は考慮されていますが、まだサポートされていません。
- CIS
- SELINUX

その他のセキュリティ強化とカスタマイズの手段は、OMS エージェントではサポートされておらず、その計画もありません。  

## <a name="agent-prerequisites"></a>エージェントの前提条件

次の表には、エージェントがインストールされる[サポートされている Linux ディストリビューション](#supported-operating-systems)に必要なパッケージが明記されています。

|必須パッケージ |説明 |最小バージョン |
|-----------------|------------|----------------|
|Glibc |    GNU C ライブラリ | 2.5-12 
|Openssl    | OpenSSL ライブラリ | 1.0.x または 1.1.x |
|Curl | cURL Web クライアント | 7.15.5 |
|Python | | 2.6 以降または 3.3 以降
|Python-ctypes | | 
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール) | | 

>[!NOTE]
>syslog メッセージを収集するには、rsyslog または syslog-ng が必要となります。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。

## <a name="network-requirements"></a>ネットワークの要件
Linux エージェントのネットワーク要件については、「[Log Analytics エージェントの概要](./log-analytics-agent.md#network-requirements)」を参照してください。

## <a name="agent-install-package"></a>エージェントのインストール パッケージ

Linux 用 Log Analytics エージェントは、複数のパッケージで構成されています。 リリース ファイルには、次のパッケージが含まれており、シェル バンドルを `--extract` パラメーターで実行することによって抽出できます。

**Package** | **Version** | **説明**
----------- | ----------- | --------------
omsagent | 1.13.9 | Linux 用 Log Analytics エージェント
omsconfig | 1.1.1 | Log Analytics エージェントの構成エージェント
omi | 1.6.4 | Open Management Infrastructure (OMI) -- 軽量の CIM サーバー。 *OMI では、サービスの機能に必要な cron ジョブを実行するためにルート アクセスが必要となることに注意してください。*
scx | 1.6.4 | オペレーティング システムのパフォーマンス メトリックの OMI CIM プロバイダー
apache-cimprov | 1.0.1 | OMI の Apache HTTP Server パフォーマンス監視プロバイダー。 Apache HTTP Server が検出された場合にのみインストールされます。
mysql-cimprov | 1.0.1 | OMI の MySQL Server パフォーマンス監視プロバイダー。 MySQL/MariaDB サーバーが検出された場合にのみインストールされます。
docker-cimprov | 1.0.0 | OMI の Docker プロバイダー。 Docker が検出された場合にのみインストールされます。

### <a name="agent-installation-details"></a>エージェントのインストールの詳細

Linux 用 Log Analytics エージェントのパッケージをインストールした後、システム全体に関連した構成の変更が別途適用されます。 omsagent パッケージをアンインストールすると、これらのアーティファクトは削除されます。

* `omsagent` という名前の非特権ユーザーが作成されます。 この資格情報でデーモンが実行されます。 
* sudoers *インクルード* ファイルが `/etc/sudoers.d/omsagent` に作成されます。 このファイルで、syslog および omsagent デーモンを再起動する権限が `omsagent` に与えられます。 sudo *インクルード* ディレクティブが、インストールされているバージョンの sudo でサポートされていない場合、それらのエントリは `/etc/sudoers` に書き込まれます。
* イベントの一部をエージェントに転送するよう syslog の構成が変更されます。 詳細については、[Syslog データ コレクションの構成](data-sources-syslog.md)に関する記事を参照してください。

監視対象の Linux コンピューターでは、エージェントは `omsagent` として表示されます。 `omsconfig` は Linux 用 Log Analytics エージェントの構成エージェントであり、5 分ごとに新しいポータル側構成を検索します。 この新しい更新された構成が、`/etc/opt/microsoft/omsagent/conf/omsagent.conf` にあるエージェント構成ファイルに適用されます。

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

## <a name="upgrade-from-a-previous-release"></a>以前のリリースからのアップグレード

バージョン 1.0.0-47 以降の以前のバージョンからのアップグレードは、各リリースでサポートされています。 `--upgrade` パラメーターを使用してインストールを実行すると、エージェントのすべてのコンポーネントを最新バージョンにアップグレードできます。

## <a name="cache-information"></a>キャッシュ情報
Linux 用 Log Analytics エージェントからのデータは、ローカル マシン上の *%STATE_DIR_WS%/out_oms_common*.buffer* にキャッシュされてから Azure Monitor に送信されます。 カスタム ログ データは *%STATE_DIR_WS%/out_oms_blob*.buffer* にバッファリングされます。 このパスは、一部の[ソリューションとデータ型](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)では異なる場合があります。

エージェントによって 20 秒ごとにアップロードが試行されます。 失敗した場合は、成功するまで、待機する時間が指数関数的に増加します。2 回目の試行の前に 30 秒前、3 回目の前に 60 秒前、さらにその次の前に 120 秒間といった具合に、再接続が成功するまで最大 16 分間待機します。 エージェントでは、特定のデータ チャンクに対して最大 6 回再試行されてから破棄され、次に進みます。 これは、エージェントが再度正常にアップロードできるようになるまで継続されます。 これは、データが破棄されるまでに最大約 30 分間バッファーされる可能性があることを意味します。

既定のキャッシュ サイズは 10 MB ですが、[omsagent.conf ファイル](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)内で変更することができます。


## <a name="next-steps"></a>次のステップ

- 仮想マシンからのエージェントの再構成、アップグレード、または削除方法については、「[Windows および Linux での Log Analytics エージェントの管理とメンテナンス](agent-manage.md)」をご覧ください。

- エージェントのインストールまたは管理中に問題が発生した場合は、[Linux エージェントのトラブルシューティング](agent-linux-troubleshoot.md)に関する記事を参照してください。
