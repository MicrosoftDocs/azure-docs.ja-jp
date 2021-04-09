---
title: Linux 上のアプリケーション用の証明書を構成する
description: Linux クラスター上の Service Fabric ランタイムを使用するアプリ用に証明書を構成します
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 70f9cc38d84681f68c10882889214648a4dd2624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785568"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Linux クラスター上の証明書とセキュリティ

この記事では、Linux クラスター上での X.509 証明書の構成に関する情報を提供します。

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Linux ノード上の X.509 証明書の場所と形式

Service Fabric では通常、X.509 証明書が Linux クラスター ノード上の */var/lib/sfcerts* ディレクトリに存在するものと想定されます。 この前提は、クラスター証明書、クライアント証明書などに適用されます。場合によっては、証明書用に *var/lib/sfcerts* フォルダー以外の場所を指定することもできます。 たとえば、Service Fabric Java SDK を使用して作成された Reliable Services では、構成パッケージ (Settings.xml) を通じて、一部のアプリケーション固有の証明書用に別の場所を指定できます。 詳しくは、「[構成パッケージ (Settings.xml) で参照される証明書](#certificates-referenced-in-the-configuration-package-settingsxml)」をご覧ください。

Linux クラスターの場合、Service Fabric が証明書として受け付けるのは、証明書と秘密キーの両方を格納した .pem ファイルか、証明書を格納した .crt ファイルと秘密キーを格納した .key ファイルのセットです。 すべてのファイルを PEM 形式にする必要があります。 

[Resource Manager テンプレート](./service-fabric-cluster-creation-create-template.md)または [PowerShell](/powershell/module/az.servicefabric/) コマンドを使って Azure Key Vault から証明書をインストールした場合、証明書は各ノードの */var/lib/sfcerts* ディレクトリに正しい形式でインストールされます。 その他の方法で証明書をインストールした場合は、クラスター ノード上で証明書が正しくインストールされていることを確認する必要があります。

## <a name="certificates-referenced-in-the-application-manifest"></a>アプリケーション マニフェストで参照される証明書

[**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) 要素や [**EndpointCertificate**](./service-fabric-service-model-schema-elements.md#endpointcertificate-element) 要素を使用してアプリケーション マニフェスト内に指定された証明書は、 */var/lib/sfcerts* ディレクトリに置かれている必要があります。 アプリケーション マニフェスト内で証明書を指定するために使用される要素はパス属性を取らないため、証明書は既定のディレクトリに置かれる必要があります。 これらの要素は、オプションの **X509StoreName** 属性を取ります。 既定値は "My" です。これは、Linux ノード上の */var/lib/sfcerts* ディレクトリを指します。 その他の値は、Linux クラスター上では定義されません。 Linux クラスター上で実行されるアプリについては、**X509StoreName** 属性を省略することをお勧めします。 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>構成パッケージ (Settings.xml) で参照される証明書

一部のサービスについては、[ConfigPackage](./service-fabric-application-and-service-manifests.md) (既定では Settings.xml) で X.509 証明書を構成することができます。 これを行うのは、たとえば、Service Fabric .NET Core または Java SDK を使用して作成された Reliable Services サービスの RPC チャネルを保護するための証明書を宣言する場合などです。 構成パッケージで証明書を参照する方法は 2 つあります。 サポートされる方法は、.NET Core と Java SDK の間で異なります。

### <a name="using-x509-securitycredentialstype"></a>X509 SecurityCredentialsType の使用

.NET と Java SDK では、**SecurityCredentialsType** に対して **X509** を指定できます。 これは、`SecurityCredentials` ([.NET](/previous-versions/azure/reference/mt124894(v=azure.100))/[Java](/java/api/system.fabric.securitycredentials)) の `X509Credentials` ([.NET](/previous-versions/azure/reference/mt124925(v=azure.100))/[Java](/java/api/system.fabric.x509credentials)) タイプに対応します。

**X509** 参照では、証明書ストア内の証明書の場所が特定されます。 次の XML は、証明書の場所を指定するためのパラメーターを示したものです。

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Linux 上で実行されるサービスの場合、**LocalMachine**/**My** は、証明書の既定の場所 ( */var/lib/sfcerts* ディレクトリ) を指します。 Linux の場合、**CertificateStoreLocation** と **CertificateStoreName** のその他の組み合わせは定義されません。 

**CertificateStoreLocation** パラメーターには、常に **LocalMachine** を指定します。 **CertificateStoreName** パラメーターは既定で "My" に設定されるため、指定する必要はありません。 **X509** 参照では、証明書ファイルがクラスター ノード上の */var/lib/sfcerts* ディレクトリに置かれている必要があります。  

次の XML は、このスタイルに基づいた **TransportSettings** セクションを示したものです。

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>X509_2 SecurityCredentialsType の使用

Java SDK では、**SecurityCredentialsType** に **X509_2** を指定することができます。 これは、`SecurityCredentials` ([Java](/java/api/system.fabric.securitycredentials)) の `X509Credentials2` ([Java](/java/api/system.fabric.x509credentials2)) タイプに対応します。 

**X509_2** 参照では、パスのパラメーターを指定するので、 */var/lib/sfcerts* 以外のディレクトリにある証明書を特定することができます。  次の XML は、証明書の場所を指定するためのパラメーターを示したものです。 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

次の XML は、このスタイルに基づいた **TransportSettings** セクションを示したものです。

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> 証明書は、上記の XML で .crt ファイルとして指定されます。 このことは、秘密キーを格納した .key ファイルも同じ場所にあることを示唆します。

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Reliable Services アプリを Linux クラスター上で実行するように構成する

Service Fabric SDK では、Service Fabric ランタイム API と通信して、プラットフォームを利用することができます。 セキュリティで保護された Linux クラスター上でこの機能を使ったアプリケーションを実行する場合は、Service Fabric ランタイムに対する検証に使用できる証明書を使って、アプリケーションを構成する必要があります。 .NET Core または Java SDK を使って記述された Service Fabric Reliable Service サービスを含むアプリケーションでは、この構成が必要になります。 

アプリケーションを構成するには、*ApplicationManifest.xml* ファイル内の **ApplicationManifest** タグの下にある、**Certificates** タグの下に [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) 要素を追加します。 次の XML は、サムプリントによって参照される証明書を示したものです。 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

参照できるのは、クラスター証明書か、各クラスター ノードにインストールされた証明書です。 Linux では、証明書ファイルは */var/lib/sfcerts* ディレクトリに置かれる必要があります。 詳しくは、「[Linux ノード上の X.509 証明書の場所と形式](#location-and-format-of-x509-certificates-on-linux-nodes)」をご覧ください。
