---
title: "HDInsight での Script Action 開発 | Microsoft Docs"
description: "Script Action を使用して Hadoop クラスターをカスタマイズする方法について説明します。 Script Action は、Hadoop クラスターで実行される追加のソフトウェアのインストールや、クラスターにインストールされているアプリケーションの構成を変更するために使用することができます。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 6c468c8ed0d817a32c343d55cac780b904ee8668
ms.openlocfilehash: 6cedd18c22bcd97f7fb2b6926bdf637fe9f04ab4
ms.lasthandoff: 02/07/2017


---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>HDInsight の Windows ベースのクラスター用 Script Action スクリプトを開発する
HDInsight 用の Script Action スクリプトを記述する方法について説明します。 Script Action スクリプトの使用方法については、「 [Script Action を使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。 Linux ベースの HDInsight クラスターに関する同様の記事については、「 [HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」をご覧ください。



> [!IMPORTANT]
> このドキュメントの手順は、Windows ベースの HDInsight クラスターに対してのみ機能します。 Windows では、バージョン 3.4 より前の HDInsight のみを使用できます。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。 Linux ベースのクラスターでのスクリプト アクションの使用方法について詳しくは、[HDInsight でのスクリプト アクションの開発 (Linux) ](hdinsight-hadoop-script-actions-linux.md)に関するページをご覧ください。
>
>



Script Action は、Hadoop クラスターで実行される追加のソフトウェアのインストールや、クラスターにインストールされているアプリケーションの構成を変更するために使用することができます。 Script Action は、HDInsight クラスターがデプロイされる際にクラスター ノードで実行されるスクリプトであり、クラスター内のノードが HDInsight 構成を完了すると実行されます。 Script Action はシステム管理者アカウント権限で実行され、完全なアクセス権をクラスター ノードに提供します。 各クラスターには、指定された順序で実行される Script Action の一覧が提供されます。

> [!NOTE]
> 次のようなエラー メッセージが表示されることがあります。
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage : 用語 'Save-HDIFile' は、コマンドレット、関数、スクリプト ファイル、または操作可能なプログラムの名前として認識されません。 名前が正しく記述されていることを確認し、パスが含まれている場合はそのパスが正しいことを確認してから、再試行してください。
> これは、ヘルパー メソッドを含めていなかったためです。  「 [カスタム スクリプトのためのヘルパー メソッド](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts)」を参照してください。
>
>

## <a name="sample-scripts"></a>サンプルのスクリプト
Windows オペレーティング システムで HDInsight クラスターを作成する場合、Script Action は、Azure PowerShell スクリプトです。サイト構成ファイルを構成するためのサンプル スクリプトを次に示します。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

このスクリプトでは、構成ファイルの名前、変更するプロパティ、設定する値、および説明の&4; つのパラメーターを使用します。 For example:

    hive-site.xml hive.metastore.client.socket.timeout 90

これらのパラメーターは、hive-site.xml ファイルの hive.metastore.client.socket.timeout  の値を 90 に設定します。  既定値は 60 秒です。

このサンプル スクリプトは、 [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1)からも入手できます。

HDInsight は、HDInsight クラスターで追加のコンポーネントをインストールするためのいくつかのスクリプトを提供します。

| 名前 | スクリプト |
| --- | --- |
| **Spark のインストール** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1。 「[HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]」を参照してください。 |
| **R のインストール** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。 [HDInsight クラスターでの R のインストールと使用][hdinsight-r-scripts]に関する記事を参照してください。 |
| **Solr のインストール** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。 「 [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)」をご覧ください。 |
| - **Giraph のインストール** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。 「 [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)」をご覧ください。 |

Script Action は、Azure ポータル、Azure PowerShell から、または HDInsight .NET SDK を使用してデプロイできます。  詳細については、「[Script Action を使って HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]」をご覧ください。

> [!NOTE]
> サンプル スクリプトは、HDInsight クラスター Version 3.1 以降でのみ機能します。 HDInsight クラスター バージョンの詳細については、「 [HDInsight クラスター バージョン](hdinsight-component-versioning.md)」をご覧ください。
>
>

## <a name="helper-methods-for-custom-scripts"></a>カスタム スクリプトのためのヘルパー メソッド
Script Action ヘルパー メソッドは、カスタム スクリプトの記述で利用できるユーティリティです。 これらは [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)で定義されており、次を使用してスクリプトに含めることができます。

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

このスクリプトによって提供されるヘルパー メソッドを次に示します。

| ヘルパー メソッド | Description |
| --- | --- |
| **Save-HDIFile** |クラスターに割り当てられた Azure VM のノードに関連付けられているローカル ディスク上の場所に、指定した Uniform Resource Identifier (URI) からファイルをダウンロードする |
| **Expand-HDIZippedFile** |ZIP ファイルを解凍する |
| **Invoke-HDICmdScript** |cmd.exe からスクリプトを実行する |
| **Write-HDILog** |Script Action に使用されるカスタム スクリプトから出力を書き込む |
| **Get-Services** |スクリプトが実行されるコンピューターで実行されているサービスの一覧を取得する |
| **Get-Service** |入力としてサービス名が指定されると、スクリプトが実行されるコンピューター上の特定のサービスの詳細な情報を取得する (サービス名、プロセス ID、状態など) |
| **Get-HDIServices** |スクリプトが実行されるコンピューターで実行されている HDInsight サービスの一覧を取得する |
| **Get-HDIService** |入力として HDInsight サービス名が指定されると、スクリプトが実行されるコンピューター上の特定のサービスの詳細な情報を取得する (サービス名、プロセス ID、状態など) |
| **Get-ServicesRunning** |スクリプトが実行されるコンピューターで実行されているサービスの一覧を取得する |
| **Get-ServiceRunning** |スクリプトが実行されるコンピューター上で (名前別に) 特定のサービスが実行されているかどうかを確認する |
| **Get-HDIServicesRunning** |スクリプトが実行されるコンピューターで実行されている HDInsight サービスの一覧を取得する |
| **Get-HDIServiceRunning** |スクリプトが実行されるコンピューター上で (名前別に) 特定の HDInsight サービスが実行されているかどうかを確認する |
| **Get-HDIHadoopVersion** |スクリプトが実行されるコンピューターにインストールされている Hadoop のバージョンを取得する |
| **Test-IsHDIHeadNode** |スクリプトが実行されるコンピューターがヘッドノードであるかどうかを確認する |
| **Test-IsActiveHDIHeadNode** |スクリプトが実行されるコンピューターがアクティブなヘッドノードであるかどうかを確認する |
| **Test-IsHDIDataNode** |スクリプトが実行されるコンピューターがデータノードであるかどうかを確認する |
| **Edit-HDIConfigFile** |構成ファイル hive-site.xml、core-site.xml、hdfs-site.xml、mapred-site.xml、yarn-site.xml を編集する |

## <a name="best-practices-for-script-development"></a>スクリプトの開発におけるベスト プラクティス
HDInsight クラスター向けのカスタム スクリプトを開発する際、留意すべきベスト プラクティスがあります。

* Hadoop のバージョンのチェック

    HDInsight Version 3.1 (Hadoop 2.4) 以降のみが、Script Action を使用したクラスターでのカスタム コンポーネントのインストールをサポートします。 カスタム スクリプトでは、スクリプト内のその他のタスクの実行を続行する前に、バージョンをチェックするために、 **Get-HDIHadoopVersion** ヘルパー メソッドを使用する必要があります。
* スクリプト リソースへの安定したリンクの提供

    ユーザーは、クラスターのカスタマイズで使用されるすべてのスクリプトとその他のアーティファクトがクラスターの有効期間全体で使用できること、実行中これらのファイルのバージョンが変更されないことを確認する必要があります。 クラスター内のノードの再イメージ化が必要な場合、これらのリソースが必要になります。 ベスト プラクティスとして、すべてをダウンロードして、ユーザーを制御するストレージ アカウントにアーカイブします。 これは、既定のストレージ アカウントか、カスタマイズされたクラスターのデプロイ時に指定されている追加のストレージ アカウントのいずれかです。
    たとえば、ドキュメントに用意されている Spark と R のカスタマイズされたクラスターのサンプルでは、このストレージ アカウントにリソースのローカル コピーが作成されています (https://hdiconfigactions.blob.core.windows.net/)。
* クラスターのカスタマイズ スクリプトはべき等にする

    HDInsight クラスターのノードはクラスターの有効期間中に再イメージ化されることを予測する必要があります。 クラスターのカスタマイズ スクリプトは、クラスターが再イメージ化されるたびに実行されます。 再イメージ化の際に、クラスターの最初の作成時に初めてスクリプトが実行された直後と同じカスタマイズ状態に戻ることを確認するという意味で、このスクリプトはべき等であるように設計する必要があります。 たとえば、カスタム スクリプトが最初の実行時にアプリケーションを D:\AppLocation にインストールする場合、その後スクリプトを実行するたびに、再イメージ化の際、スクリプトはアプリケーションが D:\AppLocation に存在するかどうかを確認した後で、スクリプト内の他のステップを続行する必要があります。
* 最適な場所にカスタム コンポーネントをインストールする

    クラスター ノードが再イメージ化されると、C:\ リソース ドライブと D:\ システム ドライブは再フォーマットされ、それらのドライブにインストールされたデータとアプリケーションが失われる可能性があります。 これは、クラスターの一部である Azure 仮想マシン (VM) ノードが停止して、新しいノードに置き換えられた場合にも起こる可能性があります。 コンポーネントは D:\ ドライブやクラスタ上の C:\apps にインストールできます。 C:\ ドライブのその他の場所はすべて予約されています。 クラスターのカスタマイズのスクリプトに、アプリケーションやライブラリがインストールされる場所を指定します。
* クラスターのアーキテクチャの高可用性を確保

    HDInsight は、高可用性を確保するために、アクティブ / パッシブ アーキテクチャを備えています。1 つのヘッドノードはアクティブ モード (HDInsight サービスが実行されている) であり、その他のヘッドノードはスタンバイ モード (HDInsight サービスが実行されていない) です。 HDInsight サービスが中断されると、ノードはアクティブ モードおよびパッシブ モードを切り替えます。 高可用性を確保する目的で Script Action を使用して両方のヘッド ノードにサービスをインストールする場合、HDInsight のフェールオーバー メカニズムが、このようなユーザーがインストールしたサービスを自動的にフェールオーバーできなくなるため、注意してください。 そのため、高可用性が期待される HDInsight ヘッド ノードにユーザーがインストールしたサービスは、アクティブ/パッシブ モードの場合に独自のフェールオーバーのメカニズムを持つか、アクティブ　 / アクティブ モードになる必要があります。

    *ClusterRoleCollection* パラメーターに値としてヘッドノードの役割を指定すると、両方のヘッドノードで HDInsight のスクリプト アクション コマンドが実行されます。 そのため、カスタム スクリプトの設計時には、スクリプトがこの設定を認識するようにしてください。 両方のヘッドノードで同じサービスがインストールし、開始され、互いに競合するという問題を回避する必要があります。 また、再イメージ化の際にデータが失われるので、Script Action を通じてインストールされるソフトウェアはそうした事象に対して回復可能である必要があります。 多数のノードに分散された高可用性のデータを操作するアプリケーションを設計する必要があります。 同時に再イメージ化できる、クラスター内のノードは最大で 1/5 であることをに注意してください。
* Azure BLOB ストレージを使用するカスタム コンポーネントの構成

    クラスター ノードにインストールするカスタム コンポーネントは、Hadoop 分散ファイル システム (HDFS) ストレージを使用するように既定で構成されている場合があります。 代わりに Azure BLOB ストレージを使用するには、構成を変更する必要があります。 クラスターの再イメージ化の際に、HDFS ファイル システムはフォーマットされ、保管されているすべてのデータが失われます。 代わりに Azure BLOB ストレージを使用すると、データは確実に保持されます。

## <a name="common-usage-patterns"></a>一般的な使用パターン
このセクションでは、独自のカスタム スクリプトの書き込み中に発生する可能性がある一般的な使用状況パターンの実装についてのガイダンスを提供します。

### <a name="configure-environment-variables"></a>環境変数を構成する
多くの場合、スクリプト アクションの開発では、環境変数を設定する必要があると感じることがあります。 たとえば、最も可能性の高いシナリオは、外部サイトからバイナリをダウンロードし、クラスターにインストールして、'PATH' 環境変数にインストールされている場所を追加する場合です。 次のスニペットでは、カスタム スクリプトで環境変数を設定する方法を示します。

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

このステートメントは、環境変数 **MDS_RUNNER_CUSTOM_CLUSTER** を値 'true' に設定します。またコンピューター全体にこの変数の範囲を設定します。 環境変数はコンピューターやユーザーの適切な範囲で設定されていることが重要になることがあります。 環境変数の設定の詳細については、[こちら][1]を参照してください。

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>カスタム スクリプトを保管する場所へのアクセス
クラスターのカスタマイズに使用したスクリプトは、クラスターの既定のストレージ アカウントかその他のストレージ アカウントにおける読み取り専用のパブリック コンテナーのいずれかにする必要があります。 スクリプトが他の場所に配置されているリソースにアクセスする場合、パブリックでアクセスできる (少なくともパブリック読み取り専用にする) ようにする必要があります。 たとえば、SaveFile HDI コマンドを使用してファイルにアクセスし、保存する必要がある場合などです。

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

この例では、ストレージ アカウント「somestorageaccount」のコンテナー「somecontainer」を、パブリックからアクセスできるようにする必要があります。 そうしないと、スクリプトは「Not Found」例外をスローして失敗します。

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Add-AzureRmHDInsightScriptAction コマンドレットにパラメーターを渡す
複数のパラメーターを Add-AzureRmHDInsightScriptAction コマンドレットに渡すには、スクリプトのすべてのパラメーターを格納する文字列値の書式を設定する必要があります。 For example:

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

または

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>障害が発生したクラスターのデプロイメントにおける例外の実行
クラスターのカスタマイズが予想どおりに成功しなかった場合に正確な通知を受け取るには、例外をスローし、クラスターの作成を失敗させることが重要になります。 たとえば、ファイルが存在する場合はファイルを処理し、ファイルが存在しない場合はエラー状況を処理する必要があります。 これにより、スクリプトは正常に終了し、クラスターの状態が正確に把握できるようになります。 次のスニペットでは、これを実行する方法の例を示します。

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

このスニペットでは、ファイルが存在しない場合、スクリプトは、エラー メッセージを表示した後に実際には正常終了の状態になります。そのため、クラスターは、クラスター カスタマイズ プロセスが "正常に" 完了したと見なして実行状態になります。 ファイルが存在しなかったために実際はクラスターのカスタマイズが期待したとおりに進まなかったという事実を正確に通知する必要がある場合は、例外をスローしてクラスター カスタマイズ ステップを失敗させるほうが適切です。 その場合は、代わりに次のサンプル コード スニペットを使用してください。

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Script Action のデプロイ用チェックリスト
スクリプトのデプロイを準備する際に実行した手順を次に示します。

1. カスタム スクリプトが含まれているファイルを、デプロイ時にクラスター ノードからアクセス可能な場所に配置します。 これには、クラスターのデプロイ時に指定された既定または追加のストレージ アカウント、またはその他のパブリックにアクセス可能なストレージ コンテナーを指定できます。
2. スクリプトがべき等に実行されるようにチェックを追加し、スクリプトが同じノードで複数回実行可能にします。
3. **Write-Output** Azure Powershell コマンドレットを使用して、STDOUT と STDERR に出力します。 **Write-Host**を使用しないでください。
4. $env:TEMP などの一時ファイル フォルダーを使用して、スクリプトで使用するダウンロード済みファイルを維持し、スクリプトの実行後にそれらをクリーンアップします。
5. カスタム ソフトウェアは D:\ または C:\apps のみにインストールします。 C: ドライブのその他の場所は予約されているため使用しないでください。 C:\apps フォルダー以外の C: ドライブにファイルをインストールすると、ノードの再イメージ化の際にセットアップが失敗する可能性があります。
6. OS レベル設定や Hadoop サービス構成ファイルが変更された場合、スクリプトに設定された環境変数などの OS レベル設定を有効にするために、HDInsight サービスを再起動することをお勧めします。

## <a name="debug-custom-scripts"></a>カスタム スクリプトをデバッグする
スクリプト エラー ログは、他の出力と共に、クラスターの作成時に指定した既定のストレージ アカウント内に格納されます。 ログは、*u<\cluster-name-fragment><\time-stamp>setuplog* という名前のテーブルに格納されます。 これらはクラスター内でスクリプトが実行されるすべてのノード (ヘッドノードと worker ノード) から取得されたレコードを持つ集計ログです。
ログを確認する簡単な方法は、HDInsight Tools for Visual Studio を使用することです。 ツールのインストールについては、 [HDInsight Hadoop Tools for Visual Studio の使用開始](hdinsight-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)

**Visual Studio を使用してログを確認するには**

1. Visual Studio を開きます。
2. **[表示]** をクリックし、**[サーバー エクスプローラー]** をクリックします。
3. [Azure] を右クリックし、 **[Microsoft Azure サブスクリプションへの接続]**をクリックして、資格情報を入力します。
4. **[ストレージ]** を展開し 、既定のファイル システムとして使用されている、Azure のストレージ アカウントを展開して、**[テーブル]** を展開し、テーブル名をダブルクリックします。

カスタム スクリプトの STDOUT と STDERR の両方を表示するために、クラスター ノードにリモート接続することもできます。 各ノード上のログは、そのノード専用で、**C:\HDInsightLogs\DeploymentAgent.log** に記録されます。 これらのログ ファイルには、カスタム スクリプトからのすべての出力が記録されます。 Spark Script Action のログ スニペットの例は次のようになります。

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


このログから、HEADNODE0 という名前の VM で Spark Script Action が実行され、実行中に例外がスローされなかったことがわかります。

実行エラーが発生すると、それを記述する出力もこのログ ファイルに含まれます。 これらのログで提供される情報は、発生する可能性のあるスクリプトの問題をデバッグするときに役立ちます。

## <a name="see-also"></a>関連項目
* [Script Action を使って HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize]
* [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]
* [HDInsight クラスターに R をインストールして使用する][hdinsight-r-scripts]
* [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)
* [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx

