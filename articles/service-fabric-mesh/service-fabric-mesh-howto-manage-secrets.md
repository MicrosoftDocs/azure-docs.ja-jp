---
title: Azure Service Fabric Mesh アプリケーションのシークレットを管理する | Microsoft Docs
description: アプリケーション シークレットを管理して、Service Fabric Mesh アプリケーションの作成やデプロイを安全に行えるようにします。
services: service-fabric-mesh
keywords: secrets
author: aljo-microsoft
ms.author: aljo
ms.date: 4/2/2019
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: c2548ea3cf892ebe1a56cbb0909bfa5d5e805acf
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503304"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Azure Service Fabric Mesh アプリケーションのシークレットを管理する
Service Fabric Mesh では、Azure リソースとしてシークレットがサポートされています。 Service Fabric Mesh のシークレットには、格納や送信を安全に行う必要がある、機密性の高いテキスト情報を指定できます (ストレージ接続文字列やパスワードなど)。 この記事では、Service Fabric Secure Store Service を使用して、シークレットをデプロイし、保持する方法について説明します。

Mesh アプリケーションのシークレットは、次の要素で構成されます。
* **シークレット** リソース: シークレットのテキストを格納するコンテナーです。 **シークレット** リソース内に含まれている機密情報は、安全に格納、送信されます。
* 1 つ以上の**シークレット/値**リソース: **シークレット** リソース コンテナーに格納されます。 各**シークレット/値**リソースは、バージョン番号によって区別されます。 **シークレット/値**リソースのバージョンを変更することはできません (新しいバージョンを追加することしかできません)。

シークレットの管理は、次の手順で行います。
1. kind の定義として inlinedValue、contentType の定義として SecretsStoreRef を使用し、Azure Resource Model の YAML または JSON ファイル内で Mesh の**シークレット**を宣言します。
2. **シークレット** リソース (手順 1. で宣言したもの) に格納される**シークレット/値**リソースを、Azure Resource Model の YAML または JSON ファイル内で宣言します。
3. Mesh のシークレット値を参照するように Mesh アプリケーションを変更します。
4. Mesh アプリケーションをデプロイするかローリング アップグレードして、シークレット値が使用されるようにします。
5. Azure の CLI コマンド "az" を使用して、Secure Store Service のライフ サイクルを管理します。

## <a name="declare-a-mesh-secrets-resource"></a>Mesh のシークレット リソースを宣言する
Mesh のシークレット リソースは、kind の定義として inlinedValue を使用して、Azure Resource Model の JSON または YAML ファイル内で宣言します。 Mesh のシークレット リソースでサポートされるのは、Secure Store Service をソースとするシークレットです。 
>
次に示すのは、Mesh のシークレット リソースを JSON ファイル内で宣言する方法の例です。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
次に示すのは、Mesh のシークレット リソースを YAML ファイル内で宣言する方法の例です。
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Mesh のシークレット/値リソースを宣言する
Mesh のシークレット/値リソースは、前の手順で定義した Mesh シークレット リソースに依存するリソースです。

"resources" セクションの "value:" フィールドと "name:" フィールドの関係について言うと、コロンで区切られた "name:" 文字列の 2 番目の部分は、シークレットのバージョン番号に使用されます。また、コロンの前の名前は、依存先の Mesh シークレット値と一致する必要があります。 たとえば、要素 ```name: mysecret:1.0``` では、1.0 がバージョン番号であり、名前 ```mysecret``` は以前に定義された ```"value": "mysecret"``` と一致する必要があります。

>
次に示すのは、Mesh のシークレット/値リソースを JSON ファイル内で宣言する方法の例です。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
次に示すのは、Mesh のシークレット/値リソースを YAML ファイル内で宣言する方法の例です。
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Mesh のシークレット値を参照するように Mesh アプリケーションを変更する
Service Fabric Mesh アプリケーションでは、Secure Store Service のシークレット値を使用するために、次の 2 つの文字列を認識する必要があります。
1. Microsoft.ServiceFabricMesh/Secrets.name。これにはファイルの名前が格納されます。シークレット値はプレーンテキストで格納されます。
2. Windows または Linux の環境変数 "Fabric_SettingPath"。この変数には、Secure Store Service のシークレット値が格納されたファイルにアクセスできるようになる場所へのディレクトリ パスが格納されます。 Windows でホストされた Mesh アプリケーションの場合は "C:\Settings"、Linux でホストされた Mesh アプリケーションの場合は "/var/settings" となります。

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Mesh アプリケーションをデプロイするかローリング アップグレードして、シークレット値が使用されるようにする
シークレットの作成やバージョン管理されたシークレット/値の作成は、リソース モデルが宣言されたデプロイでのみ実行できます。 これらのリソースを作成するには、**az mesh deployment** コマンドを使用して、リソース モデルの JSON または YAML ファイルを渡す必要があります。

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Secure Store Service のライフ サイクルを管理するための Azure CLI コマンド

### <a name="create-a-new-secrets-resource"></a>新しいシークレット リソースを作成する
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
**template-file** か **template-uri** のいずれかを渡します (両方ではありません)。

例:
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- az mesh deployment create --https:\//www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>シークレットを表示する
シークレットの説明が返されます (値ではありません)。
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>シークレットを削除する

- Mesh アプリケーションによって参照されている間は、シークレットを削除することはできません。
- シークレット リソースを削除すると、すべてのシークレット/リソース バージョンが削除されます。
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>サブスクリプション内のシークレットを一覧表示する
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>リソース グループ内のシークレットを一覧表示する
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>シークレットの全バージョンを一覧表示する
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>シークレットのバージョンの値を表示する
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>シークレットのバージョンの値を削除する
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>次の手順 
Service Fabric Mesh の詳細については、以下の概要ページを参照してください。
- [Service Fabric Mesh の概要](service-fabric-mesh-overview.md)
