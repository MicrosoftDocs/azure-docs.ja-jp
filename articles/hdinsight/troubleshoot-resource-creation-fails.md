---
title: Azure HDInsight でのリソース作成エラーをトラブルシューティングする
description: 容量に関する一般的な問題のエラーと軽減策については、この記事で説明しています。
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: bad934e02184c46c19dcda91e18e7c7ce2f1c884
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944646"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Azure HDInsight でのリソース作成エラーをトラブルシューティングする

この記事では、Azure HDInsight を操作する際の問題のトラブルシューティング手順と想定できる解決策について説明します。

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>エラー:The deployment would exceed the quota of '800' (デプロイが '800' のクォータを超えています)

Azure では、リソース グループあたり 800 デプロイのクォータ制限があります。 リソース グループ、サブスクリプション、アカウント、または他のスコープごとに異なるクォータが適用されています。 たとえば、ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 許可されているよりも多くのコアを使用して仮想マシンをデプロイしようとすると、クォータを超過したことを示すエラー メッセージが表示されます。

この問題を解決するには、Azure portal、CLI、または PowerShell を使用して、不要になったデプロイを削除します。

詳細については、「[リソース クォータのエラーを解決する](../azure-resource-manager/templates/error-resource-quota.md)」を参照してください。

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>エラー:The maximum node exceeded the available cores in this region (最大ノードがこのリージョンで使用できるコア数を超えました)

ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 許可されているよりも多くのコアを使用してリソースをデプロイしようとすると、クォータを超過したことを示すエラー メッセージが表示されます。

クォータの引き上げを依頼するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にアクセスし、 **[ヘルプとサポート]** を選択します。

1. **[新しいサポート リクエスト]** を選択します。

1. **[新しいサポート リクエスト]** ページの **[基本]** タブに次の情報を指定します。

   * **問題の種類:** **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
   * **サブスクリプション:** 変更するサブスクリプションを選択します。
   * **クォータの種類**: **[HDInsight]** を選択します。

詳細については、[サポート チケットを作成してコア数を増やす](hdinsight-capacity-planning.md#quotas)方法に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]