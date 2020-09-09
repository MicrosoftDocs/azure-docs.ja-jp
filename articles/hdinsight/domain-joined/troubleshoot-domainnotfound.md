---
title: Azure HDInsight でクラスターの作成が DomainNotFound エラーで失敗する
description: Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776052"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>シナリオ:Azure HDInsight でクラスターの作成が DomainNotFound エラーで失敗する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

HDI Secure (Enterprise セキュリティ パッケージ) クラスターの作成が `DomainNotFound` エラー メッセージで失敗する。

## <a name="cause"></a>原因

不正確な DNS 設定。

## <a name="resolution"></a>解決策

ドメインに参加しているクラスターがデプロイされると、HDI は AAD DS で (クラスターごとに) 内部ユーザー名とパスワードを作成し、すべてのクラスター ノードをこのドメインに参加させます。 ドメイン参加は、Samba ツールを使用して行われます。 次の前提条件が満たされていることを確認してください。

* ドメイン名は DNS によって解決される必要があります。
* ドメイン コントローラーの IP アドレスは、クラスターがデプロイされている仮想ネットワークの DNS 設定で設定する必要があります。
* 仮想ネットワークが AAD DS の仮想ネットワークとピアリングされている場合、これは手動で行う必要があります。
* DNS フォワーダーを使用している場合、ドメイン名は仮想ネットワーク内で正しく解決される必要があります。
* セキュリティ ポリシー (NSG) によってドメイン参加がブロックされないようにしてください。

### <a name="additional-debugging-steps"></a>追加のデバッグ手順

* 同じサブネットに Windows VM をデプロイし、ユーザー名とパスワードを使用してマシンをドメインに参加させます (これはコントロール パネル UI で行うことができます)。または、

* 同じサブネットに ubuntu VM をデプロイし、マシンをドメインに参加させます
  * マシンに SSH 接続します
  * sudo su
  * ユーザー名とパスワードを使用してスクリプトを実行します
  * スクリプトによって ping が実行され、必要な構成ファイルが作成されてからドメインが作成されます。 成功した場合、DNS 設定は適切です。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
