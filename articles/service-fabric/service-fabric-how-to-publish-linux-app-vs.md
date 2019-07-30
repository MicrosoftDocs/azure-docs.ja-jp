---
title: リモートの Azure Service Fabric Linux クラスター向けの .Net Core アプリケーションの作成および発行について | Microsoft Docs
description: Visual Studio からリモートの Linux クラスターをターゲットとする .Net Core アプリケーションを作成および発行する
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068126"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Visual Studio を使用してリモート Linux Service Fabric クラスターをターゲットとする .Net Core アプリケーションを作成および発行する
Visual Studio ツールを使用して、Linux Service Fabric クラスターをターゲットとする Service Fabric .Net Core アプリケーションを開発して発行できます。 Visual Studio から Linux Service Fabric クラスターをターゲットとする .Net Core アプリケーションをデプロイするには、バージョン 3.4 以降の SDK が必要になります。

> [!Note]
> Visual Studio では、Linux をターゲットとする Service Fabric アプリケーションのデバッグをサポートしていません。
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>.Net Core をターゲットとした Service Fabric アプリケーションの作成
1. Visual Studio を**管理者**として起動します。
2. **[ファイル] -> [新規] -> [プロジェクト]** の順に選択して、プロジェクトを作成します。
3. **[新しいプロジェクト]** ダイアログで、 **[クラウド] -> [Service Fabric アプリケーション]** の順に選択します。
![create-application]
4. アプリケーションに名前を付けて、 **[OK]** をクリックします。
5. **[新しい Service Fabric サービス**] ページで、 **[.Net Core] セクション**の下で作成するサービスの種類を選択します。
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>リモートの Linux クラスターへのデプロイ
1. ソリューション エクスプローラーで、アプリケーションを右クリックし、 **[ビルド]** を選択します。
![build-application]
2. アプリケーションのビルド プロセスが完了したら、サービスを右クリックし、**csproj ファイル**の編集を選択します。
![edit-csproj]
3. サービスが**アクター プロジェクト タイプ**の場合は、UpdateServiceFabricManifestEnabled プロパティを True から **False** に編集します。 アプリケーションにアクター サービスがない場合は、手順 4 に進みます。
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> UpdateServiceFabricManifestEnabled を false に設定すると、ビルド中の ServiceManifest.xml の更新が無効になります。 サービスに対する追加、削除、名前変更などの変更は、ServiceManifest.xml に反映されません。 何らかの変更が行われた場合は、ServiceManifest を手動で更新するか、または UpdateServiceFabricManifestEnabled を一時的に true に設定して、ServiceManifest.xml を更新するサービスをビルドした後で、設定を false に戻します。
>

4. RuntimeIndetifier を win7-x64 からサービス プロジェクトのターゲット プラットフォームに更新します。
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. ServiceManifest で、エントリポイント プログラムを更新して、.exe を削除します。 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. ソリューション エクスプローラーで、アプリケーションを右クリックし、 **[発行]** を選択します。 **[発行]** ダイアログ ボックスが表示されます。
7. **[接続のエンドポイント]** で、ターゲットにするリモート Service Fabric Linux クラスターのエンドポイントを選択します。
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>次の手順
* [.NET Core を使用する Service Fabric の概要](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)について学習します
