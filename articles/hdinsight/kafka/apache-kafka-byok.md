---
title: Azure HDInsight で Apache Kafka 用に自分のキーを持ち込む
description: この記事では、Azure Key Vault から自分のキーを使用し、Azure HDInsight で Apache Kafka に保存されているデータを暗号化する方法について説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 15638d90fe24938a45f6d4cce156e998f1f9afc2
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000102"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Azure HDInsight で Apache Kafka 用に自分のキーを持ち込む

Azure HDInsight では、Apache Kafka の BYOK (Bring Your Own Key) に対応しています。 この機能では、保存データの暗号化に使用するキーを所有し、管理できます。

HDInsight のマネージド ディスクはすべて、Azure Storage Service Encryption (SSE) で保護されます。 既定では、これらのディスク上のデータは、Microsoft が管理するキーを使用して暗号化されます。 BYOK を有効にした場合は、Azure Key Vault で使用し、管理する目的で HDInsight の暗号化キーを指定します。

BYOK 暗号化は 1 つのステップからなるプロセスであり、クラスター作成中に処理されます。追加コストはかかりません。 必要な作業は、HDInsight をマネージド ID として Azure Key Vault に登録し、クラスターの作成時に暗号化キーを追加することだけです。

Kafka クラスターに送信されるメッセージはすべて、Kafka で保守管理されるレプリカも含め、対称的 DEK (データ暗号化キー) で暗号化されます。 DEK は、キー コンテナーからの KEK (キー暗号化キー) を使用して保護されます。 暗号化と復号のプロセスはすべて、Azure HDInsight によって処理されます。 

