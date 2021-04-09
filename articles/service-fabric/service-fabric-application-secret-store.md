---
title: Azure Service Fabric セントラル シークレット ストア
description: この記事では、Azure Service Fabric のセントラル シークレット ストアを使用する方法について説明します。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: e9fd435803ad5354b0eb2d4f5de50009a8cbbfe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88869757"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric のセントラル シークレット ストア 
この記事では、Azure Service Fabric のセントラル シークレットストア (CSS) を使用して Service Fabric アプリケーションでシークレットを作成する方法について説明します。 CSS は、パスワード、トークン、キーなどの機密データを暗号化してメモリに保持するローカル シークレット ストア キャッシュです。

  > [!NOTE] 
  > SF version 7.1.CU3 の前に初めて CSS をアクティブ化するときに、 アクティブ化が失敗し、CSS が永続的に異常な状態のままになる可能性があります。これは、CSS が Windows 認証クラスターでアクティブ化される場合、CSS が任意のクラスターでアクティブ化されるものの `EncryptionCertificateThumbprint` が正しく宣言されていない場合、または対応する証明書がインストールされていないかノードで ACL が指定されていない場合に発生します。 Windows 認証クラスターについては、先に進む前に 7\.1.CU3 にアクセスしてください。 その他のクラスターについては、これらのインバリアントを再確認するか、7.1. CU3 にアクセスしてください。
  
## <a name="enable-central-secrets-store"></a>セントラル シークレット ストアを有効にする
`fabricSettings` の下でクラスター構成に次のスクリプトを追加して、CSS を有効にします。 CSS には、クラスター証明書以外の証明書を使用することをお勧めします。 すべてのノードに暗号化証明書がインストールされていること、および `NetworkService` が証明書の秘密キーの読み取りアクセス許可を持っていることを確認してください。
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
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>シークレット リソースを宣言する
REST API を使用して、シークレット リソースを作成できます。
  > [!NOTE] 
  > クラスターで Windows 認証を使用している場合、REST 要求はセキュリティで保護されていない HTTP チャネルを経由して送信されます。 セキュリティで保護されたエンドポイントを持つ X509 ベースのクラスターを使用することをお勧めします。

REST API を使用して `supersecret` シークレット リソースを作成するために、`https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` に PUT 要求を出します。 シークレット リソースを作成するには、クラスター証明書または管理者クライアント証明書が必要です。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>シークレット値を設定する

REST API を使用してシークレット値を設定するには、次のスクリプトを使用します。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>シークレット値を確認する
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>アプリケーションでのシークレットの使用

Service Fabric アプリケーションでシークレットを使用するには、次の手順に従います。

1. **settings.xml** ファイルに次のスニペットを含むセクションを追加します。 ここでは、値が {`secretname:version`} の形式であることに注意してください。

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. **ApplicationManifest.xml** にこのセクションをインポートします。
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   環境変数 `SecretPath` は、すべてのシークレットが格納されているディレクトリを指します。 `testsecrets` セクションの下に一覧表示される各パラメーターは、別のファイルに格納されます。 アプリケーションは、次のようにシークレットを使用できるようになりました。
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. コンテナーにシークレットをマウントします。 コンテナー内でシークレットを使用できるようにするために必要な変更は、`<ConfigPackage>` にマウント ポイントを `specify` することだけです。
次のスニペットは、変更された **ApplicationManifest.xml** です。  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   シークレットは、コンテナー内のマウント ポイントで使用可能です。

1. `Type='SecretsStoreRef` を指定することにより、プロセス環境変数にシークレットをバインドできます。 次のスニペットは、**ServiceManifest.xml** で `supersecret` バージョン `ver1` を環境変数 `MySuperSecret` にバインドする方法の例です。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>次のステップ
アプリケーション マニフェストとサービス セキュリティの詳細については、[こちら](service-fabric-application-and-service-security.md)をご覧ください。
