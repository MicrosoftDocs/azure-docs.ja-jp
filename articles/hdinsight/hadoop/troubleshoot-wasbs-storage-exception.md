---
title: アクセスされているアカウントでは Azure HDInsight の http エラーをサポートしていない
description: この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943080"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>アクセスされているアカウントでは Azure HDInsight の http エラーをサポートしていない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

次のエラー メッセージを受け取ります。

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>原因

このエラー メッセージを受け取る理由は複数あります。

* ストレージ アカウントで[セキュリティで保護された転送](../../storage/common/storage-require-secure-transfer.md)が有効であり、誤った [URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)が使用されています。

* セキュリティで保護された転送が "*無効*" なストレージ アカウントでクラスターが作成されました。 その後、ストレージ アカウントでセキュリティで保護された転送が有効になりました。

## <a name="resolution"></a>解決方法

Azure Storage または Data Lake Storage Gen2 で安全な転送が有効になっている場合、URI はそれぞれ `wasbs://` または `abfss://` になります。  [安全な転送](../../storage/common/storage-require-secure-transfer.md)に関するページも参照してください。

新しいクラスターの場合は、必要なセキュリティで保護された転送設定が既にあるストレージ アカウントを使用します。 既存のクラスターで使用中のストレージ アカウントの、セキュリティで保護された転送設定は変更しないでください。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。