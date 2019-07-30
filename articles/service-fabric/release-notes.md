---
title: Azure Service Fabric のリリース
description: Service Fabric の最新の機能および強化に関するリリース ノートです。
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 78fb96cae3d3d128da608183f37771b2ecf66dcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165358"
---
# <a name="service-fabric-releases"></a>Service Fabric のリリース

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">トラブルシューティング ガイド</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">問題追跡</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">サポート オプション</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">サポートされているバージョン</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">コード サンプル</a>

この記事では、Service Fabric のランタイムと SDK に対する最新リリースと更新プログラムの詳細を提供します。

## <a name="whats-new-in-service-fabric"></a>**Service Fabric の新機能**

### <a name="service-fabric-65"></a>Service Fabric 6.5

Service Fabric の最新のリリースには、サポート性、信頼性、パフォーマンスの向上、新機能、バグの修正、およびクラスターとアプリケーションのライフサイクル管理を容易にする拡張機能が含まれています。

> [!IMPORTANT]
> Service Fabric 6.5 は、Visual Studio 2015 での Service Fabric ツールのサポートの最終リリースです。 お客様には、今後は [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) に移行することをお勧めします。

Service Fabric 6.5 の新機能は以下のとおりです。

- Service Fabric Explorer には、イメージ ストアにアップロードしたアプリケーションを検査するための [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) が含まれます。

- [パッチ オーケストレーション アプリケーション (POA)](service-fabric-patch-orchestration-application.md) バージョン [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) には、自己診断に関する多くの機能強化が含まれます。 POA のお客様には、このバージョンに移行することをお勧めします。

- お客様がオプトアウトしていない限り、Service Fabric 6.5 クラスターでは [EventStore サービスが既定で有効になります](service-fabric-visualizing-your-cluster.md#event-store)。

- ステートフル サービス用の[レプリカ ライフサイクル イベント](service-fabric-diagnostics-event-generation-operational.md#replica-events)が追加されました。

- [シード ノードの状態の可視性が向上しました](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)。シード ノードが異常な場合のクラスター レベルの警告などです ("*ダウン*"、"*削除済み*"、または "*不明*")。

- [Service Fabric アプリケーション ディザスター リカバリー ツール](https://github.com/Microsoft/Service-Fabric-AppDRTool)を使用すると、プライマリ クラスターで災害が発生した場合に、Service Fabric ステートフル サービスを迅速に復旧できます。 プライマリ クラスターからのデータは、定期的なバックアップと復元を使用して、セカンダリ スタンバイ アプリケーションで継続的に同期されます。

- Visual Studio では、[Linux ベースのクラスターへの .NET Core アプリの発行](service-fabric-how-to-publish-linux-app-vs.md)がサポートされます。

- Azure で新しい Linux クラスターにアップグレードするか、またはクラスターを作成すると、Service Fabric 6.5 (およびそれ以降のバージョン) では [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) が自動的にインストールされます。

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) は、既定で MacOS/Linux OneBox クラスターにインストールされます。

詳しくは[Service Fabric 6.5 のリリース ノート](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)をご覧ください。

## <a name="previous-versions"></a>以前のバージョン

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 のリリース

| リリース日 | Release | 詳細情報 |
|---|---|---|
| 2018 年 11 月 30 日 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [Windows クラスター向け Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019 年 2 月 4 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019 年 3 月 4 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019 年 4 月 8 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 年 5 月 2 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 年 5 月 28 日 | [Azure Service Fabric 6.4 更新リリース](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [リリース ノート](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
