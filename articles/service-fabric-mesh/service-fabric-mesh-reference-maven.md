---
title: Azure Service Fabric Mesh Maven のリファレンス | Microsoft Docs
description: Service Fabric Mesh の Maven プラグインの使用法のリファレンスが含まれています
services: service-fabric-mesh
keywords: maven、java、cli
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 08e842f5b91bd0ca5f8e8b2a7866f3f9a689ac28
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998987"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Service Fabric Mesh 用の Maven プラグイン

## <a name="prerequisites"></a>前提条件

- Java SDK
- Maven
- メッシュ拡張機能がある Azure CLI
- Service Fabric CLI

## <a name="goals"></a>目標

### `azure-sfmesh:init`
- `application.yaml` ファイルを含めた `appresources` フォルダーを含む `servicefabric` フォルダーを作成します。 

### `azure-sfmesh:addservice`
- サービス名を付けたフォルダーを `servicefabric` フォルダー内に作成し、サービスの YAML ファイルを作成します。 

### `azure-sfmesh:addnetwork`
- 指定したネットワーク名を付けた `network` YAML を `appresources` フォルダー内に生成します。 

### `azure-sfmesh:addgateway`
- 指定したゲートウェイ名を付けた `gateway` YAML を `appresources` フォルダー内に生成します。 

### `azure-sfmesh:addsecret`
- 指定したシークレット名を付けた `secret` YAML を `appresources` フォルダー内に生成します。 

### `azure-sfmesh:addsecretvalue`
- 指定したシークレットとシークレット値の名前を付けた `secretvalue` YAML を `appresources` フォルダー内に生成します。 

### `azure-sfmesh:deploy`
- `servicefabric` フォルダーから YAML をマージして、現在のフォルダー内に Azure Resource Manager テンプレート JSON を作成します。
- すべてのリソースを Azure Service Fabric Mesh 環境にデプロイします。 

### `azure-sfmesh:deploytocluster`
- Service Fabric クラスターに適用可能な YAML から生成されたデプロイ JSON を含むフォルダー (`meshDeploy`) を作成します。
- すべてのリソースを Service Fabric クラスターにデプロイします。
 

## <a name="usage"></a>使用法

Maven プラグインを Maven Java アプリで使用するには、次のスニペットを pom.xml ファイルに追加します。

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>一般的な構成

Maven プラグインでは現在、Azure 用の Maven プラグインの一般的な構成をサポートしていません。

## <a name="how-to"></a>操作方法

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Azure Service Fabric Mesh 用に Maven プロジェクトを初期化する
次のコマンドを実行して、アプリケーション リソース YAML ファイルを作成します。

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- `app_helloworldserver` という名前のアプリケーション YAML を含むルート フォルダーに、`servicefabric->appresources` という名前のフォルダーを作成します。

### <a name="add-resource-to-your-application"></a>アプリケーションにリソースを追加する

#### <a name="add-a-new-network-to-your-application"></a>新しいネットワークをアプリケーションに追加する
次のコマンドを実行して、ネットワーク リソース YAML を作成します。 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.4/22
```

- `network_helloworldservicenetwork` という名前のフォルダー `servicefabric->appresources` にネットワーク YAML を作成します。

#### <a name="add-a-new-service-to-your-application"></a>新しいサービスをアプリケーションに追加する
次のコマンドを実行して、サービス YAML を作成します。 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- `helloworldservicenetwork` と `helloworldserver` アプリを参照する `service_helloworldservice` という名前のフォルダー `servicefabric->helloworldservice` 内にサービス YAML を作成します。
- サービスはポート 8080 でリッスンします。
- サービスでは、***helloworldserver:latest*** がコンテナー イメージとして使用されます。

#### <a name="add-a-new-gateway-resource-to-your-application"></a>アプリケーションに新しいゲートウェイ リソースを追加する
次のコマンドを実行して、ゲートウェイ リソース YAML を作成します。 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- `gateway_helloworldgateway` という名前のフォルダー `servicefabric->appresources` に新しいゲートウェイ YAML を作成します。
- このゲートウェイからの呼び出しをリッスンしているサービス リスナーとして `helloworldservicelistener` を参照します。 また、`helloworldservice` をサービスとして、`helloworldservicenetwork` をネットワークとして、`helloworldserver` をアプリケーションとして参照します。 
- 要求をポート 80 でリッスンします。

#### <a name="add-a-new-volume-to-your-application"></a>新しいボリュームをアプリケーションに追加する
次のコマンドを実行して、ボリューム リソース YAML を作成します。 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- `volume_vol1` という名前のフォルダー `servicefabric->appresources` にボリューム YAML を作成します。
- 上記のように、必要なパラメーター、`volumeAccountKey`、および `volumeShareName` のプロパティを設定します。
- 作成したこのボリュームを参照する方法については、[Azure Files ボリュームを使用したアプリのデプロイ](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)に関するページをご覧ください。

#### <a name="add-a-new-secret-resource-to-your-application"></a>アプリケーションに新しいシークレット リソースを追加する
次のコマンドを実行して、シークレット リソース YAML を作成します。 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- `secret_secret1` という名前のフォルダー `servicefabric->appresources` にシークレット YAML を作成します。
- 作成したこのシークレットを参照する方法については、[シークレットの管理](service-fabric-mesh-howto-manage-secrets.md)に関するページをご覧ください。

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>アプリケーションに新しい secretvalue リソースを追加する
次のコマンドを実行して、secretvalue リソース YAML を作成します。 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- `secretvalue_secret1_v1` という名前のフォルダー `servicefabric->appresources` に secretvalue YAML を作成します。

### <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する

ゴール `azure-sfmesh:deploytocluster`を利用して、次のコマンドを使用してアプリケーションをローカルで実行することができます。

```cmd
mvn azure-sfmesh:deploytocluster
```

既定では、このゴールはローカル クラスターにリソースをデプロイします。 ローカル クラスターにデプロイする場合は、稼働しているローカルの Service Fabric クラスターがあることを前提としています。 リソース用のローカル Service Fabric クラスターは、現在、[Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md) でのみサポートされています。

- Service Fabric クラスターに適用可能な YAML から JSON を作成します。
- 次に、クラスター エンドポイントにデプロイします。

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Azure Service Fabric Mesh にアプリケーションをデプロイする

ゴール `azure-sfmesh:deploy`を利用して、次のコマンドを実行して Service Fabric Mesh 環境にデプロイすることができます。

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- `todoapprg` というリソース グループがない場合は、作成します。
- YAML をマージすることで、Azure Resource Manager テンプレート JSON を作成します。 
- JSON を Azure Service Fabric Mesh 環境にデプロイします。