---
title: スタンドアロン クラスターの構成をアップグレードする
description: スタンドアロン Service Fabric クラスターを実行する構成をアップグレードする方法について説明します。
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 3cb1d40f5b32415588d3fd5a108967cfb4c0e534
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842616"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>スタンドアロン クラスターの構成をアップグレードする 

最新のシステムでは、アップグレードできることが製品の長期的な成功の鍵となります。 Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。 この記事では、スタンドアロン Service Fabric クラスターの構成設定をアップグレードする方法について説明します。

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>ClusterConfig.json ファイルでクラスターの設定をカスタマイズする
スタンドアロン クラスターは、*ClusterConfig.json* ファイルを使って構成します。 さまざまな設定の詳細については、「[スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)」をご覧ください。

*ClusterConfig.json* の [Cluster properties](./service-fabric-cluster-manifest.md#cluster-properties) セクションの `fabricSettings` セクションで、設定を追加、更新、または削除できます。 

たとえば、次の JSON は、`fabricSettings` の *Diagnostics* セクションに新しい設定 *MaxDiskQuotaInMB* を追加します。

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

ClusterConfig.json ファイルの設定を変更した後は、[クラスターの構成をテスト](#test-the-cluster-configuration)した後、[クラスターの構成をアップグレード](#upgrade-the-cluster-configuration)して、クラスターに設定を適用します。 

## <a name="test-the-cluster-configuration"></a>クラスター構成のテスト
構成アップグレードを開始する前に、スタンドアロン パッケージで次の PowerShell スクリプトを実行し、新しいクラスター構成 JSON をテストできます。

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

または、次のスクリプトを使用します。

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

エンドポイント、クラスター名、ノード IP など、一部の構成はアップグレードできません。古いクラスター構成 JSON に対して新しいクラスター構成 JSON をテストし、問題があれば、PowerShell ウィンドウにエラーを表示します。

## <a name="upgrade-the-cluster-configuration"></a>クラスター構成のアップグレード
クラスター構成をアップグレードするには、[Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) を実行します。 構成のアップグレードは、アップグレード ドメインごとに処理されます。

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>クラスター証明書の構成のアップグレード
クラスター証明書はクラスター ノード間の認証に使用されます。 証明書のロールオーバーが失敗するとクラスター ノード間の通信がブロックされるため、慎重に行ってください。

4 つのオプションがサポートされています。  

* 証明書のシングル アップグレード: アップグレードのパスは '証明書 A (プライマリ) -> 証明書 B (プライマリ) -> 証明書 C (プライマリ) -> ...' です。

* 証明書のダブル アップグレード: アップグレードのパスは '証明書 A (プライマリ) -> 証明書 A (プライマリ) および B (セカンダリ) -> 証明書 B (プライマリ) -> 証明書 B (プライマリ) および C (セカンダリ) -> 証明書 C (プライマリ) -> ...' です。

* 証明書の種類のアップグレード: 拇印ベースの証明書の構成 <-> CommonName ベースの証明書の構成。 たとえば、証明書の拇印 A (プライマリ) と拇印 B (セカンダリ) -> 証明書 CommonName C。

* 証明書の発行者の拇印のアップグレード: アップグレードのパスは、'証明書 CN = A、発行者の拇印 = IT1 (プライマリ) -> 証明書 CN = A、発行者の拇印 = IT1、IT2 (プライマリ) -> 証明書 CN = A、発行者の拇印 = IT2 (プライマリ)' です。


## <a name="next-steps"></a>次のステップ
* 一部の [Service Fabric クラスター設定](service-fabric-cluster-fabric-settings.md)をカスタマイズする方法を学習します。
* [クラスターのスケールアップとスケールダウン](service-fabric-cluster-scale-in-out.md)を行う方法を学習します。
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)を行う方法を学習します。

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