Azure portal または Azure CLI を使用し、キー コンテナーのキーを安全にローテーションすることができます。 キーをローテーションすると、HDInsight Kafka クラスターはすぐに新しいキーの使用を開始します。 ランサムウェア シナリオと誤削除の対策として、[論理的な削除] キー保護機能を有効にします。 この保護機能のないキー コンテナーはサポートされていません。

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
        ```Azure CLI
        az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. キーの作成

        a. 新しいキーを作成するには、 **[設定]** の下にある **[キー]** メニューから **[生成/インポート]** を選択します。

        ![Azure Key Vault で新しいキーを生成する](./media/apache-kafka-byok/kafka-create-new-key.png "Azure Key Vault で新しいキーを生成する")

        b. **[オプション]** を **[生成]** に設定し、キーの名前を付けます。

        ![キー名を生成する](./media/apache-kafka-byok/apache-kafka-create-key.png "キー名を生成する")

        c. キーの一覧から作成したキーを選択します。

        ![Azure Key Vault キーのリスト](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. Kafka クラスターの暗号化に独自のキーを使用する場合は、キーの URI を指定する必要があります。 **キー識別子**をコピーし、クラスターを作成する準備ができるまでどこかに保存します。

        ![キー識別子をコピーする](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. キー コンテナーのアクセス ポリシーにマネージド ID を追加します。

        a. 新しい Azure Key Vault アクセス ポリシーを作成します。

        ![新しい Azure Key Vault アクセス ポリシーを作成する](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. **[プリンシパルの選択]** の下で、作成したユーザー割り当てマネージド ID を選択します。

        ![Azure Key Vault アクセス ポリシーの [プリンシパルの選択] を設定する](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. **[キーのアクセス許可]** を **[取得]** 、 **[キーの折り返しを解除]** 、 **[キーを折り返す]** に設定します。

        ![Azure Key Vault アクセス ポリシーにキーのアクセス許可を設定する 1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Azure Key Vault アクセス ポリシーにキーのアクセス許可を設定する 1")

        d. **[シークレットのアクセス許可]** を **[取得]** 、 **[設定]** 、 **[削除]** に設定します。

        ![Azure Key Vault アクセス ポリシーにキーのアクセス許可を設定する 2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Azure Key Vault アクセス ポリシーにキーのアクセス許可を設定する 2")

        e. **[Save]** をクリックします。 

        ![Azure Key Vault アクセス ポリシーを保存する](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>HDInsight クラスターの作成

   これで新しい HDInsight クラスターを作成する準備が整いました。 BYOK は、クラスター作成時、新しいクラスターにのみ適用できます。 BYOK クラスターから暗号化を削除することはできません。既存のクラスターに BYOK を追加することはできません。

   ![Azure portal の Kafka ディスク暗号化](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   クラスター作成時、キーのバージョンも含む、完全キー URL を指定します。 たとえば、「 `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4` 」のように入力します。 また、クラスターにマネージド ID を割り当て、キー URI を指定する必要があります。

## <a name="rotating-the-encryption-key"></a>暗号化キーを入れ替える
   暗号化キーが作成された後で、Kafka クラスターによって使用される暗号化キーを変更することが必要になる場合があります。 これはポータルを使用して簡単に行うことができます。 この操作を行うには、クラスターが現在のキーと目的の新しいキーの両方にアクセスできる必要があり、そうでないとキーの交換操作は失敗します。

   キーを交換するには、新しいキーの完全な URL が必要です (「[Key Vault とキーを設定する](#setup-the-key-vault-and-keys)」の手順 3 を参照してください)。 これを入手したら、ポータルで Kafka クラスターのプロパティ セクションに移動し、 **[ディスクの暗号化キーの URL]** の **[キーの変更]** をクリックします。 新しいキーの URL を入力し、キーの交換を実行します。

   ![Kafka のディスク暗号化キーの交換](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Apache Kafka の BYOK についてよくあるご質問

**Kafka クラスターはキー コンテナーにどのようにしてアクセスしますか?**

   クラスター作成時、マネージド ID と HDInsight Kafka クラスターを関連付けます。 このマネージド ID は、クラスターの作成前または作成中に作成できます。 また、キーが格納されているキー コンテナーへのアクセスをマネージド ID に付与する必要があります。

**HDInsight では、すべての Kafka クラスターでこの機能を利用できますか?**

   BYOK 暗号化は Kafka 1.1 以上のクラスターで可能です。

**異なるトピック/パーティションに対して異なるキーを持つことができますか?**

   いいえ。クラスター内のマネージド ディスクはすべて同じキーで暗号化されます。

**クラスターがキー　コンテナーまたはキーにアクセスできなくなった場合はどうなりますか。**
クラスターがキーへのアクセスを失うと、Apache Ambari ポータルに警告が表示されます。 この状態では、**キーの変更**操作は失敗します。 キー アクセスが復元されると、Ambari の警告が消え、キーの交換などの操作を正常に実行できます。

   ![Kafka キー アクセス Ambari アラート](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**キーを削除した場合、どのようにしてクラスターを復元しますか?**

   "論理的な削除" 対応のキーのみがサポートされているため、キーがキー コンテナー内で回復された場合、クラスターはキーへ再びアクセスできるようになります。 Azure Key Vault キーを回復するには、「[Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval)」または「[az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)」に関するページを参照してください。

**BYOK クラスターと非 BYOK クラスターで同時にプロデューサー/コンシューマー アプリケーションを動かすことはできますか?**

   はい。 BYOK はプロデューサー/コンシューマー アプリケーションで透過的に使用されます。 暗号化は OS 層で行われます。 既存のプロデューサー/コンシューマー Kafka アプリケーションを変更する必要はありません。

**OS ディスク/リソース ディスクも暗号化されますか?**

   いいえ。 OS ディスクとリソース ディスクは暗号化されません。

**クラスターをスケール アップするとき、新しいブローカーは BYOK をシームレスにサポートしますか?**

   はい。 スケール アップ中、クラスターはキー コンテナー内のキーにアクセスする必要があります。 この同じキーを使用し、クラスター内のすべてのマネージド ディスクが暗号化されます。

**BYOK は私の住んでいる地域で利用できますか?**

   Kafka BYOK はあらゆるパブリック クラウドでご利用いただけます。

## <a name="next-steps"></a>次の手順

* Azure Key Vault の詳細については、「[Azure Key Vault とは](../../key-vault/key-vault-overview.md)」をご覧ください。
* Azure Key Vault の概要については、「[Azure Key Vault の概要](../../key-vault/key-vault-overview.md)」を参照してください。
