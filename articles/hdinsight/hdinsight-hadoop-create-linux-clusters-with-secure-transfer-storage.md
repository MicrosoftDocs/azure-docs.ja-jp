---
title: Apache Hadoop と安全な転送のストレージ - Azure HDInsight
description: 安全な転送が有効になっている Azure ストレージ アカウントで HDInsight クラスターを作成する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: a02da7237252811d89e2c19a29f49f0bf9bb3804
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945737"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Azure HDInsight の安全な転送のストレージ アカウントを使用した Apache Hadoop クラスター

"[安全な転送が必須](../storage/common/storage-require-secure-transfer.md)" 機能は、アカウントへのすべての要求にセキュリティで保護された接続を経由するように強制することで、Azure ストレージ アカウントのセキュリティを強化します。 この機能と wasbs スキーマは、HDInsight クラスター バージョン 3.6 以降でのみサポートされます。

> [!IMPORTANT]
> クラスターの作成後にセキュリティで保護されたストレージ転送を有効にすると、ストレージ アカウントを使用する際にエラーが発生する可能性があるため、推奨されません。 セキュリティで保護された転送が既に有効になっているストレージ アカウントを使用して新しいクラスターを作成することをお勧めします。

## <a name="storage-accounts"></a>ストレージ アカウント

### <a name="azure-portal"></a>Azure portal

既定では、Azure portal でストレージ アカウントを作成すると、"安全な転送が必須" プロパティが有効になります。

Azure portal で既存のストレージ アカウントを更新するには、「[Azure portal で安全な転送を要求する](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)」を参照してください。

### <a name="powershell"></a>PowerShell

PowerShell コマンドレット [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) の場合、パラメーター `-EnableHttpsTrafficOnly` が `1` に設定されていることを確認してください。

PowerShell で既存のストレージ アカウントを更新するには、「[PowerShell を使用して安全な転送を要求する](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)」を参照してください。

### <a name="azure-cli"></a>Azure CLI

Azure CLI コマンド [az storage account create](/cli/azure/storage/account#az-storage-account-create) の場合、パラメーター `--https-only` が `true` に設定されていることを確認してください。

Azure CLI で既存のストレージ アカウントを更新するには、「[Azure CLI を使用して安全な転送を要求する](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)」を参照してください。

## <a name="add-additional-storage-accounts"></a>追加のストレージ アカウントの追加

安全な転送が有効になっている追加のストレージ アカウントを追加するには、次のようないくつかのオプションがあります。

* Azure Resource Manager テンプレートの最後のセクションを変更します。
* [Azure Portal](https://portal.azure.com) を使用してクラスターを作成し、リンクされたストレージ アカウントを指定します。
* スクリプト アクションを使用して、既存の HDInsight クラスターに安全な転送が有効になっている追加のストレージ アカウントを追加します。 詳細については、「[HDInsight にストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 既定のデータ ストアとして、[Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) ではなく Azure Storage (WASB) を使用する
* HDInsight で Azure Storage を使用する方法の詳細については、[HDInsight での Azure Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。
* データを HDInsight にアップロードする方法については、[データを HDInsight にアップロードする方法](hdinsight-upload-data.md)に関する記事を参照してください。