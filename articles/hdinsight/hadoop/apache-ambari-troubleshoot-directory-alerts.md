---
title: Azure HDInsight での Apache Ambari のディレクトリに関するアラート
description: HDInsight での Apache Ambari のディレクトリに関するアラートについて、考えられる理由と解決策の説明および分析。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 47d1f9797a44d7dc918677c21ffc7a124808525d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943292"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>シナリオ:Azure HDInsight での Apache Ambari のディレクトリに関するアラート

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari から次のようなエラーが表示されます。

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>原因

Ambari アラートで言及されているディレクトリが、影響を受けるワーカー ノードにありません。

## <a name="resolution"></a>解像度

欠落しているディレクトリを、影響を受けるワーカー ノードに手動で作成します。

1. 関連するワーカー ノードに SSH 接続します。

1. ルート ユーザーを取得します。`sudo su`

1. 必要なディレクトリを再帰的に作成します。

1. これらのディレクトリの所有者とグループを変更します。

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. Apache Ambari UI で、アラートを無効にしてから有効にします。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]