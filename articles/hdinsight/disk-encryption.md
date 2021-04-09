---
title: 保存データの二重暗号化
titleSuffix: Azure HDInsight
description: この記事では、Azure HDInsight クラスターの保存データに使用できる 2 つのレイヤーでの暗号化について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 3d4f9e3be02a64efa058ea1f84a3e261cb6166fc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867119"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Azure HDInsight の保存データの二重暗号化

この記事では、Azure HDInsight クラスターの保存データを暗号化する方法について説明します。 保存データの暗号化とは、HDInsight クラスターの仮想マシンに接続されているマネージド ディスク (データ ディスク、OS ディスク、一時ディスク) に対する暗号化のことを指します。 

このドキュメントでは、Azure Storage アカウントに格納されているデータは扱いません。 クラスターには 1 つ以上の Azure Storage アカウントが接続されている場合があり、暗号化キーは Microsoft が管理することもお客様が管理することもできますが、暗号化サービスは異なります。 Azure Storage の暗号化の詳細については、「[保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)」をご参照ください。

## <a name="introduction"></a>はじめに

Azure のマネージド ディスクのロールは、主にデータ ディスク、OS ディスク、一時ディスクの 3 つです。 マネージド ディスクの種類の詳細については、「[Azure マネージド ディスクの概要](../virtual-machines/managed-disks-overview.md)」を参照してください。 

HDInsight では、次の複数の種類の暗号化が 2 つの異なるレイヤーでサポートされています。

- サーバー側暗号化 (SSE): SSE は、ストレージ サービスによって実行されます。 HDInsight で SSE は、OS ディスクとデータ ディスクの暗号化に使用されます。 共有メモリ プロトコルは既定で有効になっています。 SSE は、レイヤー 1 の暗号化サービスです。
- プラットフォーム マネージド キーを使用したホストでの暗号化: SSE と同様に、この種の暗号化はストレージ サービスによって実行されます。 ただし、これは一時ディスク専用であり、既定では有効になっていません。 ホストでの暗号化も、レイヤー 1 の暗号化サービスです。
- カスタマー マネージド キーを使用した保存時の暗号化: この種類の暗号化は、データ ディスクと一時ディスクに使用できます。 既定では有効になっておらず、Azure Key Vault を使用して顧客が独自のキーを指定する必要があります。 保存時の暗号化は、レイヤー 2 の暗号化サービスです。

これらの種類の概要は次の表のとおりです。

