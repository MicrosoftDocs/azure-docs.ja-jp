---
title: Visual Studio のテンプレート デプロイ スクリプトを Az Powershell を使用するように更新する
description: Visual Studio テンプレート デプロイ スクリプトを AzureRM から Az Powershell に更新する
author: cweining
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: 483b20f0003994f88ec35cc9f31a77cf571804f3
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695686"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Visual Studio テンプレート デプロイ スクリプトを Az Powershell を使用するように更新する

Visual Studio 16.4 では、テンプレート デプロイ スクリプトでの Az Powershell の使用がサポートされています。 Visual Studio は Az Powershell をインストールせず、リソース グループ プロジェクト内のデプロイ スクリプトを自動的に更新することもありません。 より新しい Az Powershell を使用するには、以下の 4 つの操作を行う必要があります。
1. AzureRM powershell をアンインストールする
1. Az Powershell をインストールする
1. Visual Studio 16.4 に更新する
1. プロジェクト内のデプロイ スクリプトを更新する

## <a name="uninstall-azurerm-powershell"></a>AzureRM powershell をアンインストールする
この[手順](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module)に従って、AzureRM Powershell をアンインストールしてください。

## <a name="install-az-powershell"></a>Az Powershell をインストールする
この[手順](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0)に従って、Az Powershell をインストールしてください。

## <a name="update-visual-studio-to-164"></a>Visual Studio を 16.4 に更新する
Visual Studio 16.4 が使用可能になったら、これに更新します。 アップグレード中に、Azure Powershell コンポーネントがオンになっていないことを確認してください。 ギャラリーを通じて Az Powershell をインストールしたため、Visual Studio と共にインストールされる Powershell の AzureRM バージョンは必要ありません。

既に 16.4 にアップグレードしていて、Azure Powershell コンポーネントがオンになっていた場合は、Visual Studio インストーラーを実行し、Azure ワークロードまたは個々のコンポーネント ページで Azure Powershell コンポーネントをオフにすることにより、アンインストールできます。

## <a name="update-the-deployment-script-in-your-project"></a>プロジェクト内のデプロイ スクリプトを更新する
デプロイ スクリプトで出現するすべての文字列 "AzureRm" を "Az" に置き換えます。 変更を確認するために、この [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) でリビジョンを参照できます。 Az Powershell へのスクリプトのアップグレードの詳細については、この[ドキュメント](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0)を参照してください。


