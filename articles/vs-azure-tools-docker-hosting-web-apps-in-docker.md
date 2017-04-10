---
title: "リモート Docker ホストへの ASP.NET Core Linux Docker コンテナーのデプロイ | Microsoft Docs"
description: "Visual Studio Tools for Docker を使用して、Azure Docker ホストの Linux VM で実行されている Docker コンテナーに ASP.NET Core Web アプリをデプロイする方法を説明します。"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: f99dcb1ce4cfe2a629c9254264a93b4d9d02ce4b


---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>リモート Docker ホストへの ASP.NET コンテナーのデプロイ
## <a name="overview"></a>Overview
Docker は軽量のコンテナー エンジンで、アプリケーションとサービスをホストするために使用できる仮想マシンにいくつかの点で似ています。
このチュートリアルでは、 [Visual Studio 2015 Tools for Docker](http://aka.ms/DockerToolsForVS) 拡張機能を使用して、Azure 上の Docker ホストに PowerShell で ASP.NET Core アプリをデプロイする手順について説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* 「[Azure で docker マシンを使用する方法](virtual-machines/virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」の説明に従って、Azure Docker ホスト VM を作成する
*  [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)
*  [Visual Studio 2015 Tools for Docker - Preview](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1.ASP.NET Core Web アプリケーションの作成
次の手順では、このチュートリアルで使用する基本的な ASP.NET Core アプリの作成について説明します。

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2.Docker サポートの追加
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3.DockerTask.ps1 PowerShell スクリプトの使用
1. PowerShell プロンプトからプロジェクトのルート ディレクトリに移動します。 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. リモート ホストが実行中であることを確認します。 State が "Running" と表示されている必要があります。 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
   > [!NOTE]
   > Docker Beta を使用している場合、ここにホストは表示されません。
   > 
   > 
3. -Build パラメーターを使用してアプリをビルドします。
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  
   
   > [!NOTE]
   > Docker Beta を使用している場合、-Machine 引数は省略してください。
   > 
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. -Run パラメーターを使用してアプリを実行します。
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > [!NOTE]
   > Docker Beta を使用している場合、-Machine 引数は省略してください。
   > 
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Docker が完了すると、次のような結果が表示されます。
   
   ![アプリを表示する][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png



<!--HONumber=Nov16_HO3-->


