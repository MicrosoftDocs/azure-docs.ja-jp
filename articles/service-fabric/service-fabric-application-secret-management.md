---
title: "Service Fabric アプリケーションでのシークレットの管理 | Microsoft Docs"
description: "この記事では、Service Fabric アプリケーションでシークレット値をセキュリティで保護する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: cc6ef8f3ef5371ac3092686afddc9198516916fd
ms.lasthandoff: 12/09/2016


---
# <a name="managing-secrets-in-service-fabric-applications"></a>Service Fabric アプリケーションでのシークレットの管理
このガイドでは、Service Fabric アプリケーションでシークレットを管理する手順について説明します。 シークレットは、ストレージ接続文字列、パスワード、プレーン テキストで処理できないその他の値など、機密情報である可能性があります。

このガイドでは、Azure Key Vault を使用してキーとシークレットを管理します。 ただし、アプリケーションでのシークレットの " *使用* " はクラウド プラットフォームに依存しないので、クラスターでホストされている任意の場所にアプリケーションをデプロイできます。 

## <a name="overview"></a>概要
サービスの構成設定を管理する方法として、[サービス構成パッケージ][config-package]を使用することが推奨されます。 構成パッケージはバージョン管理されており、正常性の検証と自動ロールバックを含む管理されたローリング アップグレードによって更新可能です。 グローバル サービスの停止の可能性が低減されるため、グローバル構成をお勧めします。 暗号化されたシークレットも例外ではありません。 Service Fabric には、証明書の暗号化を使用して、構成パッケージの Settings.xml ファイル内の値を暗号化および暗号化解除する機能が組み込まれています。

次の図は、Service Fabric アプリケーションでのシークレットの管理の基本的なフローを示しています。

![シークレットの管理の概要][overview]

このフローには、次の&4; つの主な手順があります。

1. データ暗号化証明書を取得します。
2. クラスターに証明書をインストールします。
3. アプリケーションをデプロイするときに、証明書を使用してシークレット値を暗号化し、サービスの Settings.xml 構成ファイルに挿入します。
4. 同じ暗号化証明書によって暗号化を解除して、Settings.xml から暗号化された値を読み取ります。 

ここでは、証明書の安全な格納場所として [Azure Key Vault][key-vault-get-started] を使用します。また、Azure の Service Fabric クラスターにインストールされている証明書を取得する方法としても使用します。 Azure にデプロイしない場合は、Service Fabric アプリケーションでのシークレットの管理に Key Vault を使用する必要はありません。

## <a name="data-encipherment-certificate"></a>データ暗号化証明書
データ暗号化証明書は、サービスの Settings.xml 内の構成値の暗号化と暗号化解除に厳密に使用されます。認証や暗号化テキストの署名には使用されません。 証明書は次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。
* 証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
* 証明書キーの使用法として、データ暗号化 (10) を指定する必要があります。サーバー認証やクライアント認証を指定することは使用できません。 
  
  たとえば、PowerShell を使用して自己署名証明書を作成するときは、`KeyUsage` フラグを `DataEncipherment` に設定する必要があります。
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>クラスターへの証明書のインストール
この証明書は、クラスターの各ノードにインストールする必要があります。 実行時に、この証明書を使用して、サービスの Settings.xml に保存された値の暗号化が解除されます。 セットアップ手順については、[Azure Resource Manager を使用してクラスターを作成する方法][service-fabric-cluster-creation-via-arm]に関する記事をご覧ください。 

## <a name="encrypt-application-secrets"></a>アプリケーション シークレットの暗号化
Service Fabric SDK には、シークレットの暗号化と暗号化解除の機能が組み込まれています。 シークレット値は作成時に暗号化し、その後、暗号化解除して、サービス コードでプログラムによって読み取ることができます。 

次の PowerShell コマンドを使用して、シークレットを暗号化します。 このコマンドは値を暗号化するだけであり、暗号化テキストの署名は**行いません**。 クラスターにインストールされている同じ暗号化証明書を使用して、シークレット値の暗号化テキストを生成する必要があります。

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

生成された Base-64 文字列には、シークレットの暗号化テキストと、暗号化に使用された証明書に関する情報が含まれます。  Base&64; でエンコードされた文字列は、サービスの Settings.xml 構成ファイル内の、`IsEncrypted` 属性が `true` に設定されたパラメーターに挿入できます。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>アプリケーション インスタンスへのアプリケーション シークレットの挿入
さまざまな環境へのデプロイは、できるだけ自動化するのが理想的です。 これは、ビルド環境でシークレットの暗号化を実行し、アプリケーション インスタンスの作成時に、暗号化されたシークレットをパラメーターとして指定することで実現できます。

#### <a name="use-overridable-parameters-in-settingsxml"></a>Settings.xml でのオーバーライド可能なパラメーターの使用
Settings.xml 構成ファイルでは、アプリケーションの作成時に指定できるオーバーライド可能なパラメーターを使用できます。 パラメーターの値を指定する代わりに、 `MustOverride` 属性を使用します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

PowerShell を使用する場合、パラメーターを[ハッシュ テーブル](https://technet.microsoft.com/library/ee692803.aspx)として `New-ServiceFabricApplication` コマンドに指定します。

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
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

## <a name="decrypt-secrets-from-service-code"></a>サービス コードからのシークレットの暗号化解除
Service Fabric のサービスは、既定で Windows のネットワーク サービスで実行されます。ノードにインストールされた証明書にアクセスするには、追加のセットアップが必要になります。

データ暗号化証明書を使用する場合、ネットワーク サービスまたはこのサービスが実行されているユーザー アカウントに証明書の秘密キーへのアクセス権があることを確認する必要があります。 Service Fabric では、サービスのアクセス権の付与を自動的に処理します (Service Fabric をそのように構成している場合)。 この構成を行うには、ApplicationManifest.xml で証明書のユーザーとセキュリティ ポリシーを定義します。 次の例では、ネットワーク サービス アカウントに、拇印によって定義された証明書への読み取りアクセス権が付与されます。

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> Windows の証明書ストア スナップインから証明書の拇印をコピーすると、不可視文字が拇印文字列の先頭に配置されます。 証明書を拇印で見つけようとしたときに、この不可視文字によってエラーが発生する可能性があるため、この余分な文字を必ず削除してください。
> 
> 

### <a name="use-application-secrets-in-service-code"></a>サービス コードでのアプリケーション シークレットの使用
構成パッケージの Settings.xml の構成値にアクセスするための API を使用すると、`IsEncrypted` 属性が `true` に設定された値の暗号化を簡単に解除できます。 暗号化されたテキストには、暗号化に使用された証明書に関する情報が含まれているので、証明書を手動で見つける必要はありません。 必要なのは、サービスが実行されているノードに証明書をインストールすることだけです。 元のシークレット値を取得するには、 `DecryptValue()` メソッドを呼び出します。

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>次のステップ
[さまざまなセキュリティ アクセス許可でアプリケーションを実行する方法](service-fabric-application-runas-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-model.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png

