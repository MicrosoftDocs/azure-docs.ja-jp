---
title: コンテナー グループの YAML リファレンス
description: コンテナー グループを構成するために Azure Container Instances によってサポートされている YAML ファイルのリファレンス
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896564"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML リファレンス: Azure Container Instances

この記事では、[コンテナー グループ](container-instances-container-groups.md)を構成するために Azure Container Instances によってサポートされている YAML ファイルの構文とプロパティについて説明します。 Azure CLI の [az container create][az-container-create] コマンドにグループ構成を入力するには、YAML ファイルを使用します。 

YAML ファイルは、再現可能なデプロイのためにコンテナー グループを構成する便利な方法です。 これは、[Resource Manager テンプレート](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) または Azure Container Instances SDK を使用したコンテナー グループの作成または更新に対する簡便な代替手段です。

> [!NOTE]
> このリファレンスは、Azure Container Instances REST API バージョン `2018-10-01` 用の YAML ファイルに適用されます。

## <a name="schema"></a>スキーマ 

YAML ファイルのスキーマを次に示します。重要なプロパティはコメントで強調してあります。 このスキーマのプロパティの説明については、「[プロパティ値](#property-values)」セクションを参照してください。

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>プロパティ値

次の表では、スキーマに設定する必要がある値について説明します。

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  名前 | string | はい | コンテナー グループの名前。 |
|  apiVersion | enum | はい | 2018-10-01 |
|  location | string | いいえ | リソースの場所。 |
|  tags | object | いいえ | リソース タグ。 |
|  identity | object | いいえ | コンテナー グループの ID (構成されている場合)。 - [ContainerGroupIdentity オブジェクト](#ContainerGroupIdentity) |
|  properties | object | はい | [ContainerGroupProperties オブジェクト](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  type | enum | いいえ | コンテナー グループに使用される ID の種類。 種類 "SystemAssigned、UserAssigned" には、暗黙的に作成された ID とユーザー割り当て ID のセットの両方が含まれます。 種類 "None" は、コンテナー グループから ID を削除します。 - SystemAssigned、UserAssigned、SystemAssigned、UserAssigned、None |
|  userAssignedIdentities | object | いいえ | コンテナー グループに関連付けられているユーザー ID のリスト。 ユーザー ID ディクショナリ キーの参照は、次の形式の Azure Resource Manager リソース ID になります: "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"。 |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  containers | array | はい | コンテナー グループ内のコンテナー。 - [Container オブジェクト](#Container) |
|  imageRegistryCredentials | array | いいえ | コンテナー グループの作成に使用されたイメージ レジストリの資格情報。 - [ImageRegistryCredential オブジェクト](#ImageRegistryCredential) |
|  restartPolicy | enum | いいえ | コンテナー グループ内のすべてのコンテナーの再起動ポリシー。 - `Always` 常に再起動する- `OnFailure` 障害時に再起動する- `Never` 再起動しない。 - Always、OnFailure、Never |
|  ipAddress | object | いいえ | コンテナー グループの IP アドレスの種類。 - [IpAddress オブジェクト](#IpAddress) |
|  osType | enum | はい | コンテナー グループ内のコンテナーで必要なオペレーティング システムの種類。 - Windows または Linux |
|  volumes | array | いいえ | このコンテナー グループのコンテナーによってマウントできるボリュームのリスト。 - [Volume オブジェクト](#Volume) |
|  診断 | object | いいえ | コンテナー グループの診断情報。 - [ContainerGroupDiagnostics オブジェクト](#ContainerGroupDiagnostics) |
|  networkProfile | object | いいえ | コンテナー グループのネットワーク プロファイル情報。 - [ContainerGroupNetworkProfile オブジェクト](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | いいえ | コンテナー グループの DNS 構成情報。 - [DnsConfiguration オブジェクト](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Container オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  名前 | string | はい | ユーザーが指定したコンテナー インスタンスの名前。 |
|  properties | object | はい | コンテナー インスタンスのプロパティ。 - [ContainerProperties オブジェクト](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  server | string | はい | "http" や "https" などのプロトコルを除いた Docker イメージ レジストリ サーバー。 |
|  username | string | はい | プライベート レジストリのユーザー名。 |
|  password | string | いいえ | プライベート レジストリのパスワード。 |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  ports | array | はい | コンテナー グループで公開されているポートのリスト。 - [Port オブジェクト](#Port) |
|  type | enum | はい | IP がパブリック インターネットまたはプライベート VNET に公開されているかどうかを指定します。 - Public または Private |
|  ip | string | いいえ | パブリック インターネットに公開されている IP。 |
|  dnsNameLabel | string | いいえ | IP の DNS 名ラベル。 |


<a id="Volume" />

### <a name="volume-object"></a>Volume オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  名前 | string | はい | ボリュームの名前。 |
|  azureFile | object | いいえ | Azure File ボリューム。 - [AzureFileVolume オブジェクト](#AzureFileVolume) |
|  emptyDir | object | いいえ | 空のディレクトリ ボリューム。 |
|  secret | object | いいえ | シークレット ボリューム。 |
|  gitRepo | object | いいえ | Git リポジトリ ボリューム。 - [GitRepoVolume オブジェクト](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | いいえ | コンテナー グループの Log Analytics 情報。 - [LogAnalytics オブジェクト](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  id | string | はい | ネットワーク プロファイルの識別子。 |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  nameServers | array | はい | コンテナー グループの DNS サーバー。 - string |
|  searchDomains | string | いいえ | コンテナー グループ内のホスト名参照用の DNS 検索ドメイン。 |
|  options | string | いいえ | コンテナー グループの DNS オプション。 |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  image | string | はい | コンテナー インスタンスの作成に使用されるイメージの名前。 |
|  command | array | いいえ | コンテナー インスタンス内で実行する exec 形式のコマンド。 - string |
|  ports | array | いいえ | コンテナー インスタンスで公開されているポート。 - [ContainerPort オブジェクト](#ContainerPort) |
|  environmentVariables | array | いいえ | コンテナー インスタンス内で設定する環境変数。 - [EnvironmentVariable オブジェクト](#EnvironmentVariable) |
|  resources | object | はい | コンテナー インスタンスのリソース要件。 - [ResourceRequirements オブジェクト](#ResourceRequirements) |
|  volumeMounts | array | いいえ | コンテナー インスタンスで使用可能なボリューム マウント。 - [VolumeMount オブジェクト](#VolumeMount) |
|  livenessProbe | object | いいえ | liveness probe。 - [ContainerProbe オブジェクト](#ContainerProbe) |
|  readinessProbe | object | いいえ | readiness probe。 - [ContainerProbe オブジェクト](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | いいえ | ポートに関連付けられているプロトコル。 - TCP または UDP |
|  port | integer | はい | ポート番号。 |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  shareName | string | はい | ボリュームとしてマウントされる Azure ファイル共有の名前。 |
|  readOnly | ブール値 | いいえ | ボリュームとしてマウントされている Azure ファイル共有が読み取り専用かどうかを示すフラグ。 |
|  storageAccountName | string | はい | Azure ファイル共有が含まれているストレージ アカウントの名前。 |
|  storageAccountKey | string | いいえ | Azure ファイル共有にアクセスするために使用されるストレージ アカウント アクセス キー。 |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  ディレクトリ | string | いいえ | ターゲット ディレクトリの名前。 ".." が含まれていたり、".." で始まっていたりすることはできません。  "." を指定した場合、ボリューム ディレクトリは Git リポジトリになります。  それ以外の場合、指定すると、ボリュームの指定された名前のサブディレクトリに Git リポジトリが含まれます。 |
|  repository | string | はい | リポジトリの URL |
|  revision | string | いいえ | 指定されたリビジョンのコミット ハッシュ。 |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | はい | Log Analytics のワークスペース ID |
|  workspaceKey | string | はい | Log Analytics のワークスペース キー |
|  logType | enum | いいえ | 使用するログの種類。 - ContainerInsights または ContainerInstanceLogs |
|  metadata | object | いいえ | Log Analytics のメタデータ。 |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | いいえ | ポートに関連付けられているプロトコル。 - TCP または UDP |
|  port | integer | はい | コンテナー グループ内で公開されているポート番号。 |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  名前 | string | はい | 環境変数の名前。 |
|  value | string | いいえ | 環境変数の値。 |
|  secureValue | string | いいえ | セキュリティで保護された環境変数の値。 |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  requests | object | はい | このコンテナー インスタンスのリソース要求。 - [ResourceRequests オブジェクト](#ResourceRequests) |
|  制限 | object | いいえ | このコンテナー インスタンスのリソース制限。 - [ResourceLimits オブジェクト](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  名前 | string | はい | ボリューム マウントの名前。 |
|  mountPath | string | はい | ボリュームをマウントする必要があるコンテナー内のパス。 コロン (:) を含めることはできません。 |
|  readOnly | ブール値 | いいえ | ボリューム マウントが読み取り専用かどうかを示すフラグ。 |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  exec | object | いいえ | プローブする実行コマンド - [ContainerExec オブジェクト](#ContainerExec) |
|  httpGet | object | いいえ | プローブする Http Get の設定 - [ContainerHttpGet オブジェクト](#ContainerHttpGet) |
|  initialDelaySeconds | integer | いいえ | 初期遅延秒数。 |
|  periodSeconds | integer | いいえ | 期間の秒数。 |
|  failureThreshold | integer | いいえ | 失敗のしきい値。 |
|  successThreshold | integer | いいえ | 成功のしきい値。 |
|  timeoutSeconds | integer | いいえ | タイムアウト秒数。 |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | はい | このコンテナー インスタンスのメモリ要求 (GB 単位)。 |
|  cpu | number | はい | このコンテナー インスタンスの CPU 要求。 |
|  gpu | object | いいえ | このコンテナー インスタンスの GPU 要求。 - [GpuResource オブジェクト](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | いいえ | このコンテナー インスタンスのメモリ制限 (GB 単位)。 |
|  cpu | number | いいえ | このコンテナー インスタンスの CPU 制限。 |
|  gpu | object | いいえ | このコンテナー インスタンスの GPU 制限。 - [GpuResource オブジェクト](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  command | array | いいえ | コンテナー内で実行するコマンド。 - string |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  path | string | いいえ | プローブするパス。 |
|  port | integer | はい | プローブするポート番号。 |
|  scheme | enum | いいえ | スキーム。 - http または https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource オブジェクト

|  名前 | 種類 | 必須 | 値 |
|  ---- | ---- | ---- | ---- |
|  count | integer | はい | GPU リソースの数。 |
|  sku | enum | はい | GPU リソースの SKU。 - K80、P100、V100 |


## <a name="next-steps"></a>次の手順

チュートリアル「[YAML ファイルを使用して複数コンテナー グループをデプロイする](container-instances-multi-container-yaml.md)」を参照してください。

[仮想ネットワーク](container-instances-vnet.md)内のコンテナー グループ、または[外部ボリュームをマウントする](container-instances-volume-azure-files.md)コンテナー グループをデプロイするための YAML ファイルの使用例を参照してください。

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

