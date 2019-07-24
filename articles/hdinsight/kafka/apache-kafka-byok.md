---
title: Azure HDInsight で Apache Kafka 用に自分のキーを持ち込む (プレビュー)
description: この記事では、Azure Key Vault から自分のキーを使用し、Azure HDInsight で Apache Kafka に保存されているデータを暗号化する方法について説明します。
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cb18f0e1b682434c5069c2a02524a6f16551e9e2
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545979"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Azure HDInsight で Apache Kafka 用に自分のキーを持ち込む

Azure HDInsight では、Apache Kafka の BYOK (Bring Your Own Key) に対応しています。 この機能では、保存データの暗号化に使用するキーを所有し、管理できます。 

HDInsight のマネージド ディスクはすべて、Azure Storage Service Encryption (SSE) で保護されます。 既定では、これらのディスク上のデータは、Microsoft が管理するキーを使用して暗号化されます。 BYOK を有効にした場合は、Azure Key Vault で使用し、管理する目的で HDInsight の暗号化キーを指定します。 

BYOK 暗号化は 1 つのステップからなるプロセスであり、クラスター作成中に処理されます。追加コストはかかりません。 必要な作業は、HDInsight をマネージド ID として Azure Key Vault に登録し、クラスターの作成時に暗号化キーを追加することだけです。

Kafka クラスターに送信されるメッセージはすべて、Kafka で保守管理されるレプリカも含め、対称的 DEK (データ暗号化キー) で暗号化されます。 DEK は、キー コンテナーからの KEK (キー暗号化キー) を使用して保護されます。 暗号化と復号のプロセスはすべて、Azure HDInsight によって処理されます。 

Azure portal または Azure CLI を使用し、キー コンテナーのキーを安全にローテーションすることができます。 キーをローテーションすると、HDInsight Kafka クラスターはすぐに新しいキーの使用を開始します。 ランサムウェア シナリオと誤削除の対策として、[論理的な削除] キー保護機能を有効にします。 この保護機能のないキー コンテナーはサポートされていません。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>BYOK を使用する
BYOK が有効な Kafka クラスターを作成するには、次の手順を行う必要があります。
1. Azure リソースのマネージド ID を作成する
2. Azure Key Vault とキーを設定する
3. BYOK を有効にして HDInsight Kafka クラスターを作成する
4. 暗号化キーを入れ替える

## <a name="create-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID を作成する

   キー コンテナーに認証するには、[Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)、[Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)、[Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)、[Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) を使用し、ユーザー割り当てマネージド ID を作成します。 Azure HDInsight でマネージド ID がどのように機能するかに関する詳細は、「[Managed identities in Azure HDInsight (Azure HDInsight のマネージド ID)](../hdinsight-managed-identities.md)」を参照してください。 マネージド ID と Kafka の BYOK には Azure Active Directory が必須ですが、ESP (Enterprise セキュリティ パッケージ) は必須ではありません。 キー コンテナー アクセス ポリシーに追加するときのために、マネージド ID のリソース ID を保存してください。

   ![Azure portal でユーザー割り当てマネージド ID を作成する](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Key Vault とキーを設定する

   HDInsight では、Azure Key Vault にのみ対応しています。 自分のキー コンテナーをお持ちの場合、Azure Key Vault に自分のキーをインポートできます。 キーでは [論理的な削除] を有効にする必要があります。 [論理的な削除] 機能は、REST、.NET/C#、PowerShell、Azure CLI の各インターフェイスで使用できます。

   1. 新しいキー コンテナーを作成するには、[Azure Key Vault](../../key-vault/key-vault-overview.md) クイック スタートに従ってください。 既存のキーをインポートする方法については、「[キー、シークレット、証明書について](../../key-vault/about-keys-secrets-and-certificates.md)」をご覧ください。

   2. [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI コマンドを使用して、キー コンテナーで [論理的な削除] を有効にします。
        ```Azure CLI az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.

        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save**. 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## Rotating the Encryption key
   There might be scenarios where you might want to change the encryption keys used by the Kafka cluster after it has been created. This can be easily via the portal. For this operation, the cluster must have access to both the current key and the intended new key, otherwise the rotate key operation will fail.

   To rotate the key, you must have the full url of the new key (See Step 3 of [Setup the Key Vault and Keys](#setup-the-key-vault-and-keys)). Once you have that, go to the Kafka cluster properties section in the portal and click on **Change Key** under **Disk Encryption Key URL**. Enter in the new key url and submit to rotate the key.

   ![Kafka rotate disk encryption key](./media/apache-kafka-byok/kafka-change-key.png)

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**What happens if the cluster loses access to the key vault or the key?**
   If the cluster loses access to the key, warnings will be shown in the Ambari portal. In this state, the **Change Key** operation will fail. Once key access is restored, ambari warnings will go away and operations such as key rotation can be successfully performed.

   ![Kafka key access ambari alert](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