|クラスターの種類 |OS Disk（マネージド ディスク） |データ ディスク（マネージド ディスク） |一時データ ディスク (ローカル SSD) |
|---|---|---|---|
|Kafka、高速書き込みが可能な HBase|レイヤー 1:[SSE での暗号化](../virtual-machines/managed-disks-overview.md#encryption) (既定)|レイヤー 1:[SSE での暗号化](../virtual-machines/managed-disks-overview.md#encryption) (既定)、レイヤー 2:CMK での保存時の暗号化 (オプション)|レイヤー 1:PMK を使用したホストでの暗号化 (オプション)、レイヤー 2:CMK での保存時の暗号化 (オプション)|
|その他すべてのクラスター (高速書き込みのない Spark、Interactive、Hadoop、HBase)|レイヤー 1:[SSE での暗号化](../virtual-machines/managed-disks-overview.md#encryption) (既定)|該当なし|レイヤー 1:PMK を使用したホストでの暗号化 (オプション)、レイヤー 2:CMK での保存時の暗号化 (オプション)|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>カスタマー マネージド キーを使用した保存時の暗号化

カスタマー マネージド キーの暗号化は、追加コストなしでクラスターの作成中に処理される 1 つのステップからなるプロセスです。 必要な作業は、Azure Key Vault でマネージド ID を承認し、自分のクラスターの作成時に暗号化キーを追加するだけです。

クラスター上の各ノードのデータ ディスクと一時ディスクはいずれも、対称データ暗号化キー (DEK) を使用して暗号化されます。 DEK は、キー コンテナーからの KEK (キー暗号化キー) を使用して保護されます。 暗号化と復号のプロセスはすべて、Azure HDInsight によって処理されます。

クラスターの VM に接続されている OS ディスクは、1 レイヤーでのみ暗号化 (PMK) できます。 ユーザーのシナリオで CMK で暗号化する必要がある場合、OS ディスクに機密データをコピーしないようにすることをお勧めします。

ディスク暗号化キーが格納されているキー コンテナーでキー コンテナー ファイアウォールが有効になっている場合、クラスターをデプロイするリージョンの HDInsight リージョン リソース プロバイダーの IP アドレスを、キー コンテナーのファイアウォール構成に追加する必要があります。 これは、HDInsight が信頼された Azure キー コンテナー サービスではないために必要です。

Azure portal または Azure CLI を使用し、キー コンテナーのキーを安全にローテーションすることができます。 キーをローテーションすると、HDInsight クラスターは数分以内に新しいキーの使用を開始します。 ランサムウェア シナリオと誤削除の対策として、 [論理的な削除](../key-vault/general/soft-delete-overview.md) キー保護機能を有効にします。 この保護機能のないキー コンテナーはサポートされていません。

### <a name="get-started-with-customer-managed-keys"></a>ユーザーが管理するキーを使用する

カスタマー マネージド キーが有効になった HDInsight クラスターを作成するには、次の手順を実行します。

1. Azure リソースのマネージド ID を作成する
1. Azure Key Vault を作成する
1. キーを作成する
1. アクセス ポリシーの作成
1. カスタマー マネージド キーを有効にして HDInsight クラスターを作成する
1. 暗号化キーを入れ替える

各手順については、次の各セクションで説明します。

### <a name="create-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID を作成する

Key Vault に対して認証するユーザー割り当てのマネージド ID を作成します。

具体的な手順については、 「[ユーザー割り当てマネージド ID の作成](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 」をご参照ください。 Azure HDInsight でマネージド ID がどのように機能するかに関する詳細は、「[Azure HDInsight のマネージド ID](hdinsight-managed-identities.md)」を参照してください。 キー コンテナー アクセス ポリシーに追加するときのために、マネージド ID のリソース ID を保存してください。

### <a name="create-azure-key-vault"></a>Azure Key Vault を作成する

Key Vault を作成します。 具体的な手順については、「[ Azure Key Vault の作成](../key-vault/general/quick-create-portal.md) 」をご参照ください。

HDInsight では、Azure Key Vault にのみ対応しています。 自分のキー コンテナーをお持ちの場合、Azure Key Vault に自分のキーをインポートできます。 キー コンテナーでは、 **論理的な削除** を有効にする必要があります。 既存のキーをインポートする方法については、「[キー、シークレット、証明書について](../key-vault/general/about-keys-secrets-certificates.md)」をご覧ください。

### <a name="create-key"></a>キーを作成する

1. 新しいキー コンテナーから、 **設定** > **キー** >  **+ 生成/インポート** に移動します。

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Azure Key Vault で新しいキーを生成する":::

1. 名前を入力し、 **作成** を選択します。 **RSA** のデフォルトの **キー タイプ** を維持します。

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="キー名を生成する":::

1. **キー** ページに戻り、作成したキーを選択します。

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="キー コンテナーのキーの一覧":::

1. バージョンを選択すると、 **キー バージョン** ページが開きます。 HDInsight クラスターの暗号化に独自のキーを使用する場合は、キーの URI を指定する必要があります。 **キー識別子** をコピーし、クラスターを作成する準備ができるまでどこかに保存します。

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="キー識別子を取得する":::

### <a name="create-access-policy"></a>アクセス ポリシーの作成

1. 新しいキー コンテナーから、 **設定** > **アクセス ポリシー** >  **+ アクセス ポリシーの追加** に移動します。

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="新しい Azure Key Vault アクセス ポリシーを作成する":::

1. **アクセス ポリシーの追加** ページで、次の情報を指定します。

    |プロパティ |説明|
    |---|---|
    |キーのアクセス許可|**取得** を選択し、 **キーのラップを解除** してから、 **キーををラップ** します。|
    |シークレットのアクセス許可|**取得**、 **設定**、 **削除** を選択します。|
    |プリンシパルの選択|以前に作成したユーザー割り当てマネージド ID を選択します。|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="Azure Key Vault アクセス ポリシーの [プリンシパルの選択] を設定する":::

1. **[追加]** を選択します。

1. **[保存]** を選択します。

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Azure Key Vault アクセス ポリシーを保存する":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>カスタマー マネージド キー ディスク暗号化を使用してクラスターを作成する

これで新しい HDInsight クラスターを作成する準備が整いました。 カスタマー マネージド キーは、クラスター作成時に新しいクラスターにのみ適用できます。 カスタマー マネージド キー クラスターからは暗号化を削除することはできず、カスタマー マネージド キーは既存のクラスターには追加できません。

2020 年 11 月のリリース以降、HDInsight では、バージョン管理されているものとバージョンレスの両方のキー URI を使用したクラスター作成がサポートされています。 バージョンレスのキー URI でクラスターを作成すると、HDInsight クラスターでは、Azure Key Vault でキーが更新されたときにキーの自動ローテーションが試行されます。 バージョン管理されたキー URI でクラスターを作成する場合は、「[暗号キーを入れ替える](#rotating-the-encryption-key)」で説明されているように、手動でキー ローテーションを実行する必要があります。

2020 年 11 月のリリースより前に作成されたクラスターでは、バージョン管理されたキー URI を使用して手動でキー ローテーションを実行する必要があります。

#### <a name="using-the-azure-portal"></a>Azure ポータルの使用

クラスターの作成時、次の方法でバージョン管理されたキー、またはバージョンレス キーを使用できます。

- **バージョン管理されている** - クラスター作成時、キーのバージョンを含む完全な **キー識別子** を指定します。 たとえば、「 `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4` 」のように入力します。
- **バージョンレス** - クラスターの作成時、**キー識別子** だけを指定します。 たとえば、「 `https://contoso-kv.vault.azure.net/keys/myClusterKey` 」のように入力します。

また、クラスターにマネージド ID を割り当てる必要もあります。

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="新しいクラスターを作成する":::

#### <a name="using-azure-cli"></a>Azure CLI の使用

次の例では、Azure CLI を使用して、ディスク暗号化が有効になった新しい Apache Spark クラスターを作成する方法を示します。 詳細については、「 [Azure CLI az hdinsight の作成](/cli/azure/hdinsight#az-hdinsight-create)」をご参照ください。 パラメーター `encryption-key-version` は省略可能です。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートを作成する

次の例では、Azure Resource Manager テンプレートを使用して、ディスク暗号化が有効になっている新しい Apache Spark クラスターを作成する方法を示します。 詳細については、「[ARM テンプレートとは](../azure-resource-manager/templates/overview.md)」を参照してください。 リソース マネージャー テンプレート プロパティ `diskEncryptionKeyVersion` は省略可能です。

この例では、PowerShell を使用してテンプレートを呼び出します。

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

リソース管理テンプレート `azuredeploy.json` の内容は、次のとおりです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>暗号化キーを入れ替える

Azure portal または Azure CLI を使用して、実行中のクラスターで使用されている暗号化キーを変更できます。 この操作を行うには、クラスターが現在のキーと目的の新しいキーの両方にアクセスできる必要があり、そうでないとキーの交換操作は失敗します。 2020 年 11 月のリリースより後に作成されたクラスターの場合は、新しいキーにバージョンを含めるかどうかを選択できます。 2020 年 11 月のリリースより前に作成されたクラスターの場合は、暗号化キーをローテーションするときに、バージョン管理されたキーを使用する必要があります。

#### <a name="using-the-azure-portal"></a>Azure ポータルの使用

キーを回転させるには、ベース キー コンテナー URI が必要です。 これが完了したら、ポータルで HDInsight クラスターのプロパティ セクションに移動し、 **[ディスクの暗号化キーの URL]** で **[キーの変更]** をクリックします。 新しいキーの URL を入力し、キーの交換を実行します。

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="ディスク暗号化キーのローテーション":::

#### <a name="using-azure-cli"></a>Azure CLI の使用

次の例では、既存の HDInsight クラスターでディスク暗号化キーをローテーションする方法を示します。 詳細については、「 [Azure CLI az hdinsight rotate-disk-encryption-key](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key)」をご参照ください。

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>カスタマー マネージド キーの暗号化に関するよくあるご質問

**HDInsight クラスターは、どのようにしてキー コンテナーにアクセスしますか?**

Hdinsight は、HDInsight クラスターに関連するマネージド ID を使用して Azure Key Vault インスタンスにアクセスします。 このマネージド ID は、クラスターの作成前または作成中に作成できます。 また、キーが格納されているキー コンテナーへのアクセスをマネージド ID に付与する必要があります。

**この機能は、HDInsight のすべてのクラスターで利用できますか?**

カスタマー マネージド キーの暗号化は、Spark 2.1 と 2.2 を除くすべてのクラスターの種類で利用できます。

**異なるディスクまたはフォルダーを暗号化するために、複数のキーを使用できますか?**

いいえ。すべてのマネージド ディスクとリソース ディスクは、同じキーで暗号化されます。

**クラスターがキー　コンテナーまたはキーにアクセスできなくなった場合はどうなりますか。**

クラスターがキーへのアクセスを失うと、Apache Ambari ポータルに警告が表示されます。 この状態では、**キーの変更** 操作は失敗します。 キー アクセスが復元されると、Ambari の警告が消え、キーの交換などの操作を正常に実行できます。

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="キー アクセス Ambari アラート":::

**キーを削除した場合、どのようにしてクラスターを復元しますか?**

「論理的な削除」対応のキーのみがサポートされているため、キーがキー コンテナー内で回復された場合、クラスターはキーへ再びアクセスできるようになります。 Azure Key Vault キーを回復するには、「[Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval)」または「[az-keyvault-key-recover](/cli/azure/keyvault/key#az-keyvault-key-recover)」に関するページを参照してください。


**クラスターがスケールアップされた場合、新しいノードでは、カスタマー マネージド キーはシームレスにサポートされますか?**

はい。 スケール アップ中、クラスターはキー コンテナー内のキーにアクセスする必要があります。 この同じキーを使用して、クラスター内のマネージド ディスクとリソース ディスクの両方が暗号化されます。

**私の所在地でカスタマー マネージド キーを使用できますか?**

HDInsight のカスタマー マネージド キーは、すべてのパブリック クラウドと各国のクラウドでご利用いただけます。

## <a name="encryption-at-host-using-platform-managed-keys"></a>プラットフォーム マネージド キーを使用したホストでの暗号化

### <a name="enable-in-the-azure-portal"></a>Azure Portal で有効にする

ホストでの暗号化は、Azure portal でクラスターを作成するときに有効にできます。

> [!Note]
> ホストでの暗号化を有効にした場合、お使いの HDInsight クラスターに Azure Marketplace からアプリケーションを追加することはできません。

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="ホストでの暗号化の有効化。":::

このオプションでは、PMK を使用し、HDInsight の VM の一時データ ディスクに[ホストでの暗号化](../virtual-machines/disks-enable-host-based-encryption-portal.md)を有効にします。 ホストでの暗号化は、[限られたリージョンの特定の VM SKU でのみサポートされています](../virtual-machines/disks-enable-host-based-encryption-portal.md)。HDInsight では、[このノード構成と SKU](./hdinsight-supported-node-configuration.md) がサポートされています。

お使いの HDInsight クラスターに適切な VM サイズを確認するには、「[Azure HDInsight クラスターの適切な VM サイズの選択](hdinsight-selecting-vm-size.md)」を参照してください。 ホストでの暗号化が有効になっている場合、Zookeeper ノードでの既定の VM SKU は DS2V2 になります。

### <a name="enable-using-powershell"></a>PowerShell を使用した有効化

次に、PowerShell を使用した、ホストでの暗号化が有効になっている新しい Azure HDInsight クラスターを作成するコード スニペットを示します。 ここでは、パラメーター `-EncryptionAtHost $true` を使用してこの機能を有効にしています。

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Azure CLI を使用して有効にする

次に、Azure CLI を使用した、ホストでの暗号化が有効になっている新しい Azure HDInsight クラスターを作成するコード スニペットを示します。 ここでは、パラメーター `--encryption-at-host true` を使用してこの機能を有効にしています。

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>次のステップ

* Azure Key Vault の詳細については、「[Azure Key Vault とは](../key-vault/general/overview.md)」をご覧ください。
* [Azure HDInsight のエンタープライズ セキュリティの概要](./domain-joined/hdinsight-security-overview.md)。
