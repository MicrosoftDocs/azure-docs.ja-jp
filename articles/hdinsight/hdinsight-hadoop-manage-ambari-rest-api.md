---
title: Ambari REST API を使用して Hadoop を監視および管理する - Azure HDInsight
description: Ambari を使用して Azure HDInsight の Hadoop クラスターを監視および管理する方法を説明します。 このドキュメントでは、HDInsight クラスターに含まれている Ambari REST API を使用する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: hrasheed
ms.openlocfilehash: 4ab30f5f737b0f5188958c4686f82a0084c3ac35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059362"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Apache Ambari REST API を使用した HDInsight クラスターの管理

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari REST API を使用して Azure HDInsight の Apache Hadoop クラスターを管理および監視する方法を説明します。

## <a id="whatis"></a>Apache Ambari とは

[Apache Ambari](https://ambari.apache.org) は、[REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) に支えられた使いやすい Web UI を提供することで、Hadoop クラスターの管理と監視を簡素化します。  Ambari は既定で Linux ベースの HDInsight クラスターに付属しています。

## <a name="prerequisites"></a>前提条件

* **HDInsight 上の Hadoop クラスター**。 [Linux 上の HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関する記事をご覧ください。

* **Bash on Ubuntu on Windows 10**。  この記事の例では、Windows 10 上で Bash シェルを使用しています。 インストール手順については、「[Windows Subsystem for Linux Installation Guide for Windows 10 (Windows 10 用 Windows Subsystem for Linux インストール ガイド)](https://docs.microsoft.com/windows/wsl/install-win10)」をご覧ください。  他の [Unix シェル](https://www.gnu.org/software/bash/)も動作します。  各例は、少し変更を加えることで、Windows コマンド プロンプトでも使用できます。  代わりに、Windows PowerShell を使用することもできます。

* **jq**。コマンド ライン JSON プロセッサです。  [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) をご覧ください。

* **Windows PowerShell**。  代わりに、[Bash](https://www.gnu.org/software/bash/) を使用することもできます。

## <a name="base-uri-for-ambari-rest-api"></a>Ambari Rest API のベース URI

 HDInsight の Ambari REST API のベース URI (Uniform Resource Identifier) は、`https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` です。`CLUSTERNAME` はお使いのクラスターの名前です。  URI のクラスター名では、**大文字と小文字が区別**されます。  URI (`CLUSTERNAME.azurehdinsight.net`) の FQDN (完全修飾ドメイン名) 部分のクラスター名では大文字と小文字が区別されませんが、URI の他の部分で出現するときは大文字と小文字が区別されます。

## <a name="authentication"></a>Authentication

HDInsight の Ambari に接続するには、HTTPS が必要です。 クラスターの作成中に入力した管理者アカウント名 (既定値は **admin**) とパスワードを使用します。

## <a name="examples"></a>例

### <a name="setup-preserve-credentials"></a>セットアップ (資格情報の保存)
各例で再入力しなくて済むように、資格情報を保存します。  クラスター名は別の手順で保存します。

**A.Bash**  
`PASSWORD` を実際のパスワードに置き換えて、次のスクリプトを編集します。  その後、コマンドを入力します。

```bash
export password='PASSWORD'
```  

**B.PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>大文字と小文字が正しく区別されたクラスター名を確認する
クラスターの作成方法によっては、クラスター名の実際の大文字小文字の区別が予想と異なる場合があります。  ここでの手順では、実際の大文字小文字の区別を示し、以降のすべての例で使用できるように、それを変数に格納します。

次のスクリプトを編集して、`CLUSTERNAME` を実際のクラスター名に置き換えます。 その後、コマンドを入力します。 (FQDN のクラスター名では、大文字と小文字は区別されません)。

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>JSON データの解析

次の例では、[jq](https://stedolan.github.io/jq/) または [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) を使用して、JSON 応答ドキュメントを解析し、結果の `health_report` 情報だけを表示します。

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> クラスター ノードの FQDN を取得する

HDInsight を使用する際は、クラスター ノードの完全修飾ドメイン名 (FQDN) を知ることが必要になる場合があります。 次の例を使用して、クラスター内のさまざまなノードの FQDN を簡単に取得できます。

**すべてのノード**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**ヘッド ノード**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**ワーカー ノード**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper ノード**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> クラスター ノードの内部 IP アドレスを取得する

このセクションの例によって返される IP アドレスは、インターネット経由で直接アクセスすることはできません。 HDInsight クラスターが含まれている Azure Virtual Network 内からだけアクセスできます。

HDInsight と仮想ネットワークの操作の詳細については、[カスタム Azure Virtual Network による HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)に関するページを参照してください。

IP アドレスを検索するには、クラスター ノードの内部完全修飾ドメイン名 (FQDN) が必要です。 FQDN の取得後、ホストの IP アドレスを取得できます。 次の例は、まず、すべてのホスト ノードの FQDN を Ambari に照会し、次に Ambari に各ホストの IP アドレスを照会します。

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>既定のストレージを取得する

HDInsight クラスターを作成する場合は、クラスターの既定のストレージとして Azure Storage アカウントまたは Data Lake Storage を使用する必要があります。 Ambari を使用すると、クラスターが作成された後にこの情報を取得できます。 たとえば、HDInsight の外部のコンテナーにデータの読み取り/書き込みをする場合です。

以下の例では、クラスターの既定のストレージ構成を取得します。

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> これらの例は、サーバーに適用された最初の構成 (`service_config_version=1`) を返し、その中にこの情報が含まれています。 クラスターの作成後に変更された値を取得する場合は、構成のバージョンを一覧表示した後で最新の構成を取得することが必要になる場合があります。

戻り値は、以下の例のいずれかと似ています。

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - この値は、クラスターが既定のストレージとして Azure Storage アカウントを使用していることを示します。 `ACCOUNTNAME` 値は、ストレージ アカウントの名前です。 `CONTAINER` 部分は、ストレージ アカウント内の BLOB コンテナーの名前です。 コンテナーは、クラスターの HDFS 互換ストレージのルートです。

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - この値は、クラスターが既定のストレージとして Azure Data Lake Storage Gen2 を使用していることを示します。 `ACCOUNTNAME` 値と `CONTAINER` 値は、前述の Azure Storage の場合と同じ意味です。

* `adl://home` - この値は、クラスターが既定のストレージとして Azure Data Lake Storage Gen1 を使用していることを示します。

    Data Lake Storage のアカウント名を検索するには、以下の例を使用します。

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    戻り値は、`ACCOUNTNAME.azuredatalakestore.net` のようになります。ここで、`ACCOUNTNAME` は Data Lake Storage アカウントの名前です。

    クラスターのストレージが含まれている Data Lake Storage 内のディレクトリを検索するには、以下の例を使用します。

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    戻り値は、`/clusters/CLUSTERNAME/` のようになります。 この値は、Data Lake Storage アカウント内のパスです。 このパスは、クラスターの HDFS 互換ファイル システムのルートです。  

> [!NOTE]  
> [Azure PowerShell](/powershell/azure/overview) に用意されている [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) コマンドレットも、クラスターのストレージ情報を返します。

### <a name="get-all-configurations"></a> すべての構成を取得する

クラスターに使用できる構成を取得します。

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

この例は、クラスターにインストールされているコンポーネントの現在の構成 ( *tag* 値で特定) を含む JSON ドキュメントを返します。 次の例は Spark タイプのクラスターから返されるデータの抜粋です。

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>特定のコンポーネントの構成を取得する

興味のあるコンポーネントの構成を取得します。 次の例では、`INITIAL` を、前の要求から返されるタグ値で置き換えます。

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

この例では、`livy2-conf` コンポーネントの現在の構成を含む JSON ドキュメントが返されます。

### <a name="update-configuration"></a>構成を更新する

1. `newconfig.json` を作成します。  
   変更後、次のコマンドを入力します。

   * `livy2-conf` を必要なコンポーネントに置き換えます。
   * `INITIAL` を、「[すべての構成を取得する](#get-all-configurations)」で取得した `tag` の実際の値に置き換えます。

     **A.Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B.PowerShell**  
     この PowerShell スクリプトでは、[jq](https://stedolan.github.io/jq/) を使用しています。  実際のパスと [jq](https://stedolan.github.io/jq/) のバージョンを反映するように、次の `C:\HD\jq\jq-win64` を編集します。

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
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
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. `newconfig.json` を編集します。  
   `newconfig.json` ドキュメントを開き、`properties` オブジェクトの値を修正/追加します。 次の例では、`"livy.server.csrf_protection.enabled"` の値を `"true"` から `"false"` に変更します。

        "livy.server.csrf_protection.enabled": "false",

    変更が完了したら、ファイルを保存します。

3. `newconfig.json` を送信します。  
   次のコマンドを利用し、更新した構成を Ambari に送信します。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    これらのコマンドは、**newconfig.json** ファイルの内容を新たな望ましい構成としてクラスターに送信します。 要求から、JSON ドキュメントが返されます。 このドキュメントの **versionTag** 要素は、送信したバージョンに一致する必要があります。**configs** オブジェクトには、要求した構成変更が含まれます。

### <a name="restart-a-service-component"></a>サービス コンポーネントの再起動

この時点で Ambari Web UI に、新しい構成を有効にするには Spark サービスを再起動する必要がある旨が表示されます。 次の手順でサービスを再起動します。

1. 次のコマンドを使用して、Spark2 サービスのメンテナンス モードを有効にします。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. メンテナンス モードを確認します。  

    これらのコマンドは、メンテナンス モードを有効にするサーバーに JSON ドキュメントを送信します。 次の要求を利用すれば、サービスがメンテナンス モードに入っていることを確認できます。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    戻り値は `ON` です。

3. 次のコマンドを使用して、Spark2 サービスを無効にします。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
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
    > この URI で返される `href` 値はクラスター ノードの内部 IP アドレスを利用します。 クラスターの外部からこれを利用するには、`10.0.0.18:8080` 部分をクラスターの FQDN に置換します。  

4. 要求を確認します。  
    `29` を、前の手順で返された `id` の実際の値に置き換えて、次のコマンドを編集します。  以下のコマンドは、要求の状態を取得します。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    `COMPLETED` の応答は、要求が完了したことを示します。

5. 前の要求が完了したら、次のコマンドを使用して Spark2 サービスを開始します。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    これで、サービスが新しい構成を使用するようになりました。

6. 最後に、次を利用し、メンテナンス モードをオフにします。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>次の手順

REST API の完全なリファレンスについては、「[Apache Ambari API リファレンス V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)」をご覧ください。