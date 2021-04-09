---
title: Visual Studio のテンプレート デプロイ スクリプトを Az PowerShell を使用するように更新する
description: Visual Studio テンプレート デプロイ スクリプトを AzureRM から Az PowerShell に更新する
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963871"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Visual Studio テンプレート デプロイ スクリプトを Az PowerShell モジュールを使用するように更新する

Visual Studio 16.4 では、テンプレート デプロイ スクリプトでの Az PowerShell モジュールの使用がサポートされています。 ただし、Visual Studio では、そのモジュールは自動的にインストールされません。 Az モジュールを使用するには、次の 4 つの手順を実行する必要があります。

1. [AzureRM モジュールのアンインストール](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Azure モジュールのインストール](/powershell/azure/install-az-ps)
1. Visual Studio を 16.4 に更新する
1. プロジェクト内のデプロイ スクリプトを更新する

## <a name="update-visual-studio-to-164"></a>Visual Studio を 16.4 に更新する

Visual Studio のインストールをバージョン 16.4 以降に更新します。 アップグレード中に、Azure PowerShell コンポーネントがオンになっていないことを確認してください。 Az モジュールをギャラリーからインストールしたため、AzureRM モジュールを再インストールする必要はありません。

既に 16.4 にアップグレードして Azure PowerShell コンポーネントがオンになっている場合は、Visual Studio インストーラーを実行してアンインストールすることができます。 Azure ワークロードまたは個々のコンポーネント ページで Azure PowerShell コンポーネントを選択しないでください。

## <a name="update-the-deployment-script-in-your-project"></a>プロジェクト内のデプロイ スクリプトを更新する

デプロイ スクリプトで出現するすべての文字列 "AzureRm" を "Az" に置き換えます。 変更を確認するために、この [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) でリビジョンを参照できます。 スクリプトを Az モジュールにアップグレードする方法の詳細については、「[AzureRM から Az への Azure PowerShell の移行](/powershell/azure/migrate-from-azurerm-to-az)」を参照してください。

## <a name="next-steps"></a>次のステップ

Visual Studio プロジェクトの使用の詳細については、[Visual Studio での Azure リソース グループ プロジェクトの作成とデプロイ](create-visual-studio-deployment-project.md)に関する記事を参照してください。
