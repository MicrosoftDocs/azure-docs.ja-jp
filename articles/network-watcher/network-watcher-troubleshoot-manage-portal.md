---
title: "Azure 仮想ネットワーク ゲートウェイと接続のトラブルシューティング - PowerShell | Microsoft Docs"
description: "このページでは、Azure Network Watcher を使用して PowerShell コマンドレットのトラブルシューティングを行う方法を説明します。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: e135e719d8f267f6a189d0f8a903a49980a5a035
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---

# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Azure Network Watcher PowerShell を使用した仮想ネットワーク ゲートウェイと接続のトラブルシューティング

> [!div class="op_single_selector"]
> - [ポータル](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher は、Azure 内のネットワーク リソースの把握に関する多くの機能を提供します。 これらの機能の 1 つが、リソースのトラブルシューティングです。 リソースのトラブルシューティングは、ポータル、PowerShell、CLI、または REST API から呼び出すことができます。 呼び出されると、Network Watcher は仮想ネットワーク ゲートウェイまたは接続の正常性を検査し、その結果を返します。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。

サポートされるゲートウェイの種類の一覧については、「[Supported Gateway types (サポートされるゲートウェイの種類)](network-watcher-troubleshoot-overview.md#supported-gateway-types)」を参照してください。

## <a name="overview"></a>概要

リソースのトラブルシューティングを使用すると、仮想ネットワーク ゲートウェイと接続によって生じる問題をトラブルシューティングできます。 リソースのトラブルシューティングに対する要求が行われると、ログのクエリが実行され、検査されます。 検査が完了すると、結果が返されます。 リソースのトラブルシューティングの要求は長時間実行される要求であり、結果が返るまで数分かかることがあります。 トラブルシューティングのログは、指定したストレージ アカウントのコンテナーに格納されます。

## <a name="troubleshoot-a-gateway-or-connection"></a>ゲートウェイまたは接続のトラブルシューティング

1. [Azure Portal](https://portal.azure.com) に移動し、**[ネットワーク]** > **[Network Watcher]** > **[VPN 診断]** の順にクリックします。
2. **[サブスクリプション]**、**[リソース グループ]**、**[場所]** を選択します。
3. リソースのトラブルシューティングではリソースの正常性に関するデータを返すとともに、 レビュー用にストレージ アカウントにログを保存します。 **[ストレージ アカウント]** をクリックしてストレージ アカウントを選択してください。
4. **[ストレージ アカウント]** ブレードで、既存のストレージ アカウントを選択するか、または **[+ ストレージ アカウント]** をクリックして新たに作成します。
5. **[コンテナー]** ブレードで、既存のコンテナーを選択するか、または **[+ コンテナー]** をクリックして新たに作成します。 完了したら、**[選択]** をクリックします。
6. トラブルシューティングの対象となるゲートウェイ リソースと接続リソースを選択し、**[トラブルシューティングの開始]** をクリックします。

ゲートウェイ リソースに対しては、複数のリソースを選択した場合、トラブルシューティングが同時に実行されます。 接続とそれに関連付けられているゲートウェイに対して同時にトラブルシューティングを実行することはできません。 接続のトラブルシューティングの方が処理に時間がかかるため、先にゲートウェイのトラブルシューティングを行うことをお勧めします。 リソースに対して VPN 診断が実行されている場合、**[トラブルシューティングの状態]** 列に **[実行中]** というステータスが表示されます。

完了すると、ステータス列が **[正常]** または **[異常]** に変化します。

![トラブルシューティングが完了][2]

## <a name="understanding-the-results"></a>結果について

ウィンドウの **[詳細]** セクションの **[Status]\(ステータス\)** タブには、選択したリソースに対して最近実行したトラブルシューティングのステータスが表示されます。 最新の診断結果は、最後の実行から xx 分後に表示されます。

|プロパティ  |Description  |
|---------|---------|
|リソース     | リソースへのリンク。        |
|ストレージ パス     |  ログが格納されているストレージ アカウントとコンテナーのパス (実行中にログが生成された場合)。 この設定は、ポータルにとどまっている間だけ維持されます。        |
|概要     | リソース正常性の概要。        |
|詳細     | リソース正常性に関する詳しい情報。        |
|最終実行日時     | トラブルシューティングが最後に実行された時刻。        |


問題を解決する方法の一般的なガイダンスは **[アクション]** タブに表示されます。 問題に対してアクションを実施できる場合は、追加のガイダンスのリンクが提供されます。 追加のガイダンスがない場合は、サポート ケースを開くための URL が応答で提供されます。  応答のプロパティと内容の詳細については、[Network Watcher のトラブルシューティングの概要](network-watcher-troubleshoot-overview.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

設定が変更されて VPN 接続が停止される場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照して、問題がある可能性のあるネットワーク セキュリティ グループとセキュリティ規則を詳しく調べます。


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png

