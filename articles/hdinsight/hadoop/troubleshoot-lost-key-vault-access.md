---
title: ディスク暗号化を使用する Azure HDInsight クラスターにおいて Key Vault のアクセスが失われる
description: Azure HDInsight クラスターと対話するときの Key Vault アクセスの問題のトラブルシューティング手順と可能な解決策。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: ce2929ca84746de1ab8b51882f3004c3699f17ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943116"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>シナリオ:ディスク暗号化を使用する Azure HDInsight クラスターにおいて Key Vault のアクセスが失われる

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Resource Health Center (RHC) のアラート `The HDInsight cluster is unable to access the key for BYOK encryption at rest` は、クラスター ノードにおいて顧客の Key Vault (KV) へのアクセスが失われた Bring Your Own Key (BYOK) クラスターに対して表示されます。 同様のアラートは、Apache Ambari UI 上でも確認できます。

## <a name="cause"></a>原因

アラートでは、クラスター ノードから KV にアクセスでき、それによってネットワーク接続、KV 正常性、ユーザー割り当てマネージド ID のアクセス ポリシーが保証される状態を確保します。 このアラートでは単に、構造ノードの再起動時にブローカーがシャットダウンされる兆候を警告しており、ノードが再起動されるまでクラスターは引き続き機能します。

**ディスク暗号化の Key Vault ステータス** からのアラートに関する詳細情報を検索するには、Apache Ambari UI に移動します。 このアラートには、検証エラーの理由に関する詳細が含まれます。

## <a name="resolution"></a>解像度

### <a name="kvaad-outage"></a>KV/AAD の停止

詳細については、「[Azure Azure Key Vault の可用性と冗長性](../../key-vault/general/disaster-recovery-guidance.md)」と Azure ステータスに関するページ (https://status.azure.com/ ) を参照してください。

### <a name="kv-accidental-deletion"></a>KV の誤削除

* 削除されたキーを KV 上にリストアして、自動復元する。 詳細については、「[削除されたキーの復元](/rest/api/keyvault/recoverdeletedkey)」を参照してください。
* KV チームに連絡を取って、誤削除から復元してもらう。

### <a name="kv-access-policy-changed"></a>KV アクセス ポリシーが変更された

KV にアクセスするために HDI クラスターに割り当てられたユーザー割り当てマネージド ID のアクセス ポリシーを復元します。

### <a name="key-permitted-operations"></a>キーの許可される操作

KV の各キーに対して、許可される一連の操作を選択できます。 BYOK キーにおいて、wrap および unwrap 操作が有効になっていることを確認してください

### <a name="expired-key"></a>キーの期限切れ

有効期限が切れてキーがローテーションされない場合、バックアップ HSM からキーを復元するか、KV チームに連絡を取って有効期限日をクリアします。

### <a name="kv-firewall-blocking-access"></a>KV ファイアウォールによるアクセス ブロック

BYOK クラスター ノードから KV へのアクセスを許可するように、KV ファイアウォール設定を変更します。

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>仮想ネットワーク上の NSG 規則によるアクセス ブロック

クラスターに接続されている仮想ネットワークに関連付けられた NSG 規則を確認します。

## <a name="mitigation-and-prevention-steps"></a>軽減策と防止策の手順

### <a name="kv-accidental-deletion"></a>KV の誤削除

* [リソースのロック設定](../../azure-resource-manager/management/lock-resources.md)を利用して Key Vault を構成します。
* ハードウェア セキュリティ モジュールにキーをバックアップします。

### <a name="key-deletion"></a>キーの削除

キー削除の前に、クラスターが削除されている必要があります。

### <a name="kv-access-policy-changed"></a>KV アクセス ポリシーが変更された

アクセス ポリシーを定期的に監査およびテストします。

### <a name="expired-key"></a>キーの期限切れ

* ご自身の HSM にキーをバックアップします。
* 有効期限の設定がないキーを使用します。
* 有効期限の設定が必要な場合は、有効期限日より前にキーをローテーションします。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。