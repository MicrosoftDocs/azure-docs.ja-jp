---
title: Azure HDInsight クラスターをカスタマイズするスクリプト 操作を開発します。
description: Bash スクリプトを使用して HDInsight クラスターをカスタマイズする方法について説明します。 スクリプト 操作により、クラスター作成の最中または後にクラスターの構成設定を変更または追加のソフトウェアをインストールすることができます。
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: b6705728fddc9a5a3c9cb8eb2f1811412fb3a290
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945479"
---
# <a name="script-action-development-with-hdinsight"></a>HDInsight でのスクリプト アクション開発

Bash スクリプトを使用して、HDInsight クラスターをカスタマイズする方法について説明します。 スクリプト アクションは、クラスターの作成時または作成後に、HDInsight をカスタマイズする方法です。

## <a name="what-are-script-actions"></a>スクリプト アクションとは

Script Action は、Azure がクラスター ノードで実行して、構成の変更やソフトウェアのインストールを行う Bash スクリプトです。 Script Action はルートとして実行され、完全なアクセス権をクラスター ノードに提供します。

スクリプト アクションは次の方法によって適用できます。

| この方法を使用してスクリプトを適用する... | クラスター作成時... | 実行中のクラスターで... |
| --- |:---:|:---:|
| Azure portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure クラシック CLI |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager テンプレート |✓ |&nbsp; |

これらの方法を使用したスクリプト アクションの適用の詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>スクリプトの開発におけるベスト プラクティス

HDInsight クラスター向けのカスタム スクリプトを開発する際、留意すべきベスト プラクティスがあります。

