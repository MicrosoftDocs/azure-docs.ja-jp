---
title: Azure コンテナーの技術資産を準備する
description: Azure Marketplace でコンテナー オファーを構成するのに役立つ技術リソースとガイドライン。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459320"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Azure コンテナーの技術資産を準備する

この記事では、Azure Marketplace でコンテナー オファーを作成するのに役立つ技術リソースと推奨事項を示します。

## <a name="before-you-begin"></a>開始する前に

クイックスタート、チュートリアル、サンプルについては、[Azure Container Instances のドキュメント](../container-instances/index.yml)をご覧ください。

## <a name="fundamental-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です。

エンジニアリング チームには､ソリューションのドメインだけでなく､Microsoft の次の技術に関する知識も必要です。

- [Azure Services](https://azure.microsoft.com/services/) に関する基本知識
- [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
- [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/)、[Azure ストレージ](https://azure.microsoft.com/services/?filter=storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking)に関する実用的な知識
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識
- [JSON](https://www.json.org/) に関する実用的な知識。

## <a name="suggested-tools"></a>推奨ツール

コンテナー イメージを管理するためのスクリプト環境として、次のいずれか一方または両方を選択します。

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure/)。

開発環境には次にツールを加えることを推奨します｡

- [Azure 記憶域エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 拡張機能: [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

使用できるツールについては、[Azure 開発者ツール](https://azure.microsoft.com/)に関するページをご覧ください。 Visual Studio を使用している場合は、[Visual Studio Marketplace](https://marketplace.visualstudio.com/) で使用できるツールを確認してください。

## <a name="create-the-container-image"></a>コンテナー イメージを作成する

オンプレミスのレジストリから Azure Container Instances にイメージをデプロイすることはできません。

- ローカル レジストリに既に作業コンテナーがある場合は、Azure レジストリを作成し、コンテナー イメージを Azure Container Registry にアップロードします。 詳細については、「[チュートリアル: Azure Container Registry タスクを使用して、クラウドでコンテナー イメージをビルドしてデプロイする](../container-registry/container-registry-tutorial-quick-task.md)。

- まだコンテナー イメージを持っておらず、既存のアプリケーションをコンテナ化したり、新しいコンテナー ベースのアプリケーションを作成する必要がある場合は、GitHub からアプリケーションのソース コードをクローンし、アプリケーションのソースからコンテナー イメージを作成して、ローカルの Docker 環境でイメージをテストします。 詳細については、「[チュートリアル: Azure Container Instances へのデプロイに使用するコンテナー イメージを作成する](../container-instances/container-instances-tutorial-prepare-app.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [コンテナー オファーを作成する](create-azure-container-offer.md)