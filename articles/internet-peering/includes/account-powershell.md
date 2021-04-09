---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678468"
---
構成を開始する前に、必要なモジュールをインストールしてインポートします。 PowerShell でモジュールをインストールするには、管理者特権が必要です。

1. Az モジュールをインストールしてインポートします。
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az.Peering モジュールをインストールしてインポートします。
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 次のコマンドを使用して、モジュールが正しくインポートされたことを確認します。
    ```powershell
    Get-Module
    ```
1. 次のコマンドを使用して Azure アカウントにサインインします。
    ```powershell
    Connect-AzAccount
    ```
1. アカウントのサブスクリプションを確認し、ピアリングを作成するサブスクリプションを選択します。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. リソース グループがまだない場合は、ピアリングを作成する前に、作成しておく必要があります。 リソース グループを作成するには、次のコマンドを実行します。

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> まだ ASN とサブスクリプションを関連付けていない場合は、[ピア ASN の関連付け](../howto-subscription-association-powershell.md)に関するページの手順に従ってください。 この操作はピアリングを要求するために必要です。

> [!NOTE]
> リソース グループの場所は、ピアリングを設定するために選択する場所とは関係ありません。
&nbsp;
