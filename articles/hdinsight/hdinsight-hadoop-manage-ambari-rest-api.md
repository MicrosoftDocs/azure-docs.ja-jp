---
title: "Ambari REST API を使用して Azure HDInsight クラスターを監視および管理する | Microsoft Docs"
description: "Ambari を使用して Linux ベースの HDInsight クラスターを監視および管理する方法を説明します。 このドキュメントでは、HDInsight クラスターに含まれている Ambari REST API を使用する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/23/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2e26bd81c59fd53a0e8fc693dde30cb403995896
ms.openlocfilehash: 38d37e45c34c8c0a3bd2ed94f72944208292f466
ms.lasthandoff: 02/24/2017


---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Ambari REST API を使用した HDInsight クラスターの管理

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari には使いやすい Web UI と REST API が用意されているため、Hadoop クラスターを簡単に管理および監視できます。 Ambari は Linux オペレーティング システムを使用する HDInsight クラスターに含まれており、クラスターの監視と構成の変更を行うために使用します。 このドキュメントでは、Ambari REST API の使用方法の基本を説明します。

## <a id="whatis"></a>Ambari とは

[Apache Ambari](http://ambari.apache.org) は、Hadoop クラスターのプロビジョニング、管理、監視に使用する Web UI を簡単に使用できる方法を提供することで Hadoop の管理を簡略化します。 開発者は、 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)を使用して、これらの機能をアプリケーションに統合することができます。

Ambari は既定で Linux ベースの HDInsight クラスターに付属しています。

## <a name="how-to-use-the-ambari-rest-api"></a>Ambari REST API を使用する方法

> [!IMPORTANT]
> このドキュメントの情報と例は、Linux オペレーティング システムを使用する HDInsight クラスターが前提となっています。 詳細については、[HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md)に関する記事を参照してください。

このドキュメントでは、Bourne シェル (bash) と PowerShell の両方の例を提供しています。 bash の例は GNU bash 4.3.11 でテストしましたが、他の Unix シェルでも動作するはずです。 PowerShell の例は PowerShell 5.0 でテストしましたが、PowerShell 3.0 以降で動作するはずです。

__Bourne シェル__ (Bash) を使用する場合は、以下のものをインストールする必要があります。

* [cURL](http://curl.haxx.se/): cURL は、コマンド ラインから REST API を操作するためのユーティリティです。 このドキュメントでは、Ambari REST API との通信にこれを使用しています。

Bash を使用する場合でも PowerShell を使用する場合でも、[jq](https://stedolan.github.io/jq/) をインストールする必要があります。 Jq は、JSON ドキュメントを操作するためのユーティリティです。 このユーティリティは、**すべての** Bash の例と **1 つ**の PowerShell の例で使用されています。

### <a name="base-uri-for-ambari-rest-api"></a>Ambari Rest API のベース URI

HDInsight の Ambari REST API のベース URI は、https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME (**CLUSTERNAME** はクラスターの名前) です。

> [!IMPORTANT]
> URI (CLUSTERNAME.azurehdinsight.net) の FQDN (完全修飾ドメイン名) 部分のクラスター名では大文字と小文字が区別されませんが、URI の他の部分で出現するときは大文字と小文字が区別されます。 たとえば、クラスター名が `MyCluster` であれば、有効な URI は次のようになります。
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> 名前が&2; 番目に出現したときの大文字/小文字の指定が正しくないため、次の URI はエラーを返します。
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>認証

HDInsight の Ambari に接続するには、HTTPS が必要です。 接続を認証するときに、クラスターの作成時に指定した管理者アカウント名 (既定値は **admin**) とパスワードを使用する必要があります。

## <a name="examples-authentication-and-parsing-json"></a>例: 認証と JSON の解析

次の例は、ベース Ambari REST API に対して GET 要求を行う方法を示しています。

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> このドキュメントの Bash の例では、次のことが前提条件となっています。
>
> * クラスターのログイン名は、既定値の `admin` です。
> * `$PASSWORD` には、HDInsight ログイン コマンドのパスワードが含まれています。 この値を設定するには、`PASSWORD='mypassword'` を使用します。
> * `$CLUSTERNAME` には、クラスターの名前が含まれています。 この値を設定するには、`set CLUSTERNAME='clustername'` を使用します。

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> このドキュメントの PowerShell の例では、次のことが前提条件となっています。
>
> * `$creds` は、クラスターの管理者ログインとパスワードが含まれている資格情報オブジェクトです。 この値を設定するには、`$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` を使用し、入力を求めるメッセージが表示されたら資格情報を指定します。
> * `$clusterName` は、クラスターの名前が含まれている文字列です。 この値を設定するには、`$clusterName="clustername"` を使用します。

どちらの例も、次の例のような情報で始まる JSON ドキュメントを返します。

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>JSON データの解析

次の例は、`jq` を使用して JSON 応答ドキュメントを解析し、結果の `health_report` 情報だけを表示します。

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

PowerShell 3.0 以降では、`ConvertFrom-Json` コマンドレットが用意されています。このコマンドレットは、JSON ドキュメントを、PowerShell で操作しやすいオブジェクトに変換します。 次の例は、`ConvertFrom-Json` を使用して、結果の `health_report` 情報だけを表示します。

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> このドキュメントのほとんどの例では `ConvertFrom-Json` を使用して応答ドキュメントの要素を表示していますが、「[Ambari 構成の更新](#example-update-ambari-configuration)」の例では jq を使用しています。 この例では、jq を使用して、JSON 応答ドキュメントから新しいテンプレートを構築します。

REST API の完全なリファレンスについては、「 [Ambari API リファレンス V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)」をご覧ください。

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>例: クラスター ノードの FQDN を取得する

HDInsight を使用する際は、クラスター ノードの完全修飾ドメイン名 (FQDN) を知ることが必要になる場合があります。 次の例を使用して、クラスター内のさまざまなノードの FQDN を簡単に取得できます。

* **すべてのノード**

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **ヘッド ノード**

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **ワーカー ノード**

    ```bash
    curl -u admin:PASSWORD -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Zookeeper ノード**

    ```bash
    curl -u admin:PASSWORD -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>例: クラスター ノードの内部 IP アドレスの取得

> [!IMPORTANT]
> このセクションの例によって返される IP アドレスは、インターネット経由で直接アクセスすることはできません。 HDInsight クラスターが含まれている Azure Virtual Network 内からだけアクセスできます。
>
> HDInsight と仮想ネットワークの操作の詳細については、[カスタム Azure Virtual Network による HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)に関するページを参照してください。

IP アドレスを取得する前に、ホストの FQDN を知る必要があります。 FQDN の取得後、ホストの IP アドレスを取得できます。 次の例は、まず、すべてのホスト ノードの FQDN を Ambari に照会し、次に Ambari に各ホストの IP アドレスを照会します。

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>例: 既定のストレージの取得

HDInsight クラスターを作成する場合は、クラスターの既定のストレージとして Azure Storage アカウントまたは Data Lake Store を使用する必要があります。 Ambari を使用すると、クラスターが作成された後にこの情報を取得できます。 たとえば、HDInsight の外部のコンテナーにデータの読み取り/書き込みをする場合です。

以下の例では、クラスターの既定のストレージ構成を取得します。

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> これらの例は、サーバーに適用された最初の構成 (`service_config_version=1`) を返し、その中にこの情報が含まれています。 クラスターの作成後に変更された値を取得する場合は、構成のバージョンを一覧表示した後で最新の構成を取得することが必要になる場合があります。

戻り値は、以下の例のいずれかと似ています。

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - この値は、クラスターが既定のストレージとして Azure Storage アカウントを使用していることを示します。 `ACCOUNTNAME` 値は、ストレージ アカウントの名前です。 `CONTAINER` 部分は、ストレージ アカウント内の BLOB コンテナーの名前です。 コンテナーは、クラスターの HDFS 互換ストレージのルートです。

* `adl://home` - この値は、クラスターが既定のストレージとして Azure Data Lake Store を使用していることを示します。

    Data Lake Store のアカウント名を検索するには、以下の例を使用します。

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    戻り値は、`ACCOUNTNAME.azuredatalakestore.net` のようになります。ここで、`ACCOUNTNAME` は Data Lake Store アカウントの名前です。

    クラスターのストレージが含まれている Data Lake Store 内のディレクトリを検索するには、以下の例を使用します。

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    戻り値は、`/clusters/CLUSTERNAME/` のようになります。 この値は、Data Lake Store アカウント内のパスです。 このパスは、クラスターの HDFS 互換ファイル システムのルートです。 

> [!NOTE]
> [Azure PowerShell](https://docs.microsoft.com/powershell/) に用意されている `Get-AzureRmHDInsightCluster` コマンドレットも、クラスターのストレージ情報を返します。


## <a name="example-get-configuration"></a>例: 構成の取得

1. クラスターに使用できる構成を取得します。

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    この例は、クラスターにインストールされているコンポーネントの現在の構成 ( *tag* 値で特定) を含む JSON ドキュメントを返します。 次の例は Spark タイプのクラスターから返されるデータの抜粋です。
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. 興味のあるコンポーネントの構成を取得します。 次の例では、`INITIAL` を、前の要求から返されるタグ値で置き換えます。

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    この例では、`core-site` コンポーネントの現在の構成を含む JSON ドキュメントが返されます。

## <a name="example-update-configuration"></a>例: 構成の更新

1. Ambari で "desired configuration (望ましい構成)" として格納される現在の構成を取得します。

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    この例は、クラスターにインストールされているコンポーネントの現在の構成 ( *tag* 値で特定) を含む JSON ドキュメントを返します。 次の例は Spark タイプのクラスターから返されるデータの抜粋です。
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    この一覧から、コンポーネントの名前をコピーする必要があります (たとえば、**spark\_thrift\_sparkconf** と **tag** 値)。

2. 次のコマンドを利用し、コンポーネントの構成とタグを取得します。 構成を取得するコンポーネントとタグで **spark-thrift-sparkconf** と **INITIAL** を置換します。
   
    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```
   
    jq を使用して、HDInsight から取得したデータを新しい構成テンプレートに変換します。 具体的には、これらの例では以下の操作を実行します。
   
    * 文字列 "version" と日付を含む一意の値が作成され、`newtag` に格納されます。

    * 新しい望ましい構成のルート ドキュメントが作成されます。

    * `.items[]` 配列のコンテンツが取得され、**desired_config** 要素の下に追加されます。

    * 新しい構成の送信に必要ないため、`href`、`version`、`Config` の各要素が削除されます。

    * 値を `version#################` に指定して `tag` 要素を追加します。 数値部分は現在の日付に基づきます。 構成ごとに一意のタグを与える必要があります。
     
    最後に、データが `newconfig.json` ドキュメントに保存されます。 ドキュメントの構造は次の例の構造に似たものになります。
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. `newconfig.json` ドキュメントを開き、`properties` オブジェクトの値を修正/追加します。 次の例では、`"spark.yarn.am.memory"` の値を `"1g"` から `"3g"` に変更します。 また、`"spark.kryoserializer.buffer.max"` も追加し、値を `"256m"` にします。
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    変更が完了したら、ファイルを保存します。

4. 次のコマンドを利用し、更新した構成を Ambari に送信します。
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    これらのコマンドは、**newconfig.json** ファイルの内容を新たな望ましい構成としてクラスターに送信します。 要求から、JSON ドキュメントが返されます。 このドキュメントの **versionTag** 要素は、送信したバージョンに一致する必要があります。**configs** オブジェクトには、要求した構成変更が含まれます。

### <a name="example-restart-a-service-component"></a>例: サービス コンポーネントの再起動

この時点で Ambari Web UI に、新しい構成を有効にするには Spark サービスを再起動する必要がある旨が表示されます。 次の手順でサービスを再起動します。

1. 次を利用し、Spark サービスのメンテナンス モードを有効にします。

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    これらのコマンドは、メンテナンス モードを有効にするサーバーに JSON ドキュメントを送信します。 次の要求を利用すれば、サービスがメンテナンス モードに入っていることを確認できます。
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    戻り値は `ON` です。

2. 次に、次のコードを利用し、サービスを無効にします。

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    応答は次の例のようになります。
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > この URI で返される `href` 値はクラスター ノードの内部 IP アドレスを利用します。 クラスターの外部からこれを利用するには、`10.0.0.18:8080' 部分をクラスターの FQDN に置換します。 
    
    以下のコマンドは、要求の状態を取得します。

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    `COMPLETED` の応答は、要求が完了したことを示します。

3. 前の要求が完了したら、次を利用し、サービスを開始します。
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    これで、サービスが新しい構成を使用するようになりました。

4. 最後に、次を利用し、メンテナンス モードをオフにします。
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>次のステップ

REST API の完全なリファレンスについては、「 [Ambari API リファレンス V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)」をご覧ください。


