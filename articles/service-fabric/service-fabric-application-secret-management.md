---
title: Azure Service Fabric のアプリケーション シークレットを管理する
description: Service Fabric アプリケーションでシークレット値をセキュリティで保護する方法 (プラットフォーム不可知) について説明します。
ms.topic: conceptual
ms.date: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a11869c3b606ed9e74ce4f598109139fa1bb4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89012825"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Service Fabric アプリケーションで暗号化されたシークレットを管理する
このガイドでは、Service Fabric アプリケーションでシークレットを管理する手順について説明します。 シークレットは、ストレージ接続文字列、パスワード、プレーン テキストで処理できないその他の値など、機密情報である可能性があります。

Service Fabric アプリケーションで暗号化されたシークレットを使用するには、3 つの手順が必要です。
* 暗号化証明書を設定してシークレットを暗号化する。
* アプリケーションで暗号化されたシークレットを指定する。
* サービス コードから暗号化されたシークレットを復号化する。

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>暗号化証明書を設定してシークレットを暗号化する
暗号化証明書を設定し、それを使ってシークレットを暗号化する方法は、Windows と Linux とで異なります。
* [Windows クラスターでの暗号化証明書の設定とシークレットの暗号化。][secret-management-windows-specific-link]
* [Linux クラスターでの暗号化証明書の設定とシークレットの暗号化。][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>アプリケーションで暗号化されたシークレットを指定する
前の手順では、証明書を使用してシークレットを暗号化し、アプリケーションで使用するための base-64 でエンコードされた文字列を生成する方法について説明しました。 この base-64 でエンコードされた文字列は、サービスの Settings.xml 内で暗号化された[パラメーター][parameters-link]として、またはサービスの ServiceManifest.xml 内で暗号化された[環境変数][environment-variables-link]として指定できます。

サービスの Settings.xml 構成ファイルで、`IsEncrypted` 属性を `true` に設定して、暗号化された[パラメーター][parameters-link]を指定します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
サービスの ServiceManifest.xml ファイルで、`Type` 属性を `Encrypted` に設定して、暗号化された[環境変数][environment-variables-link]を指定します。
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

アプリケーション マニフェストに証明書を指定することで、シークレットを Service Fabric アプリケーションに含めることも必要です。 **SecretsCertificate** 要素を **ApplicationManifest.xml** に追加して、目的の証明書の拇印を含めます。

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> SecretsCertificate を指定するアプリケーションをアクティブ化すると、Service Fabric によって一致する証明書が検索され、アプリケーションを完全なアクセス許可で実行している ID が証明書の秘密キーに付与されます。 また Service Fabric によって証明書の変更が監視され、必要に応じてアクセス許可が再適用されます。 共通名で宣言された証明書の変更を検出するために、Service Fabric では、一致するすべての証明書を検索し、それをキャッシュされたサムプリント一覧と比較する定期的なタスクが実行されます。 新しいサムプリントが検出された場合は、そのサブジェクトによって証明書が更新されたことを意味します。 タスクは、クラスターの各ノード上で 1 分ごとに 1 回実行されます。
>
> SecretsCertificate ではサブジェクトベースの宣言が許可されますが、暗号化された設定は、クライアント上で設定の暗号化に使用されたキー ペアに関連付けられていることに注意してください。 元の暗号化証明書 (または同等のもの) がサブジェクトベースの宣言と一致することと、アプリケーションをホストする可能性のあるクラスターのすべてのノードに、対応する秘密キーも含めて、その証明書がインストールされていることを確認する必要があります。 サブジェクトベースの宣言に一致し、元の暗号化証明書と同じキー ペアから作成されたすべての有効期限内の証明書は、同等のものと見なされます。
>

### <a name="inject-application-secrets-into-application-instances"></a>アプリケーション インスタンスへのアプリケーション シークレットの挿入
さまざまな環境へのデプロイは、できるだけ自動化するのが理想的です。 これは、ビルド環境でシークレットの暗号化を実行し、アプリケーション インスタンスの作成時に、暗号化されたシークレットをパラメーターとして指定することで実現できます。

#### <a name="use-overridable-parameters-in-settingsxml"></a>Settings.xml でのオーバーライド可能なパラメーターの使用
Settings.xml 構成ファイルでは、アプリケーションの作成時に指定できるオーバーライド可能なパラメーターを使用できます。 パラメーターの値を指定する代わりに、 `MustOverride` 属性を使用します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Settings.xml 内の値をオーバーライドするには、ApplicationManifest.xml でサービスのオーバーライド パラメーターを宣言します。

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

これで、アプリケーション インスタンスの作成時に、" *アプリケーション パラメーター* " として値を指定できるようになります。 ビルド プロセスでの統合を容易にするために、アプリケーション インスタンスの作成は、PowerShell を使用してスクリプト化することも、C# で記述することもできます。

PowerShell を使用する場合、パラメーターを[ハッシュ テーブル](/previous-versions/windows/it-pro/windows-powershell-1.0/ee692803(v=technet.10))として `New-ServiceFabricApplication` コマンドに指定します。

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

C# を使用する場合、アプリケーション パラメーターを `NameValueCollection` として `ApplicationDescription` に指定します。

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>サービス コードから暗号化されたシークレットを復号化する
[パラメーター][parameters-link]と[環境変数][environment-variables-link]にアクセスする API を使用すると、暗号化された値を簡単に復号化できます。 暗号化された文字列には、暗号化に使用された証明書に関する情報が含まれているので、証明書を手動で指定する必要はありません。 必要なのは、サービスが実行されているノードに証明書をインストールすることだけです。

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>次のステップ
* Service Fabric [シークレット ストア](service-fabric-application-secret-store.md) 
* アプリケーション マニフェストとサービス セキュリティの詳細については、[こちら](service-fabric-application-and-service-security.md)をご覧ください。

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md
