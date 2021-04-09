---
title: Azure Service Fabric のプローブ
description: アプリケーションおよびサービスのマニフェスト ファイルを使用して、Azure Service Fabric の liveness probe をモデル化する方法について説明します。
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82137588"
---
# <a name="liveness-probe"></a>liveness probe
Azure Service Fabric のバージョン 7.1 以降では、[コンテナー化された][containers-introduction-link]アプリケーションに対して liveness probe メカニズムがサポートされています。 liveness probe は、コンテナー化されたアプリケーションの活動性を報告するのに役立ちます。すぐに応答しない場合、そのアプリケーションは再起動されます。
この記事では、マニフェスト ファイルを使用して liveness probe を定義する方法の概要を示します。

この記事を読み進める前に、[Service Fabric のアプリケーション モデル][application-model-link]と [Service Fabric のホスティング モデル][hosting-model-link]について理解を深めておいてください。

> [!NOTE]
> liveness probe は、NAT ネットワーク モードのコンテナーでのみサポートされています。

## <a name="semantics"></a>Semantics
指定できるのは、1 つのコンテナーにつき 1 つの liveness probe だけで、次のフィールドでその動作を制御できます。

* `initialDelaySeconds`: コンテナーが起動されてから、probe の実行を開始するまでの最初の遅延 (秒)。 サポートされる値は **int** です。既定値は 0、最小値は 0 です。

* `timeoutSeconds`: probe が正常に終了しなかった場合に、それが失敗したと見なされるまでの期間 (秒)。 サポートされる値は **int** です。既定値は 1、最小値は 1 です。

* `periodSeconds`: probe の頻度を指定する期間 (秒)。 サポートされる値は **int** です。既定値は 10、最小値は 1 です。

* `failureThreshold`: この値に達すると、コンテナーが再起動されます。 サポートされる値は **int** です。既定値は 3、最小値は 1 です。

* `successThreshold`: 失敗した場合、probe が成功と見なされるには、この値に対して正常に実行される必要があります。 サポートされる値は **int** です。既定値は 1、最小値は 1 です。

1 つのコンテナーに対して使用できる probe はいかなるときも最大で 1 つです。 **timeoutSeconds** で設定された時間内に probe が終了しない場合は、待機しながら **failureThreshold** までの時間をカウントします。 

さらに、Service Fabric により、**DeployedServicePackage** について次の probe [正常性][health-introduction-link]レポートが生成されます。

* `OK`: **successThreshold** で設定された値に対して probe は成功しています。

* `Error`: probe の **failureCount** ==  **failureThreshold**。この後、コンテナーが再起動されます。

* `Warning`: 
    * probe が失敗し、**failureCount** < **failureThreshold** となっています。 この正常性レポートは、**failureCount** が **failureThreshold** または **successThreshold** で設定された値に達するまで維持されます。
    * 失敗後に成功した場合、警告は残りますが、連続して成功した場合は更新されます。

## <a name="specifying-a-liveness-probe"></a>liveness probe の指定

**ServiceManifestImport** の下の ApplicationManifest.xml ファイルで probe を指定できます。

probe は、次のいずれかに対して指定できます。

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>HTTP プローブ

HTTP probe の場合、指定したポートとパスに、Service Fabric から HTTP 要求が送信されます。 返されるコードが 200 以上、400 未満の場合は、成功が示されます。

HTTP probe を指定する方法の例を次に示します。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HTTP probe には、設定できる次の追加のプロパティがあります。

* `path`: HTTP 要求で使用するパス。

* `port`: probe に使用するポート。 このプロパティは必須です。 範囲は 1 から 65535 です。

* `scheme`: コード パッケージへの接続に使用するスキーム。 このプロパティを HTTPS に設定すると、証明書の検証はスキップされます。 既定の設定は HTTP です。

* `httpHeader`: 要求内で設定するヘッダー。 複数のヘッダーを指定できます。

* `host`: 接続先のホスト IP アドレス。

### <a name="tcp-probe"></a>TCP プローブ

TCP probe の場合、Service Fabric では、指定されたポートを使用してコンテナー上のソケットを開こうとします。 接続を確立できる場合、probe は成功と見なされます。 TCP ソケットを使用する probe を指定する方法の例を次に示します。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Exec probe

この probe では、**exec** コマンドをコンテナーに発行し、そのコマンドが終了するまで待機します。

> [!NOTE]
> **exec** コマンドでは、コンマで区切られた文字列を取得します。 次の例のコマンドは、Linux コンテナーで使用できます。
> Windows コンテナーに対して probe を試す場合は、**cmd** を使用します。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>次のステップ
関連情報については、次の記事を参照してください。
* [Service Fabric とコンテナー][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

