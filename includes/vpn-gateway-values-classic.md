---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 11d2172d085fe9b47587f4084908f99d7b54437e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103239"
---
Azure Portal でクラシック VNets を作成するときに表示される名前は、PowerShell に使用する完全な名前ではありません。 たとえば、Azure Portal に **TestVNet1** という名前で表示されている VNet は、ネットワーク構成ファイルではそれよりもかなり長い名前である可能性があります。 リソース グループ "ClassicRG" の VNet の場合、名前は次のようになります。**Group ClassicRG TestVNet1**。 接続を作成するときは、ネットワーク構成ファイルに示されている値を使用することが重要です。

次の手順では、Azure アカウントに接続し、ネットワーク構成ファイルをダウンロードして表示し、接続に必要な値を取得します。

1. Azure サービス管理 (SM) PowerShell コマンドレットの最新バージョンをダウンロードしてインストールします。 ほとんどの場合、ローカルに Resource Manager モジュールがインストールされていますが、サービス管理モジュールはありません。 サービス管理モジュールはレガシであるため、個別にインストールする必要があります。 詳細については、[サービス管理用のコマンドレットをインストールする](/powershell/azure/servicemanagement/install-azure-ps)ことに関するページを参照してください。

1. 管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続するには、次の例を参照してください。 これらのコマンドは、PowerShell サービス管理モジュールを使用してローカルで実行する必要があります。 アカウントに接続します。 接続については、次の例を参考にしてください。

   ```powershell
   Add-AzureAccount
   ```
1. アカウントのサブスクリプションを確認します。

   ```powershell
   Get-AzureSubscription
   ```
1. 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. コンピューター上にディレクトリを作成します。 たとえば、C:\AzureVNet などです
1. ネットワーク構成ファイルをそのディレクトリにエクスポートします。 この例では、ネットワーク構成ファイルは **C:\AzureNet** にエクスポートされます。

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. テキスト エディターでファイルを開き、VNet とサイトの名前を確認します。 これらの名前は、接続を作成する際に使用する名前となります。<br>**VNet** 名は **VirtualNetworkSite name =** で示されています<br>**サイト** 名は **LocalNetworkSiteRef name =** で示されています