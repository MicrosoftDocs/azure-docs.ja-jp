---
title: Azure HDInsight での "クロス オリジン API のブロック" による Jupyter サーバー 404 エラー
description: Azure HDInsight での "クロス オリジン API のブロック" による Jupyter サーバーでの 404 "見つかりません" エラー
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f5ac70381f0879a896e96cb37a70c1b1a1f0b5da
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091003"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>シナリオ: Azure HDInsight での "クロス オリジン API のブロック" による Jupyter サーバーでの 404 "見つかりません" エラー

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

HDInsight 上の Jupyter サービスにアクセスするとき、"見つかりません" というエラー ボックスが表示されます。 Jupyter のログを調べると、次のような内容が見つかります。

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: hn0-pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Jupyter ログの "Origin" フィールドに IP アドレスが表示される場合もあります。

## <a name="cause"></a>原因

このエラーは、2 つのことが原因で発生する場合があります。

- ネットワーク セキュリティ グループ (NSG) ルールを構成して、クラスターへのアクセスを制限している場合。 NSG ルールでアクセスを制限しても、クラスター名ではなく IP アドレスを使って、Apache Ambari および他のサービスに直接アクセスすることができます。 ただし、Jupyter にアクセスすると、404 "Not Found" エラーが表示されることがあります。

- HDInsight ゲートウェイに、標準の `xxx.azurehdinsight.net` ではなくカスタマイズした DNS 名を指定した場合。

## <a name="resolution"></a>解決策

1. 次の 2 つの場所の jupyter.py ファイルを変更します。

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. 次のような行を探します: `NotebookApp.allow_origin='\"https://{2}.{3}\"'`。それを次のように変更します: `NotebookApp.allow_origin='\"*\"'`。

1. Ambari から Jupyter サービスを再起動します。

1. コマンド プロンプトで「`ps aux | grep jupyter`」と入力すると、任意の URL に接続が許可されていることが示されます。

これは、既に指定している設定より安全性が低くなります。 ただし、クラスターへのアクセスは制限されており、NSG が設定されているため外部からクラスターへの接続が許可されていることが前提となります。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
