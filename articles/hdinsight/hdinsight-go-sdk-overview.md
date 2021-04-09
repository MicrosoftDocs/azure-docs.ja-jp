---
title: Azure HDInsight SDK for Go
description: Azure HDInsight SDK for Go および Apache Hadoop クラスターを使用するための参考資料
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: 09564bd149488d956586c8a0e349cd79eec358a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582753"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK for Go (プレビュー)

## <a name="overview"></a>概要
HDInsight SDK for Go に用意されているクラスと関数を使用して HDInsight クラスターを管理できます。 これには、スクリプト アクションを作成、削除、更新、一覧表示、サイズ変更、実行したり、HDInsight クラスターのプロパティを監視、取得したりする操作が含まれます。

> [!NOTE]  
>この SDK の GoDoc 参考資料は[こちらでも入手できます](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight)。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* [`go get` ツール](https://github.com/golang/go/wiki/GoGetTools)。
* [Go](https://golang.org/dl/)。

## <a name="sdk-installation"></a>SDK のインストール

GOPATH の場所から `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight` を実行します。

## <a name="authentication"></a>認証

SDK は最初に Azure サブスクリプションで認証する必要があります。  以下の例に従って、サービス プリンシパルを作成し、これを使用して認証します。 その後、`ClustersClient` のインスタンスを生成します。これには、管理操作の実行に使用できる関数が多数含まれています (以下のセクションで説明します)。

> [!NOTE]  
> 認証方法は以下の例の他にもあり、そちらの方がご自身のニーズに適している可能性もあります。 すべての関数の概要は「[Azure SDK for Go における認証方法](/azure/go/azure-sdk-go-authorization)」にあります。

### <a name="authentication-example-using-a-service-principal"></a>サービス プリンシパルを使用した認証の例

まず、[Azure Cloud Shell](https://shell.azure.com/bash) にログインします。 現在、サービス プリンシパル作成対象のサブスクリプションを使用していることを確認します。

```azurecli-interactive
az account show
```

ご自身のサブスクリプション情報が JSON として表示されます。

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

正しいサブスクリプションにログインしていない場合は、以下を実行して正しいサブスクリプションを選択します。 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> 他の関数で、たとえば Azure portal で HDInsight クラスターを作成するといった方法で HDInsight リソース プロバイダーをまだ登録していない場合は、認証前に登録する必要があります。 これを [Azure Cloud Shell](https://shell.azure.com/bash) から実行するには、次のコマンドを実行します。
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

次に、ご自身のサービス プリンシパルの名前を選択し、次のコマンドを使用して作成します。

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

サービス プリンシパル情報が JSON として表示されます。

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

次のスニペットをコピーし、コマンド実行後に返された JSON の文字列を `TENANT_ID`、`CLIENT_ID`、`CLIENT_SECRET`、および `SUBSCRIPTION_ID` に入力して、サービス プリンシパルを作成します。

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>クラスターの管理

> [!NOTE]  
> このセクションでは、`ClusterClient` インスタンスの認証と構成が既に完了していること、および `client` と呼ばれる変数にそのインスタンスが格納されていることを前提としています。 `ClusterClient` を認証し、取得する方法については、上記の「認証」セクションを参照してください。

### <a name="create-a-cluster"></a>クラスターの作成

新しいクラスターを作成するには、`client.Create()` を呼び出します。 

#### <a name="example"></a>例

この例は、2 つのヘッド ノードと 1 つのワーカー ノードを含む [Apache Spark](https://spark.apache.org/) クラスターを作成する方法を示しています。

> [!NOTE]  
> 次に示すように、最初にリソース グループとストレージ アカウントを作成する必要があります。 これらが既に作成済みの場合、この手順はスキップできます。

##### <a name="creating-a-resource-group"></a>リソース グループを作成する

[Azure Cloud Shell](https://shell.azure.com/bash) を使用して次を実行することで、リソース グループを作成できます

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>ストレージ アカウントの作成

[Azure Cloud Shell](https://shell.azure.com/bash) を使用して次を実行することで、ストレージ アカウントを作成できます。

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

ここで、次のコマンドを実行して、ストレージ アカウントに対するキーを取得します (これはクラスターを作成するときに必要になります)。

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
以下の Go スニペットでは、2 つのヘッド ノードと 1 つのワーカー ノードを含む Spark クラスターが作成されます。 コメントの説明に従って空白の変数を入力します。また、ご自身のニーズに合わせて他のパラメーターを変更します。

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>クラスターの詳細の取得

特定のクラスターのプロパティを取得するには:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>例

`get` を使用すると、ご自身のクラスターを適切に作成できたことを確認できます。

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

出力は次のようになります。

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>クラスターを一覧表示する

#### <a name="list-clusters-under-the-subscription"></a>サブスクリプションのクラスターの一覧表示

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>リソース グループ別のクラスターの一覧表示

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> `List()` と `ListByResourceGroup()` の両方が `ClusterListResultPage` 構造体を返します。 次のページを取得するには、`Next()` を呼び出します。 以下の例に示すように、`ClusterListResultPage.NotDone()` で `false` が返されるまでこれを繰り返すことができます。

#### <a name="example"></a>例

次の例では、現在のサブスクリプションのすべてのクラスターのプロパティが出力されます。

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>クラスターの削除

クラスターを削除するには:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>クラスター タグの更新

次のように、特定のクラスターのタグを更新できます。

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>例

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>クラスターのサイズ変更

特定のクラスターの worker ノード数をサイズ変更するには、次のように新しいサイズを指定します。

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>クラスターの監視

HDInsight 管理 SDK を使用して、Operations Management Suite (OMS) でご自身のクラスターの監視を管理することもできます。

管理に使用するために `ClusterClient` を作成したときと同様に、監視に使用するために `ExtensionClient` を作成する必要があります。 上記の [認証] セクションを完了すると、次のように `ExtensionClient` を作成できます。

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 下の監視の例では、`extClient` という名前の `ExtensionClient` を既に初期化し、上記のようにその `Authorizer` を設定していることが前提となっています。

### <a name="enable-oms-monitoring"></a>OMS 監視の有効化

> [!NOTE]  
> OMS の監視を有効にするには、既存の Log Analytics ワークスペースが必要です。 まだ作成していない場合、その方法については、「[Azure ポータルで Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」を参照してください。

ご自身のクラスターで OMS 監視を有効にするには:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>OMS 監視の状態の表示

ご自身のクラスターに対する OMS の状態を取得するには:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>OMS 監視の無効化

ご自身のクラスターに対する OMS を無効にするには:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>スクリプト操作

HDInsight には、クラスターをカスタマイズするためにカスタム スクリプトを呼び出すスクリプト アクションという構成関数があります。

> [!NOTE]  
> スクリプト アクションを使用する方法の詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](./hdinsight-hadoop-customize-cluster-linux.md)」を参照してください

### <a name="execute-script-actions"></a>スクリプト アクションの実行

次のように、特定のクラスターに対してスクリプト アクションを実行できます。

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

'スクリプト アクションの削除' 操作と '保存済みスクリプト アクションの一覧表示' 操作の場合、管理に使用するために `ScriptActionsClient` を作成したときと同様に `ClusterClient` を作成する必要があります。 上記の [認証] セクションを完了すると、次のように `ScriptActionsClient` を作成できます。

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 下のスクリプト アクションの例では、`scriptActionsClient` という名前の `ScriptActionsClient` を既に初期化し、上記のようにその `Authorizer` を設定していることが前提となっています。

### <a name="delete-script-action"></a>スクリプト アクションの削除

特定のクラスターに対して指定した保存済みスクリプト アクションを削除するには:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>保存済みスクリプト アクションの一覧表示

> [!NOTE]  
> いずれの `ListByCluster()` でも `ScriptActionsListPage` 構造体が返されます。 次のページを取得するには、`Next()` を呼び出します。 以下の例に示すように、`ClusterListResultPage.NotDone()` で `false` が返されるまでこれを繰り返すことができます。

指定したクラスターに対する保存済みスクリプト アクションを一覧表示するには:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>例

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>スクリプトの全実行履歴の一覧表示

この操作のためには、管理に使用するために `ClusterClient` を作成したときと同様に `ScriptExecutionHistoryClient` を作成する必要があります。 上記の [認証] セクションを完了すると、次のように `ScriptActionsClient` を作成できます。

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> 以下では、`scriptExecutionHistoryClient` という名前の `ScriptExecutionHistoryClient` を既に初期化し、上記のようにその `Authorizer` を設定していることが前提となっています。

指定したクラスターに対するスクリプトの実行履歴をすべて一覧表示するには:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>例

この例では、過去のすべてのスクリプト実行の詳細が出力されます。

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>次のステップ

[GoDoc 参考資料](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight)を読みます。 GoDoc では、SDK のすべての関数のために参照文書を用意しています。