* [Apache Hadoop のバージョンを対象にする](#bPS1)
* [ターゲット OS バージョン](#bps10)
* [スクリプト リソースへの安定したリンクの提供](#bPS2)
* [プリコンパイル済みリソースの使用](#bPS4)
* [クラスターのカスタマイズ スクリプトはべき等にする](#bPS3)
* [クラスターのアーキテクチャの高可用性を確保](#bPS5)
* [Azure BLOB ストレージを使用するカスタム コンポーネントの構成](#bPS6)
* [STDOUT および STDERR に情報を書き込む](#bPS7)
* [LF 行の終わりで、ファイルを ASCII として保存する](#bps8)
* [再試行ロジックを使用して一時的なエラーから回復する](#bps9)

> [!IMPORTANT]  
> スクリプト アクションは 60 分以内に完了する必要があります。そうでないと、プロセスは失敗します。 ノードのプロビジョニング中、スクリプトは他のセットアップ プロセスや構成プロセスと同時に実行されます。 CPU 時間やネットワーク帯域幅などのリソースの競合が原因で、開発環境の場合よりスクリプトの完了に時間がかかる可能性があります。

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Apache Hadoop のバージョンを対象にする

HDInsight のバージョンが異なれば、異なるバージョンの Hadoop サービスとコンポーネントがインストールされます。 スクリプトに特定のバージョンのサービスまたはコンポーネントが期待される場合、必要なコンポーネントを含むバージョンの HDInsight スクリプトのみを使用する必要があります。 HDInsight に含まれているコンポーネントのバージョンの情報は、 [HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md) に関するドキュメントで確認できます。

### <a name="checking-the-operating-system-version"></a>オペレーティング システム バージョンの確認

異なるバージョンの HDInsight は、特定のバージョンの Ubuntu に依存します。 スクリプトで確認する必要がある OS バージョンによって違いがある可能性があります。 たとえば、Ubuntu のバージョンに関連付けられているバイナリをインストールする必要がある可能性があります。

オペレーティング システムのバージョンを確認するには、`lsb_release` を使用します。 たとえば、以下のスクリプトは、OS のバージョンによって異なる特定の tar ファイルを参照する方法を示します。

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a>オペレーティング システム バージョンをターゲットにします。

HDInsight は、Ubuntu Linux ディストリビューションに基づいています。 異なるバージョンの HDInsight は、異なるバージョンの Ubuntu に依存し、スクリプトの動作が変わる可能性があります。 たとえば、HDInsight 3.4 以前は、Upstart を使用する Ubuntu バージョンに基づいています。 バージョン 3.5 以降は、Systemd を使用する Ubuntu 16.04 に基づいています。 Systemd と Upstart は、異なるコマンドに依存しているため、両方で動作するスクリプトを記述する必要があります。

HDInsight 3.4 と 3.5 のもう 1 つの重要な違いは `JAVA_HOME` が Java 8 を指すようになった点です。 次のコードでは、スクリプトが Ubuntu 14 または 16 で実行されているかどうかを判断する方法を示しています。

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

これらのスニペットが含まれる完全スクリプトは https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh にあります。

HDInsight で使用される Ubuntu のバージョンについては、「[HDInsight コンポーネントのバージョン](hdinsight-component-versioning.md)」のドキュメントをご覧ください。

Systemd と Upstart の違いを理解するには、「[Systemd for Upstart users (Upstart ユーザー向けの Systemd)](https://wiki.ubuntu.com/SystemdForUpstartUsers)」をご覧ください。

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>スクリプト リソースへの安定したリンクの提供

スクリプトとそれに関連付けられているリソースは、クラスターの有効期間を通じて使用可能である必要があります。 これらのリソースは、スケーリング処理中に、新しいノードをクラスターに追加する場合に必要です。

ベスト プラクティスとして、すべてをダウンロードして、Azure Storage アカウントのサブスクリプションにアーカイブする方法があります。

> [!IMPORTANT]  
> 使用されるストレージ アカウントは、クラスターの既定のストレージ アカウントかその他のストレージ アカウントにおける読み取り専用のパブリック コンテナーのいずれかにする必要があります。

たとえば、Microsoft から提供されるサンプルは、[https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) ストレージ アカウントに格納されています。 この場所は、HDInsight チームによって管理されている、読み取り専用のパブリック コンテナーです。

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>プリコンパイル済みリソースの使用

スクリプトの実行にかかる時間を短縮するために、ソース コードからリソースをコンパイルする操作を行わないようにしてください。 たとえば、リソースを事前にコンパイルし、HDInsight と同じデータ センターにある Azure Storage アカウント BLOB に格納します。

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>クラスターのカスタマイズ スクリプトはべき等にする

スクリプトはべき等にする必要があります。 スクリプトを複数回実行する場合、クラスターは毎回同じ状態に戻ります。

たとえば、複数回実行する場合は、構成ファイルを変更するスクリプトは重複したエントリに追加しないでください。

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>クラスターのアーキテクチャの高可用性を確保

Linux ベースの HDInsight クラスターは、クラスター内でアクティブな 2 つのヘッド ノードを提供し、スクリプト アクションがその両方のノードで実行されます。 インストールするコンポーネントにヘッド ノードを 1 つのみ予定する場合は、両方のヘッド ノードにコンポーネントをインストールしないでください。

> [!IMPORTANT]  
> HDInsight の一部として提供されるサービスは、必要に応じて 2 つのヘッド ノードの間でフェールオーバーするように設計されています。 この機能は、スクリプト アクションを使用してインストールするカスタム コンポーネントには拡張されません。 カスタム コンポーネントに高可用性が必要な場合は、独自のフェールオーバー メカニズムを実装する必要があります。

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Azure BLOB ストレージを使用するカスタム コンポーネントの構成

クラスターにインストールするコンポーネントに、Apache Hadoop 分散ファイル システム (HDFS) ストレージを使用する既定の構成が含まれる可能性があります。 HDInsight は、既定のストレージとして Azure Storage または Data Lake Storage のいずれかを使用します。 両方ともデータを保持する HDFS 互換ファイル システムを提供します (、クラスターが削除された場合でも)。 HDFS の代わりに WASB または ADL を使用するように、インストールするコンポーネントを構成しなければならない場合があります。

ほとんどの操作では、ファイル システムを指定する必要はありません。 たとえば、次の場合 hadoop-common.jar ファイルがローカル ファイル システムからクラスター ストレージにコピーされます。

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

この例では、`hdfs` コマンドは、既定のクラスター ストレージを透過的に使用します。 一部の操作では、URI を指定しなければならない場合があります。 たとえば、Azure Data Lake Storage Gen1 の場合は `adl:///example/jars`、Data Lake Storage Gen2 の場合は`abfs:///example/jars`、Azure Storage の場合は `wasb:///example/jars` です。

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>STDOUT および STDERR に情報を書き込む

HDInsight のログは、STDOUT と STDERR に書き込まれた出力を記述します。 Ambari Web UI を使用して、この情報を表示できます。

> [!NOTE]  
> Apache Ambari は、クラスターが正常に作成された場合にのみ使用できます。 クラスターの作成時にスクリプト アクションを使用して作成に失敗した場合は、[スクリプト アクションのトラブルシューティング](./troubleshoot-script-action.md)に関するページで、ログに記録された情報にアクセスする他の方法を確認してください。

ほとんどのユーティリティとインストール パッケージは STDOUT および STDERR に情報を書き込みますが、ログ記録を追加したい場合もあります。 STDOUT にテキストを送信するには、`echo` を使用します。 次に例を示します。

```bash
echo "Getting ready to install Foo"
```

既定では、`echo` は STDOUT に文字列を送信します。 STDERR に転送するには、`echo` の前に `>&2` を追加します。 次に例を示します。

```bash
>&2 echo "An error occurred installing Foo"
```

これは、代わりに、STDOUT に書き込まれた情報を　STDERR (2) にリダイレクトします。 IO リダイレクトの詳細については、「[https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html)」を参照してください。

スクリプト アクションによってログに記録される情報の表示の詳細については、[スクリプト アクションのトラブルシューティング](./troubleshoot-script-action.md)に関するページを参照してください。

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a> LF 行の終わりで、ファイルを ASCII として保存する

Bash スクリプトは、行が LF で終了する ASCII 形式で保存する必要があります。 UTF-8 として保存するか、または行の終わりに CRLF を使用するファイルは、次のエラーで失敗する可能性があります。

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a> 再試行ロジックを使用して一時的なエラーから回復する

ファイルのダウンロード時、apt-get を使用したパッケージのインストール時、またはインターネット経由でデータを転送するその他の操作時に、一時的なネットワーク エラーにより、操作に失敗する場合があります。 たとえば、通信対象のリモート リソースが、バックアップ ノードへのフェールオーバー中である可能性があります。

一時的なエラーに対するスクリプトの回復力を高めるには、再試行ロジックを実装します。 次の関数は、再試行ロジックを実装する方法を示します。 失敗する前に、操作を3 回再試行します。

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

次の例では、この関数を使用する方法を示します。

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>カスタム スクリプトのためのヘルパー メソッド

スクリプト アクションのヘルパー メソッドは、カスタム スクリプトの記述で利用できるユーティリティです。 これらのメソッドは [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) スクリプトに含まれています。 次を使用してダウンロードし、これらをスクリプトの一部として使用します。

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

次のヘルパーがスクリプトで使用できます。

| ヘルパーの使用 | 説明 |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |元の URI から指定されたファイルのパスに、ファイルをダウンロードします。 既定では、既存のファイルは上書きされません。 |
| `untar_file TARFILE DESTDIR` |tar ファイルを (`-xf`を使用して) インストール先ディレクトリに抽出します。 |
| `test_is_headnode` |スクリプトがクラスターのヘッド ノードで実行された場合は 1 が返され、それ以外の場合は 0 が返されます。 |
| `test_is_datanode` |現在のノードがデータ (ワーカー) ノードの場合は 1 を返し、それ以外の場合は 0 を返します。 |
| `test_is_first_datanode` |現在のノードが最初のデータ (ワーカー) ノード (workernode0) の場合は 1 を返し、それ以外の場合は 0 を返します。 |
| `get_headnodes` |クラスターのヘッドノードの完全修飾ドメイン名が返されます。 名前はコンマで区切られます。 エラーの場合は、空の文字列が返されます。 |
| `get_primary_headnode` |プライマリ ヘッドノードの完全修飾ドメイン名を取得します。 エラーの場合は、空の文字列が返されます。 |
| `get_secondary_headnode` |セカンダリ ヘッドノードの完全修飾ドメイン名を取得します。 エラーの場合は、空の文字列が返されます。 |
| `get_primary_headnode_number` |プライマリ ヘッドノードの数値のサフィックスを取得します。 エラーの場合は、空の文字列が返されます。 |
| `get_secondary_headnode_number` |セカンダリ ヘッドノードの数値のサフィックスを取得します。 エラーの場合は、空の文字列が返されます。 |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>一般的な使用パターン

このセクションでは、独自のカスタム スクリプトの書き込み中に発生する可能性がある一般的な使用状況パターンの実装についてのガイダンスを提供します。

### <a name="passing-parameters-to-a-script"></a>スクリプトにパラメーターを渡す

場合によっては、スクリプトにパラメーターが必要な場合があります。 たとえば、Ambari の REST API の使用時に、クラスターの管理者パスワードが必要になる場合があります。

スクリプトに渡されるパラメーターは *位置指定パラメーター* と呼ばれ、最初のパラメーターでは `$1` に、2 番目のパラメーターでは `$2` に割り当てられ、以下同様です。 `$0` にはスクリプト自体の名前が含まれています。

パラメーターとしてスクリプトに渡される値は、単一引用符 (') で囲む必要があります。 これにより、渡された値はリテラルとして処理されます。

### <a name="setting-environment-variables"></a>環境変数の設定

環境変数を設定すると、次のステートメントで実行されます。

```bash
VARIABLENAME=value
```

前の例で、`VARIABLENAME` は変数の名前です。 変数にアクセスするには、`$VARIABLENAME` を使用します。 たとえば、PASSWORD という名前の環境変数として位置指定パラメーターで指定された値を割り当てるには、次のステートメントを使用します。

```bash
PASSWORD=$1
```

以後のこの情報へのアクセスでは `$PASSWORD`が使用されます。

スクリプト内で設定された環境変数は、スクリプトのスコープ内でのみ存在します。 場合によっては、スクリプトの終了後に永続化されるシステム全体の環境変数を追加する必要があります。 システム全体に関連する環境変数を追加するには、変数を `/etc/environment` に追加します。 たとえば、次のステートメントは `HADOOP_CONF_DIR` を追加します。

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>カスタム スクリプトを保管する場所へのアクセス

クラスターのカスタマイズに使用したスクリプトは、次の場所のいずれかに保存する必要があります。

* クラスターに関連付けられている __Azure Storage アカウント__。

* クラスターに関連付けられている __追加のストレージ アカウント__。

* __パブリックに読み取り可能な URI__。 たとえば、OneDrive、Dropbox、その他の ホスティング サービスに格納されたデータの URL。

* HDInsight クラスターに関連付けられている __Azure Data Lake Storage アカウント__。 HDInsight での Azure Data Lake Storage の使用の詳細については、「[クイック スタート:HDInsight のクラスターを設定する](./hdinsight-hadoop-provision-linux-clusters.md)」をご覧ください。

    > [!NOTE]  
    > HDInsight が Data Lake Storage へのアクセスに使用するサービス プリンシパルには、スクリプトに対する読み取りアクセスが必要です。

スクリプトによって使用されるリソースも公開されている必要があります。

Azure Storage account または Azure Data Lake Storage にファイルを格納すると、Azure ネットワーク内の両方で高速アクセスが可能になります。

> [!NOTE]  
> スクリプトの参照に使用する URI 形式は、使用されるサービスによって異なります。 HDInsight クラスターに関連付けられているストレージ アカウントの場合は、`wasb://` または `wasbs://` を使用します。 パブリックに読み取り可能な URI の場合は、`http://` または `https://` を使用します。 Data Lake Storage の場合は、`adl://` を使用します。

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>スクリプト アクションのデプロイ用チェックリスト

スクリプトのデプロイを準備するときの手順を次に示します。

* カスタム スクリプトが含まれているファイルを、デプロイ時にクラスター ノードからアクセス可能な場所に配置します。 たとえば、クラスターの既定のストレージ。 ファイルは、パブリックに読み取り可能なホスティング サービスにも格納できます。
* スクリプトがべき等であることを確認します。 これにより、スクリプトを同じノードで複数回実行できるようになります。
* /tmp などの一時ファイル ディレクトリを使用して、スクリプトで使用するダウンロード済みファイルを維持し、スクリプトの実行後にそれらをクリーンアップします。
* OS レベル設定や Hadoop サービス構成ファイルが変更された場合は、HDInsight サービスの再起動が必要になる場合があります。

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Script Action の実行方法

次のメソッドを使用して HDInsight クラスターをカスタマイズする場合、スクリプト アクションを使用できます。

* Azure portal
* Azure PowerShell
* Azure Resource Manager のテンプレート
* HDInsight .NET SDK

各メソッドの使用に関する詳細については、「[スクリプト アクションの使用方法](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>カスタム スクリプトのサンプル

Microsoft は、HDInsight クラスターにコンポーネントをインストールするサンプル スクリプトを提供しています。 スクリプト アクションの例については、[HDInsight クラスターでの Hue のインストールおよび使用](hdinsight-hadoop-hue-linux.md)に関する記事を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

次に、開発したスクリプトを使用しているときに発生する可能性のあるエラーを示します。

**エラー**: `$'\r': command not found`. `syntax error: unexpected end of file`が続くこともあります。

*原因*:このエラーはスクリプトの行が CRLF で終了する場合に発生します。 Unix システムでは、LF が行の終わりとしてのみ想定されます。

Windows の多くのテキスト エディターでは CRLF が一般的な行の終わりであるため、この問題は、Windows 環境でスクリプトが作成されたときに特に多く発生します。

*解決方法*:これがテキスト エディターのオプションである場合は、行の終わりとして Unix 形式または LE を選択します。 Unix システムで次のコマンドを使用して CRLF を LF に変更することもできます。

> [!NOTE]  
> CRLF 行の終わりが LF に変更されるという点で、次のコマンドはほぼ同等です。 システムで使用できるユーティリティに基づいて、いずれかを選択します。

| command | Notes |
| --- | --- |
| `unix2dos -b INFILE` |元のファイルは .BAK 拡張子でバックアップされます |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE には改行が LF のみのバージョンが含まれます |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | ファイルを直接変更します |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE には改行が LF のみのバージョンが含まれます |

**エラー**: `line 1: #!/usr/bin/env: No such file or directory`.

*原因*:このエラーはスクリプトがバイト オーダー マーク (BOM) を含む UTF-8 として保存された場合に発生します。

*解決方法*:ファイルを ASCII として、または BOM なしの UTF-8 として保存します。 Linux または Unix システムで次のコマンドを使用して、BOM なしのファイルを作成することもできます。

```bash
awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE
```

`INFILE` を BOM を含むファイルに置き換えます。 `OUTFILE` は、BOM なしのスクリプトを含む新しいファイルの名前にする必要があります。

## <a name="next-steps"></a><a name="seeAlso"></a>次のステップ

* [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)
* [HDInsight .NET SDK リファレンス](/dotnet/api/overview/azure/hdinsight) を使用して、HDInsight を管理する .NET アプリケーションの作成の詳細について理解します。
* [HDInsight REST API](/rest/api/hdinsight/) を使用して、REST を使って HDInsight クラスターで管理操作を実行する方法について理解します。