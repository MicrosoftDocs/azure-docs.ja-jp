---
title: Azure Service Fabric - Service Fabric アプリケーションの KeyVault 参照を使用する
description: この記事では、アプリケーション シークレットでの Service Fabric KeyVaultReference サポートの使用方法について説明します。
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898598"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Azure にデプロイされた Service Fabric アプリケーションに対する KeyVaultReference サポート

クラウド アプリケーションを構築する際の一般的な課題は、アプリケーションにシークレットを安全に配布する方法です。 たとえば、パイプライン中やオペレーターにキーを公開せずに、データベース キーをアプリケーションにデプロイする必要がある場合があります。 Service Fabric の KeyVaultReference サポートにより、Key Vault に格納されているシークレットの URL を参照するだけで、簡単にシークレットをアプリケーションにデプロイできます。 Service Fabric では、アプリケーションのマネージド ID に代わってそのシークレットのフェッチを処理し、シークレットを使用してアプリケーションをアクティブ化します。

> [!NOTE]
> Service Fabric アプリケーションに対する KeyVaultReference サポートは、Service Fabric バージョン 7.2 CU5 から (プレビュー段階が終了して) 一般提供されています。 この機能を使用する前に、このバージョンにアップグレードすることをお勧めします。

> [!NOTE]
> Service Fabric アプリケーションに対する KeyVaultReference サポートでは、[バージョン管理された](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)シークレットのみがサポートされます。 バージョン管理されていないシークレットはサポートされていません。 Key Vault は、お使いの Service Fabric クラスターと同じサブスクリプションに含まれている必要があります。 

## <a name="prerequisites"></a>前提条件

- Service Fabric アプリケーションのマネージド ID

    Service Fabric の KeyVaultReference サポートでは、アプリケーションのマネージド ID を使用して、アプリケーションの代わりにシークレットがフェッチされます。したがって、アプリケーションにはマネージド ID が割り当てられており、それを介してデプロイされている必要があります。 こちらの[ドキュメント](concepts-managed-identity.md)に従って、アプリケーションのマネージド ID を有効にします。

- セントラル シークレット ストア (CSS)。

    セントラル シークレット ストア (CSS) は、Service Fabric の暗号化されたローカル シークレット キャッシュです。 この機能では、シークレットは Key Vault からフェッチされた後、CSS を使用して保護および永続化されます。 この機能を使用するには、この省略可能なシステム サービスを有効にする必要もあります。 こちらの[ドキュメント](service-fabric-application-secret-store.md)に従って、CSS を有効にして構成します。

- アプリケーションのマネージド ID に keyvault へのアクセス許可を付与する

    こちらの[ドキュメント](how-to-grant-access-other-resources.md)を参照して、マネージド ID に keyvault へのアクセス許可を付与する方法を確認します。 また、システム割り当てマネージド ID を使用している場合は、アプリケーションのデプロイ後にのみ、マネージド ID が作成されることに注意してください。 これにより、コンテナーへのアクセス権が ID に付与される前に、アプリケーションでシークレットへのアクセスが試行される競合状態が生じる場合があります。 システム割り当て ID の名前は `{cluster name}/{application name}/{service name}` です。
    
## <a name="use-keyvaultreferences-in-your-application"></a>アプリケーションで KeyVaultReferences を使用する
KeyVaultReferences はさまざまな方法で使用できます
- [環境変数として](#as-an-environment-variable)
- [コンテナーにファイルとしてマウントする](#mounted-as-a-file-into-your-container)
- [コンテナー リポジトリのパスワードへの参照として](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>環境変数として

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>コンテナーにファイルとしてマウントする

- 1 つのセクションを settings.xml に追加する

    `MySecret` パラメーターを型 `KeyVaultReference` および値 `<KeyVaultURL>` として定義します

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- `<ConfigPackagePolicies>` で ApplicationManifest.xml 内の新しいセクションを参照します

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- サービス コードからシークレットを使用します

    `<Section  Name=MySecrets>` 下に一覧表示される各パラメーターは、EnvironmentVariable SecretPath によって参照されるフォルダー下のファイルです。 次の C# のコード スニペットでは、アプリケーションから MySecret を読み取る方法を示しています。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > MountPoint で、シークレット値が含まれたファイルがマウントされるフォルダーが制御されます。

### <a name="as-a-reference-to-a-container-repository-password"></a>コンテナー リポジトリのパスワードへの参照として

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>次のステップ

* [Azure KeyVault ドキュメント](../key-vault/index.yml)
* [セントラル シークレット ストアについて](service-fabric-application-secret-store.md)
* [Service Fabric アプリケーションのマネージド ID について](concepts-managed-identity.md)
