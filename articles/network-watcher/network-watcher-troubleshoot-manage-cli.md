---
title: "Azure 仮想ネットワーク ゲートウェイと接続のトラブルシューティング - Azure CLI | Microsoft Docs"
description: "このページでは Azure Network Watcher を使用して Azure CLI のトラブルシューティングを行う方法を説明する"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 0dcc02e4f3b1e512a2c9e4b96894324414182af9
ms.lasthandoff: 03/28/2017


---

# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Azure Network Watcher Azure CLI を使用した仮想ネットワーク ゲートウェイと接続のトラブルシューティング

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher は、Azure 内のネットワーク リソースの把握に関する多くの機能を提供します。 これらの機能の 1 つが、リソースのトラブルシューティングです。 リソースのトラブルシューティングは、PowerShell、CLI、または REST API から呼び出すことができます。 呼び出されると、Network Watcher は仮想ネットワーク ゲートウェイまたは接続の正常性を検査し、その結果を返します。

この記事では、Windows、Mac、Linux で使用できるクロスプラット フォーム Azure CLI 1.0 を使います。 Network Watcher では、CLI サポートの Azure CLI 1.0 が使用されています。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。

## <a name="overview"></a>概要

リソースのトラブルシューティングを使用すると、仮想ネットワーク ゲートウェイと接続によって生じる問題をトラブルシューティングできます。 リソースのトラブルシューティングに対する要求が行われると、ログのクエリが実行され、検査されます。 検査が完了すると、結果が返されます。 リソースのトラブルシューティングの要求は長時間実行される要求であり、結果が返るまで数分かかることがあります。 トラブルシューティングのログは、指定したストレージ アカウントのコンテナーに格納されます。

## <a name="retrieve-a-virtual-network-gateway-connection"></a>仮想ネットワーク ゲートウェイ接続の取得

この例では、リソースのトラブルシューティングは接続上で実行されています。 仮想ネットワーク ゲートウェイを渡すこともできます。 次のコマンドレットは、リソース グループ内の VPN 接続を一覧表示します。

```azurecli
azure network vpn-connection list -g resourceGroupName
```

このコマンドを実行してサブスクリプション内の接続を表示することもできます。

```azurecli
azure network vpn-connection list -s subscription
```

ストレージ アカウントの名前を得たら、このコマンドを実行してアカウントのリソース ID を取得できます。

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

リソースのトラブルシューティングではリソースの正常性に関するデータを返すとともに、レビュー用にストレージ アカウントにログを保存します。 この手順では、ストレージ アカウントを作成します。既存のストレージ アカウントが存在する場合は、それを使用できます。

```azurecli
azure storage account create -n storageAccountName -l location -g resourceGroupName
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Network Watcher のリソースのトラブルシューティングの実行

リソースのトラブルシューティングは、`network watcher troubleshoot` コマンドレットを使用して行います。 このコマンドレットに、リソース グループ、Network Watcher の名前、接続の ID、ストレージ アカウントの ID、トラブルシューティングの結果を格納する BLOB のパスを渡します。

```azurecli
azure network watcher -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

コマンドレットを実行すると、Network Watcher によってリソースがレビューされて正常性が検証されます。 結果はシェルに戻され、指定したストレージ アカウントに結果のログが保存されます。

## <a name="understanding-the-results"></a>結果について

アクション テキストは、問題を解決する方法の一般的なガイダンスを提供します。 問題に対してアクションを実施できる場合は、追加のガイダンスのリンクが提供されます。 追加のガイダンスがない場合は、サポート ケースを開くための URL が応答で提供されます。  応答のプロパティと内容の詳細については、[Network Watcher のトラブルシューティングの概要](network-watcher-troubleshoot-overview.md)に関するページを参照してください。

Azure ストレージ アカウントからファイルをダウンロードする方法については、「[.NET を使用して Azure BLOB ストレージを使用する](../storage/storage-dotnet-how-to-use-blobs.md)」を参照してください。 使用できるツールとして他に Storage Explorer があります。 ストレージ エクスプローラーの詳細については、[ストレージ エクスプローラー](http://storageexplorer.com/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

設定が変更されて VPN 接続が停止される場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照して、問題がある可能性のあるネットワーク セキュリティ グループとセキュリティ規則を詳しく調べます。

