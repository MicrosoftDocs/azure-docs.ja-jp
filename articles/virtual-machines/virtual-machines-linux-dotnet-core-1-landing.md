---
title: "Azure Linux Virtual Machine DotNet Core チュートリアル 1 | Microsoft Docs"
description: "Azure Virtual Machines DotNet Core チュートリアル"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b3652e86-0c44-4ac9-8cd1-27abdeaea4d4
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d9d65d8fb5fd7b1ff226e96fe33f8b0757d7a12e
ms.lasthandoff: 03/03/2017


---
# <a name="automating-application-deployments-to-linux-virtual-machines"></a>Linux Virtual Machines へのアプリケーションのデプロイを自動化する 

この&4; 部構成のシリーズでは、Azure Resource Manage テンプレートを使った Azure リソースとアプリケーションのデプロイおよび構成について説明します。 このシリーズでは、サンプル テンプレートをデプロイし、デプロイ テンプレートを検証します。 このシリーズの目的は、Azure リソースの関係について学習し、完全に統合された Azure Resource Manager テンプレートのデプロイを実際に体験してみることです。 このドキュメントは、Azure Resource Manager に関する基本的な知識があることを前提としています。このチュートリアルを開始する前に、Azure Resource Manager の基本的な概念について理解を深めてください。 

## <a name="music-store-application"></a>ミュージック ストア アプリケーション
このシリーズで使うサンプルは、ミュージック ストアのショッピング体験をシミュレートする .Net Core アプリケーションです。 このアプリケーションは、Linux または Windows の仮想システムにデプロイできます。両方のサンプルのデプロイを作成しています。 このアプリケーションには、Web アプリケーションと SQL データベースが含まれています。 このシリーズの記事を読む前に、このページにあるデプロイ ボタンを使って、アプリケーションをデプロイしてください。 完全にデプロイすると、アプリケーション/Azure のアーキテクチャは次の図のようになります。 

ミュージック ストアの Resource Manager テンプレートは、こちら ([ミュージック ストアの Linux テンプレート](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db)) にあります。

![ミュージック ストア アプリケーション](./media/virtual-machines-linux-dotnet-core/music-store.png)

関連するテンプレート JSON を含め、これらの各コンポーネントについては、次の&4; つの記事で確認します。

* [**アプリケーション アーキテクチャ**](virtual-machines-linux-dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – Web サイトやデータベースなどのアプリケーション コンポーネントは、仮想マシンや Azure SQL データベースなどの Azure コンピューター リソースでホストする必要があります。 このドキュメントでは、コンピューティング ニーズを Azure リソースにマッピングし、Azure Resource Manager テンプレートを使ってこれらのリソースをデプロイする方法について説明します。 
* [**アクセスとセキュリティ**](virtual-machines-linux-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – Azure でアプリケーションをホストする場合は、アプリケーションにアクセスする方法とさまざまなアプリケーション コンポーネントが相互にアクセスする方法を検討する必要があります。 このドキュメントでは、アプリケーションへのインターネット アクセスとアプリケーション コンポーネント間のアクセスを提供し、セキュリティで保護する方法について説明します。
* [**可用性とスケール**](virtual-machines-linux-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – 可用性とスケールは、インフラストラクチャのダウンタイム時に実行を維持できるアプリケーション機能と、アプリケーションの需要に応じてコンピューティング リソースをスケールする機能を指します。 このドキュメントでは、負荷分散された高可用性アプリケーションをデプロイするうえで必要なコンポーネントについて説明します。
* [**アプリケーションのデプロイ**](virtual-machines-linux-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - アプリケーションを Azure Virtual Machines にデプロイする場合は、アプリケーション バイナリを Virtual Machines にインストールする方法を検討する必要があります。 このドキュメントでは、Azure 仮想マシンのカスタム スクリプト拡張機能を使って、アプリケーションのインストールを自動化する方法について説明します。

Azure Resource Manager テンプレートを開発する目的は、Azure インフラストラクチャのデプロイに加え、この Azure インフラストラクチャでホストするすべてのアプリケーションのインストールと構成を自動化することです。 これらの記事を通じて、このエクスペリエンスの例を示します。

## <a name="deploy-the-music-store-application"></a>ミュージック ストア アプリケーションのデプロイ
このボタンを使って、ミュージック ストア アプリケーションをデプロイできます。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-linux%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

この Azure Resource Manager テンプレートには、次のパラメーター値が必要です。

| パラメーター名 | Description |
| --- | --- |
| SSHKEYDATA |仮想マシンへのアクセスをセキュリティで保護するために使う SSH キー データ。 SSH キー ペアの作成については、[Azure の Linux VM 用 SSH キーの作成](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 |
| ADMINUSERNAME |仮想マシンと Azure SQL Database で使う管理者ユーザー名。 |
| SQLADMINPASSWORD |Azure SQL Database で使うパスワード。 |
| NUMBEROFINSTANCES |作成する仮想マシンの数。 これらの各仮想マシンがミュージック ストア Web アプリケーションをホストし、すべてのトラフィックがそれらの仮想マシン間で負荷分散されます。 |
| PUBLICIPADDRESSDNSNAME |パブリック IP アドレスに関連付けられているグローバルに一意の DNS 名。 |

テンプレートのデプロイが完了したら、任意のインターネット ブラウザーを使って、パブリック IP アドレスを参照します。 .Net Core のミュージック サイトが表示されます。

## <a name="next-steps"></a>次のステップ
<hr>

[手順 1 - Azure Resource Manager テンプレートを使ったアプリケーション アーキテクチャ](virtual-machines-linux-dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[手順 2 - Azure Resource Manager テンプレートでのアクセスとセキュリティ](virtual-machines-linux-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[手順 3 - Azure Resource Manager テンプレートでの可用性とスケール](virtual-machines-linux-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[手順 4 - Azure Resource Manager テンプレートを使ったアプリケーションのデプロイ](virtual-machines-linux-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


