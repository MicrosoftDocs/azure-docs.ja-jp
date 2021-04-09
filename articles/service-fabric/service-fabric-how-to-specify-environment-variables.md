---
title: サービスの環境変数を指定する
description: Service Fabric でアプリケーション用の環境変数を使う方法について説明します
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 610e44ac98b8b8a2ce5a91fdbcfda145ae36a94b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576742"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Service Fabric のサービス用に環境変数を指定する方法

この記事では、Service Fabric でサービスまたはコンテナーの環境変数を指定する方法を説明します。

## <a name="procedure-for-specifying-environment-variables-for-services"></a>サービス用の環境変数を指定する手順

この例では、コンテナーの環境変数を設定します。 アプリケーションおよびサービス マニフェストは既にあるものとします。

1. ServiceManifest.xml ファイルを開きます。
2. `CodePackage` 要素に、環境変数ごとに新しい `EnvironmentVariables` 要素と `EnvironmentVariable` 要素を追加します。

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   環境変数は、アプリケーション マニフェスト内でオーバーライドできます。

3. アプリケーション マニフェストで環境変数をオーバーライドするには、`EnvironmentOverrides` 要素を使います。

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Docker Compose を使用して環境変数を動的に指定する

Service Fabric では、[Docker Compose を使用してデプロイする](service-fabric-docker-compose.md#supported-compose-directives)機能がサポートされています。 Compose ファイルでは、環境変数をシェルから取得できます。 この動作を使用して、必要な環境値を動的に置き換えることができます。

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>次のステップ
この記事で説明されている主要な概念の一部について詳しくは、「[複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)」をご覧ください。

Visual Studio で使用可能なその他のアプリケーション管理機能については、 [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)に関する記事をご覧ください。
