---
title: "Azure マイクロサービスの開発環境のセットアップ | Microsoft Docs"
description: "ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。 このセットアップを終えれば、アプリケーションを構築する準備は完了です。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 10a5871d64524ff8adeb6ee3041b348ef2b1b32e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="prepare-your-development-environment"></a>開発環境を準備する
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 開発コンピューターで [Azure Service Fabric アプリケーション][1]をビルドして実行するには、ランタイム、SDK、およびツールをインストールしてください。 また、SDK に含まれる Windows PowerShell スクリプトの実行を有効にする必要があります。

## <a name="prerequisites"></a>前提条件
### <a name="supported-operating-system-versions"></a>サポートされるオペレーティング システムのバージョン
開発では、次のオペレーティング システムのバージョンがサポートされます。

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 には、既定では Windows PowerShell 2.0 のみが含まれます。 Service Fabric PowerShell のコマンドレットには PowerShell 3.0 以降が必要です。 Microsoft ダウンロード センターから [Windows PowerShell 5.0 をダウンロード][powershell5-download]できます。
> 
> 

## <a name="install-the-sdk-and-tools"></a>SDK とツールのインストール
### <a name="to-use-visual-studio-2017"></a>Visual Studio 2017 を使用するには
Service Fabric ツールは、Visual Studio 2017 の Azure 開発および管理ワークロードに含まれています。 このワークロードを Visual Studio のインストールの一環として有効にします。
さらに、Web Platform Installer を使用して Microsoft Azure Service Fabric SDK をインストールする必要があります。

* [Microsoft Azure Service Fabric SDK のインストール][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Visual Studio 2015 を使用するには (Visual Studio 2015 Update 2 以降が必要)
Visual Studio 2015 では、Service Fabric ツールは、Web Platform Installer を使用して SDK と共にインストールされます。

* [Microsoft Azure Service Fabric SDK とツールのインストール][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>SDK のみのインストール
SDK のみが必要な場合は、次のパッケージをインストールすることができます。
* [Microsoft Azure Service Fabric SDK のインストール][core-sdk]

現在のバージョンは次のとおりです。
* Service Fabric SDK 2.7.198
* Service Fabric ランタイム 5.7.198
* Service Fabric Tools for Visual Studio 2015 1.7.50721
* Visual Studio 2017 Update 3 には、Service Fabric Tools for Visual Studio 1.7.20170817 が含まれています
* Visual Studio 2017 Update 4 Preview 1 (15.4.0 Preview 1.0) には、Service Fabric Tools for Visual Studio 1.7.20170721 が含まれています

サポートされているバージョンの一覧については、[Service Fabric のサポート](service-fabric-support.md)に関するページを参照してください。

## <a name="enable-powershell-script-execution"></a>PowerShell スクリプトの実行の有効化
Service Fabric は、ローカル開発クラスターの作成、および Visual Studio からのアプリケーションのデプロイに、Windows PowerShell スクリプトを使用します。 既定では、Windows はこれらのスクリプトの実行をブロックします。 これらを有効にするには、PowerShell 実行ポリシーを変更する必要があります。 管理者として PowerShell を開き、次のコマンドを入力します。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>次のステップ
開発環境のセットアップが完了したので、アプリのビルドと実行を開始してください。

* [Visual Studio で最初の Service Fabric アプリケーションを作成する](service-fabric-create-your-first-application-in-visual-studio.md)
* [ローカル クラスター上でアプリケーションをデプロイし管理する方法](service-fabric-get-started-with-a-local-cluster.md)
* [サービスのフレームワークを選択する](service-fabric-choose-framework.md)
* [GitHub での Service Fabric コード サンプルの確認](https://aka.ms/servicefabricsamples)
* [Service Fabric エクスプローラーを使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)
* [Service Fabric のラーニング パスに沿ってプラットフォームの広範な概要を理解する](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Service Fabric キャンペーン ページ"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI のリンク"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI のリンク"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI のリンク"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

