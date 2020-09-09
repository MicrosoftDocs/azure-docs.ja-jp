---
title: Azure Service Fabric - Service Fabric アプリケーションの KeyVault 参照を使用する
description: この記事では、アプリケーション シークレットでの Service Fabric KeyVaultReference サポートの使用方法について説明します。
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f1ac3ac50c5ac7cbabb03561c5db7f9c14150de4
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246165"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Service Fabric アプリケーションでの KeyVaultReference サポート (プレビュー)

クラウド アプリケーションを構築するときの一般的な課題は、アプリケーションによって必要とされるシークレットを安全に格納する方法です。 たとえば、コンテナー リポジトリの資格情報を keyvault に格納し、アプリケーション マニフェスト上でそれを参照する場合があります。 Service Fabric KeyVaultReference では、Service Fabric マネージド IDを使用し、keyvault シークレットの参照を容易にします。 この記事では以降、Service Fabric KeyVaultReference の使用方法について詳しく説明するとともに、一般的な使用方法をいくつか紹介します。

## <a name="prerequisites"></a>前提条件

- アプリケーションでのマネージド ID (MIT)
    
    Service Fabric KeyVaultReference サポートでは、アプリケーションのマネージド ID を使用するため、KeyVaultReferences の使用を計画しているアプリケーションでは必然的にマネージド ID を使用することになります。 こちらの[ドキュメント](concepts-managed-identity.md)に従って、アプリケーションのマネージド ID を有効にします。

- セントラル シークレット ストア (CSS)。

    セントラル シークレット ストア (CSS) は、Service Fabric の暗号化されたローカル シークレット キャッシュです。 CSS は、パスワード、トークン、キーなどの機密データを暗号化してメモリに保持するローカル シークレット ストア キャッシュです。 KeyVaultReference は、一度フェッチされると、CSS にキャッシュされます。

    `fabricSettings` 下のご自身のクラスター構成に以下を追加して、KeyVaultReference サポートでの必要なすべての機能を有効化します。

    ```json
    "fabricSettings": 
    [
        ...
    {
                "name":  "CentralSecretService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > CSS には別の暗号化証明書を使用することをお勧めします。 "CentralSecretService" セクション下に追加することができます。
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
また、変更を有効にするには、アップグレード ポリシーを変更し、クラスターでアップグレードが進行するのに合わせて各ノードで Service Fabric ランタイムを強制的に再起動するよう指定する必要があります。 この再起動により、新たに有効になったシステム サービスが各ノードで確実に開始および実行されます。 次のスニペットでは、forceRestart が必須の設定です。残りの設定には既存の値を使用します。
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- アプリケーションのマネージド ID に keyvault へのアクセス許可を付与する

    こちらの[ドキュメント](how-to-grant-access-other-resources.md)を参照して、マネージド ID に keyvault へのアクセス許可を付与する方法を確認します。 また、システム割り当てマネージド ID を使用している場合、アプリケーションのデプロイ後にのみ、マネージド ID が作成されます。

## <a name="keyvault-secret-as-application-parameter"></a>アプリケーション パラメーターとしての keyvault シークレット
たとえば、アプリケーションでは、keyvault 内に格納されたバックエンド データベースのパスワードを読み取る必要があり、Service Fabric KeyVaultReference のサポートによって、それを簡単に行えるようになります。 次の例では、Service Fabric KeyVaultReference のサポートを使用して、keyvault から `DBPassword` シークレットを読み取ります。

- 1 つのセクションを settings.xml に追加する

    `DBPassword` パラメーターを型 `KeyVaultReference` および値 `<KeyVaultURL>` として定義します

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- `<ConfigPackagePolicies>` で ApplicationManifest.xml 内の新しいセクションを参照します

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- ご自身のアプリケーション上で KeyVaultReference を使用します

    サービス インスタンス化における Service Fabric では、アプリケーションのマネージド ID を使用して KeyVaultReference パラメーターを解決します。 `<Section  Name=dbsecrets>` 下に一覧表示される各パラメーターは、EnvironmentVariable SecretPath によって参照されるフォルダー下のファイルです。 次の C# のコード スニペットでは、アプリケーション上で DBPassword を読み取る方法を示しています。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > コンテナーのシナリオでは、MountPoint を使用して、`secrets` がマウントされる場所を制御できます。

## <a name="keyvault-secret-as-environment-variable"></a>環境変数としての keyvault シークレット

Service Fabric 環境変数では、KeyVaultReference 型がサポートされるようになりました。次の例では、KeyVault に格納されているシークレットに環境変数をバインドする方法を示しています。

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>コンテナー リポジトリ パスワードとしての keyvault シークレット
KeyVaultReference は、コンテナー RepositoryCredentials でサポートされている型です。次の例では、keyvault 参照をコンテナー リポジトリ パスワードとして使用する方法を示しています。
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>よく寄せられる質問
- KeyVaultReference サポートに対して、マネージド ID が有効になっている必要があります。マネージド ID を有効にせずに KeyVaultReference を使用すると、アプリケーションのアクティブ化に失敗します。

- システム割り当て ID を使用している場合は、アプリケーションがデプロイされた後にのみ作成され、これによって循環依存関係が生成されます。 アプリケーションがデプロイされたら、システム割り当て ID に keyvault へのアクセス許可を付与できます。 名前 {cluster}/{application name}/{servicename} によって、システム割り当て ID を検索できます

- keyvault はご自身の Service Fabric クラスターと同じサブスクリプション内に置かれている必要があります。 

## <a name="next-steps"></a>次のステップ

* [Azure KeyVault ドキュメント](../key-vault/index.yml)
