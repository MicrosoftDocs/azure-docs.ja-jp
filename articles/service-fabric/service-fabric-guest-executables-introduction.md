---
title: 既存の実行可能ファイルを Azure Service Fabric にパッケージする
description: 既存のアプリケーションを Service Fabric クラスターにデプロイできるようにゲスト実行可能ファイルとしてパッケージ化する方法について説明します。
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017748"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>既存の実行可能ファイルを Service Fabric にデプロイする
Node.js、Java、C++ など、あらゆる種類のコードをサービスとして Azure Service Fabric 内で実行できます。 このようなサービスを Service Fabric ではゲスト実行可能ファイルと呼びます。

Service Fabric は、ゲスト実行可能ファイルをステートレス サービスと同様に扱います。 これにより、ゲスト実行可能ファイルは可用性などのメトリックに基づいてクラスター内のノードに配置されます。 この記事を読むと、Visual Studio またはコマンド ライン ユーティリティを使用して、ゲスト実行可能ファイルをパッケージ化し、Service Fabric クラスターにデプロイする方法を理解できます。

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Service Fabric でゲスト実行可能ファイルを実行するメリット
Service Fabric クラスターでゲスト実行可能ファイルを実行するメリットはいくつかあります。

* 高可用性: アプリケーションを Service Fabric で実行すると、可用性が向上します。 Service Fabric によって、アプリケーションのインスタンスが常に稼動している状態が維持されます。
* 正常性状態の監視。 Service Fabric の正常性監視機能により、アプリケーションが稼動しているかどうかが検出され、障害が発生した場合は診断情報が提供されます。   
* アプリケーション ライフサイクル管理: Service Fabric を使用すると、ダウンタイムなしでアップグレードを実行できるだけでなく、アップグレード中に正常性に問題が発生したことが報告された場合は前のバージョンに自動的にロールバックされます。    
* 密度: 各アプリケーションを専用のハードウェアで実行する必要がないクラスターで複数のアプリケーションを実行できます。
* 探索可能性: REST を使って Service Fabric Naming Service を呼び出し、クラスター内の他のサービスを発見できます。 

## <a name="samples"></a>サンプル
* [ゲスト実行可能ファイルをパッケージ化してデプロイするためのサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [REST を使用してネーム サービス経由で通信する 2 つのゲスト実行可能ファイル (C# と nodejs) のサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>アプリケーション マニフェスト ファイルとサービス マニフェスト ファイルの概要
ゲスト実行可能ファイルのデプロイの一環として、[アプリケーション モデル](service-fabric-application-model.md)に関するページに目を通し、Service Fabric のパッケージ化とデプロイのモデルについて理解しておくと役立ちます。 Service Fabric パッケージ化モデルでは、アプリケーション マニフェストとサービス マニフェストの 2 つの XML ファイルを使用します。 ApplicationManifest.xml ファイルと ServiceManifest.xml ファイルのスキーマ定義が、Service Fabric SDK と共に *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* にインストールされています。

* **アプリケーション マニフェスト**: アプリケーション マニフェストは、アプリケーションについて記述するために使用されます。 このファイルには、アプリケーションを構成する複数のサービスとその他のパラメーター (インスタンス数など) が列挙されています。パラメーターは、個々のサービスや複数のサービスをデプロイする方法を定義するために使用されます。

  Service Fabric では、デプロイとアップグレードを行う際に 1 つのアプリケーションをユニットとして扱います。 アプリケーションは 1 つのユニットとしてアップグレードすることができ、エラー (およびロールバック) が発生した場合も 1 つのユニットとして管理されます。 Service Fabric は、アップグレード プロセスが正常完了した場合だけでなく、失敗した場合でも、アプリケーションが不明または不安定な状態にならないことを保証します。
* **サービス マニフェスト** : サービス マニフェストは、サービスのコンポーネントを記述するために使用されます。 このファイルには、サービスの名前と種類、そのコード、構成などの情報が記載されています。 また、サービスのデプロイ後の構成に使用できるその他のパラメーターも含まれます。

## <a name="application-package-file-structure"></a>アプリケーション パッケージ ファイルの構造
Service Fabric にアプリケーションをデプロイするには、事前に定義されたディレクトリ構造にアプリケーションが従っている必要があります。 この構造の例を次に示します。

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot には、アプリケーションを定義する ApplicationManifest.xm ファイルが含まれています。 アプリケーションに含まれる各サービスのサブディレクトリには、サービスに必要なすべてのアーティファクトが含まれています。 これらのサブディレクトリは、ServiceManifest.xml と一般的に次のディレクトリです。

* *Code*。 このディレクトリにはサービス コードが含まれています。
* *Config*:settings.xml ファイル (および、必要に応じてその他のファイル) が含まれています。実行時にサービスがこのファイルにアクセスし、特定の構成設定を取得します。
* *データ*。 サービスでこのデータが必要になる場合があります。 データは、一時的なデータのみを格納します。 フェールオーバー中など、サービスの再配置が必要な場合、Service Fabric ではこのデータ ディレクトリには変更がコピー/レプリケートされません。

> [!NOTE]
> `config` ディレクトリと `data` ディレクトリは、必要がなければ作成する必要はありません。
>
>

## <a name="next-steps"></a>次のステップ
関連する情報やタスクは、次の記事を参照してください。
* [ゲスト実行可能ファイルをデプロイする](service-fabric-deploy-existing-app.md)
* [複数のゲスト実行可能ファイルのデプロイ](./service-fabric-deploy-existing-app.md)
* [Visual Studio で最初のゲスト実行可能ファイル アプリケーションを作成する](quickstart-guest-app.md)
* [ゲスト実行可能ファイルをパッケージ化してデプロイするためのサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)。これには、パッケージ化ツールのプレリリース版のリンクが含まれています。
* [REST を使用してネーム サービス経由で通信する 2 つのゲスト実行可能ファイル (C# と nodejs) のサンプル](https://github.com/Azure-Samples/service-fabric-containers)
