---
title: パラメーターを使用してサービスのポート番号を指定する
description: Service Fabric でパラメーターを使ってアプリケーションのポートを指定する方法を説明します
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ba2fb459dc9c981ad168aca4d0edf969650ccf48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576708"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Service Fabric でパラメーターを使用してサービスのポート番号を指定する方法

この記事では、Visual Studio を使い、Service Fabric でパラメーターを使ってサービスのポート番号を指定する方法について説明します。

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>パラメーターを使ってサービスのポート番号を指定する手順

この例では、パラメーターを使って ASP.NET Core Web API のポート番号を設定します。

1. Visual Studio を開き、新しい Service Fabric アプリケーションを作成します。
1. ステートレス ASP.NET Core テンプレートを選びます。
1. Web API を選びます。
1. ServiceManifest.xml ファイルを開きます。
1. サービスに指定されているエンドポイントの名前を書き留めておきます。 既定値は `ServiceEndpoint` です。
1. ApplicationManifest.xml ファイルを開きます。
1. `ServiceManifestImport` 要素に新しい `RessourceOverrides` 要素を追加し、ServiceManifest.xml ファイル内のエンドポイントを参照するように指定します。

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. `Endpoint` 要素で、パラメーターを使って任意の属性をオーバーライドできるようになります。 次の例では、`Port` を指定し、角かっこを使ってパラメーター名に設定しています (例: `[MyWebAPI_PortNumber]`)。

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. ApplicationManifest.xml ファイルの `Parameters` 要素でパラメーターを指定します。

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. `DefaultValue` を定義します。

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. ApplicationParameters フォルダーの `Cloud.xml` ファイルを開きます。
1. リモート クラスターに公開するときに使う別のポートを指定するには、そのポート番号のパラメーターをこのファイルに追加します。

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Cloud.xml 発行プロファイルを使って Visual Studio からアプリケーションを発行すると、サービスはポート 80 を使うように構成されます。 MyWebAPI_PortNumber パラメーターを指定しないでアプリケーションを展開すると、サービスはポート 8080 を使います。

## <a name="next-steps"></a>次のステップ
この記事で説明されている主要な概念の一部について詳しくは、「[複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)」をご覧ください。

Visual Studio で使用可能なその他のアプリケーション管理機能については、 [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)に関する記事をご覧ください。
