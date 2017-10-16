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
ms.openlocfilehash: 4a87ee69f23779bf4f6f5db40bc05edbcfc7668d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>リモート Docker ホストへの ASP.NET コンテナーのデプロイ
## <a name="overview"></a>概要
Docker は軽量のコンテナー エンジンで、アプリケーションとサービスをホストするために使用できる仮想マシンにいくつかの点で似ています。
このチュートリアルでは、[Visual Studio Tools for Docker](https://docs.microsoft.com/en-us/dotnet/articles/core/docker/visual-studio-tools-for-docker) 拡張機能を使って、Azure 上の Docker ホストに PowerShell で ASP.NET Core アプリをデプロイする手順について説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* 「[Azure で docker マシンを使用する方法](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」の説明に従って、Azure Docker ホスト VM を作成する
* [Visual Studio](https://www.visualstudio.com/downloads/) の最新バージョンをインストールする
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122) をダウンロードする
* [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) をインストールする

## <a name="1-create-an-aspnet-core-web-app"></a>1.ASP.NET Core Web アプリケーションの作成
次の手順では、このチュートリアルで使用する基本的な ASP.NET Core アプリの作成について説明します。

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>手順 2.Docker サポートの追加
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
   
3. -Build パラメーターを使用してアプリをビルドします。
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. -Run パラメーターを使用してアプリを実行します。
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
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
