---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814832"
---
この機能はプレビュー段階にあります。 これを使用するには、プレビュー拡張機能またはモジュールをインストールする必要があります。

### <a name="install-extension-for-azure-cli"></a>Azure CLI 用の拡張機能のインストール

Azure CLI の場合は、[Event Grid 拡張機能](/cli/azure/azure-cli-extensions-list)が必要です。

[CloudShell](/azure/cloud-shell/quickstart) の場合:

* 拡張機能を以前にインストールしている場合は、`az extension update -n eventgrid` で拡張機能を更新します。
* 拡張機能を以前にインストールしていない場合は、`az extension add -n eventgrid` で拡張機能をインストールします。

ローカル インストールの場合:

1. [Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。 `az --version` を使って確認し、最新バージョンがあることを確認します。
1. `az extension remove -n eventgrid` で以前のバージョンの拡張機能をアンインストールします。
1. `az extension add -n eventgrid` で `eventgrid` 拡張機能をインストールします

### <a name="install-module-for-powershell"></a>PowerShell 用のモジュールのインストール

PowerShell の場合は、[AzureRM.EventGrid モジュール](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)が必要です。

[CloudShell](/azure/cloud-shell/quickstart-powershell) の場合:

* モジュールをインストールします。`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

ローカル インストールの場合:

1. 管理者として PowerShell コンソールを開きます。
1. モジュールをインストールします。`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

`-AllowPrerelease`パラメーターが使用可能でない場合は、次のステップを実行します。

1. `Install-Module PowerShellGet -Force` を実行します。
1. `Update-Module PowerShellGet` を実行します。
1. PowerShell コンソールを閉じます。
1. 管理者として PowerShell を再起動します。
1. モジュールをインストールします。`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
