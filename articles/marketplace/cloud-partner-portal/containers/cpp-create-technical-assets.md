---
title: Azure コンテナー イメージの技術資産を作成する | Microsoft Docs
description: Azure コンテナー用の技術資産を作成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 5a7531be73a872d9c088a0bf02a8686f947c220a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047366"
---
# <a name="prepare-your-container-technical-assets"></a>コンテナーの技術資産を準備する

この記事では、Azure Marketplace 用のコンテナー オファーを構成するための手順と要件について説明します。

## <a name="before-you-begin"></a>開始する前に

クイック スタート、チュートリアル、サンプルが提供されている「[Azure Container Instances のドキュメント](https://docs.microsoft.com/azure/container-instances)」を確認してください。

## <a name="fundamental-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です｡
 
エンジニアリング チームには､ソリューションのドメインばかりでなく､Microsoft の次の技術に関する知識も必要です｡

-   [Azure Services](https://azure.microsoft.com/services/) に関する基本知識 
-   [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
-   [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/) と [Azure ストレージ](https://azure.microsoft.com/services/?filter=storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking)に関する実用的な知識
-   [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識
-   [JSON](https://www.json.org/) に関する実用的な知識

## <a name="suggested-tools"></a>推奨ツール

コンテナー イメージを管理するためのスクリプト環境として、次のいずれか一方または両方を選択します。

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

また､開発環境には次にツールを加えることを推奨します｡

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   拡張機能: [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

また､[Azure Developer Tools](https://azure.microsoft.com/tools/) ページの記載されている利用可能なツールもご覧になることをお勧めします｡Visual Studio を使用する場合は､[Visual Studio Marketplace](https://marketplace.visualstudio.com/) もご覧ください｡

## <a name="create-the-container-image"></a>コンテナー イメージを作成する

- コンテナー仮想マシン (VM) 用の仮想ハード ディスク (VHD) を作成して構成します。 この VHD には、コンテナー用のオペレーティング システム (Windows、Linux、または Ubuntu) が格納されます。 データ ディスクの追加が必要になる場合があります。
- VM の OS、VM のサイズ、開くポート、接続するデータ ディスクを構成します。
- オファーに必要なアプリケーションと他のソフトウェアをインストールします。 たとえば、データベース ソフトウェア、サード パーティ製ソフトウェア、カスタム アプリケーションなどです。

## <a name="next-steps"></a>次の手順

[コンテナー オファーを作成する](./cpp-create-offer.md)
