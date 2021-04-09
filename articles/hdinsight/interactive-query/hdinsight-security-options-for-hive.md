---
title: Azure HDInsight の Hive のセキュリティ オプション
description: 標準および ESP クラスターの Hive のセキュリティ オプション。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: a608c34225641a3c7764d6c7dd3872c5f61fe3c8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869720"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Azure HDInsight の Hive のセキュリティ オプション

このドキュメントでは、HDInsight の Hive で推奨されるセキュリティ オプションについて説明します。 これらのオプションは、Ambari を使用して構成できます。

:::image type="content" source="./media/hdinsight-security-options-for-hive/security-options-hive.png " alt-text="Hive のセキュリティ オプション" border="true":::

## <a name="hiveserver2-authentication"></a>HiveServer2 認証

標準クラスターの場合、HiveServer2 認証で推奨される設定は既定値の [なし] です。 認証を有効にするには、[ESP](../domain-joined/hdinsight-security-overview.md) (Enterprise セキュリティ パッケージ) クラスターにアップグレードすることが推奨されています。 

ESP クラスターの場合、規定で [Kerberos](https://web.mit.edu/Kerberos/) 認証が有効になっています。 Pluggable Authentication Module (PAM) とカスタム認証スキームはサポートされていません。

## <a name="hiveserver2-authorization"></a>HiveServer2 認可

標準クラスターの場合、既定の設定は [なし] です。 [SqlStdAuth (SQL Standards Based Authorization)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) を有効にすることができます。 [Apache Ranger](https://ranger.apache.org/) を使用した認可は、標準クラスターでサポートされていません。 Ranger 認可を行うために、ESP クラスターにアップグレードすることが推奨されています。 

ESP クラスターの場合、既定で Ranger を使用した認可が有効になっています。 


## <a name="ssl-encryption-for-hiveserver2"></a>HiveServer2 の SSL 暗号化

標準クラスターまたは ESP クラスターの場合、Hiveserver2 SSL を有効にすることが推奨されていません。 代わりに、SSL がゲートウェイで有効になっています。 [転送中の暗号化](../domain-joined/encryption-in-transit.md)を有効にすると、[インターネット プロトコル セキュリティ (IPSec)](https://en.wikipedia.org/wiki/IPsec) を使用してクラスター ノード間の通信を暗号化できます。


## <a name="next-steps"></a>次の手順
* [HiveServer2 認証の概要](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [HiveServer2 認可の概要](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [SQL 標準ベースの Hive 認可を有効にする](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Hive を使用した Apache Ranger](../domain-joined/apache-domain-joined-run-hive.md)
