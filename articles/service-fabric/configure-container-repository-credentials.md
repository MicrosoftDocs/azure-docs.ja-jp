---
title: Azure Service Fabric - コンテナー リポジトリの資格情報を構成する
description: コンテナー レジストリからイメージをダウンロードするためのリポジトリ資格情報を構成します
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0c6421fed88a3909db717c13a6b3faf51c4491cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574821"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>アプリケーションでコンテナー イメージをダウンロードするためのリポジトリ資格情報を構成する

コンテナー レジストリの認証は、アプリケーション マニフェストの `ContainerHostPolicies` セクションに `RepositoryCredentials` を追加することによって構成します。 コンテナー レジストリ (下の例の *myregistry.azurecr.io*) のアカウントとパスワードを追加することで、サービスがコンテナー イメージをリポジトリからダウンロードできるようになります。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

リポジトリのパスワードは、クラスターのすべてのノードにデプロイされる暗号化証明書を使用して暗号化することをお勧めします。 Service Fabric がサービス パッケージをクラスターにデプロイするときに、その暗号化証明書を使って暗号テキストが解読されます。 Invoke-ServiceFabricEncryptText コマンドレットを使ってパスワードの暗号テキストを作成し、ApplicationManifest.xml ファイルに追加してください。
証明書と暗号化セマンティクスの詳細については、[シークレットの管理](service-fabric-application-secret-management.md)に関するページを参照してください。

## <a name="configure-cluster-wide-credentials"></a>クラスター全体の資格情報を構成する

Service Fabric では、アプリケーションから既定のリポジトリ資格情報として使用できる、クラスター全体の資格情報を構成できます。

この機能は、`true` または `false` の値を指定した `UseDefaultRepositoryCredentials` 属性を ApplicationManifest.xml の `ContainerHostPolicies` に追加することで、有効または無効にすることができます。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric では、`Hosting` セクションの ClusterManifest に指定できる、既定のリポジトリ資格情報が使用されます。  `UseDefaultRepositoryCredentials` が `true` の場合、Service Fabric で ClusterManifest から次の値が読み取られます。

* DefaultContainerRepositoryAccountName (文字列)
* DefaultContainerRepositoryPassword (文字列)
* IsDefaultContainerRepositoryPasswordEncrypted (ブール値)
* DefaultContainerRepositoryPasswordType (文字列)

ClusterManifestTemplate.json ファイルの `Hosting` セクション内に追加できる内容の例を次に示します。 `Hosting` セクションは、クラスターの作成時、または構成をアップグレードするときに後で追加できます。 詳細については、[Service Fabric クラスター設定の変更](service-fabric-cluster-fabric-settings.md)と [Service Fabric アプリケーションのシークレット管理](service-fabric-application-secret-management.md)に関するページを参照してください。

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>トークンをレジストリ資格情報として使用する

Service Fabric では、お使いのコンテナーのイメージをダウンロードするための資格情報としてのトークンの使用をサポートしています。  この機能は、基になる仮想マシン スケール セットの *マネージド ID* を活用してレジストリに対する認証を行うため、ユーザー資格情報を管理する必要がなくなります。  詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。  この機能を使用するには、次の手順を実行する必要があります。

1. VM に対して *システム割り当てマネージド ID* が有効になっていることを確認します。

    ![Azure portal:仮想マシン スケール セット ID オプションを作成する](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> ユーザー割り当てマネージド ID の場合は、この手順をスキップします。 下の残りの手順は、スケール セットが単一のユーザー割り当てマネージド ID にのみ関連付けられている限り、同じように機能します。

2. レジストリからイメージをプルするアクセス許可または読み取るアクセス許可を仮想マシンスケール セットに付与します。 Azure portal 内の Azure コンテナー レジストリの [Access Control (IAM)] ブレードで、仮想マシンに対する "*ロールの割り当て*" を追加します。

    ![VM プリンシパルを ACR に追加する](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. 次に、ご使用のアプリケーション マニフェストを選択します。 `ContainerHostPolicies` セクションに、`‘UseTokenAuthenticationCredentials=”true”` 属性を追加します。

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > `UseTokenAuthenticationCredentials` を true にし、さらにフラグ `UseDefaultRepositoryCredentials` を true に設定すると、デプロイ中にエラーが発生します。

## <a name="next-steps"></a>次のステップ

* [コンテナー レジストリ認証](../container-registry/container-registry-authentication.md)の詳細を確認します。
