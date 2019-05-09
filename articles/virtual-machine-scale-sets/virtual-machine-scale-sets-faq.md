---
title: Azure Virtual Machine Scale Sets の FAQ | Microsoft Docs
description: 仮想マシン スケール セットについてよく寄せられる質問の回答を示します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: manayar
ms.custom: na
ms.openlocfilehash: 56a31770c374cdccaec4dbee751925a6da00fa59
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683955"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Azure Virtual Machine Scale Sets の FAQ

Azure における仮想マシン スケール セットについてよく寄せられる質問の回答を示します。

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Scale Sets に関してよく寄せられる質問

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Scale Sets には何個の VM を設定できますか?

スケール セットには、プラットフォーム イメージに基づいて 0 から 1,000 個の VM、またはカスタム イメージに基づいて 0 から 600 個の VM を含めることができます。

### <a name="are-data-disks-supported-within-scale-sets"></a>Scale Sets 内でデータ ディスクはサポートされていますか?

はい。 スケール セットでは、セット内のすべての VM に適用される、接続されたデータ ディスクの構成を定義できます。 詳細については、[Azure Scale Sets と接続されたデータ ディスク](virtual-machine-scale-sets-attached-disks.md)に関するページをご覧ください。 データを格納するための他のオプションを次に示します。

* Azure ファイル (SMB 共有ドライブ)
* OS ドライブ
* 一時ドライブ (ローカル、Azure Storage に保存されない)
* Azure データ サービス (Azure テーブル、Azure BLOB など)
* 外部データ サービス (リモート データベースなど)

### <a name="which-azure-regions-support-scale-sets"></a>Scale Sets は、どの Azure リージョンでサポートされていますか?

すべてのリージョンで Scale Sets がサポートされています。

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>カスタム イメージを使用して Scale Sets を作成するにはどうすればよいですか?

VM イメージを作成しキャプチャしてから、それをスケール セットのソースとして使用します。 カスタム VM イメージを作成して使用する方法のチュートリアルについては、[Azure CLI](tutorial-use-custom-image-cli.md) または [Azure PowerShell](tutorial-use-custom-image-powershell.md) に関するページを参照してください。

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Scale Sets 容量を 20 から 15 に減らすと、どの VM が削除されますか?

可用性を最大限に高めるために、仮想マシンは、すべての更新ドメインと障害ドメインのスケール セットから均等に削除されます。 ID が最大の VM が最初に削除されます。

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>その後、容量を 15 から 18 に増やすとどうなりますか。

容量を 18 に増やすと、3 つの新しい VM が作成されます。 VM が作成されるたびに、VM インスタンス ID は前の最大値に増分された値となります (例: 20、21、22)。 VM は障害ドメインと更新ドメインに分散されます。

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Scale Sets で複数の拡張機能を使用する場合、実行順序を強制できますか?

はい、スケール セット[拡張機能のシーケンス処理](virtual-machine-scale-sets-extension-sequencing.md)を使用できます。

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Scale Sets は、Azure 可用性セットと連携できますか?

リージョン (非ゾーン) スケール セットは*配置グループ*を使用します。配置グループは、5 つの障害ドメインと 5 つの更新ドメインを使用する暗黙的な可用性セットとして機能します。 100 を超える VM のスケール セットは複数の配置グループにまたがります。 配置グループの詳細については、「[大規模な Virtual Machine Scale Sets の使用](virtual-machine-scale-sets-placement-groups.md)」をご覧ください。 VM の可用性セットは、VM Scale Sets と同じ VNET に存在できます。 一般的な構成では、(多くの場合、可用性セットに固有の構成を必要とする) 制御ノード VM とデータ ノードを Scale Sets に配置します。

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>スケール セットは、Azure 可用性ゾーンと連携しますか。

はい。 詳細については、[スケール セットのゾーン](./virtual-machine-scale-sets-use-availability-zones.md)に関するドキュメントを参照してください。


## <a name="autoscale"></a>自動スケール

### <a name="what-are-best-practices-for-azure-autoscale"></a>Azure の自動スケールのベスト プラクティスを教えてください。

自動スケールのベスト プラクティスについては、[仮想マシンの自動スケールのベスト プラクティス](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices)に関するページを参照してください。

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>ホストベースのメトリックを使用した自動スケールのメトリック名はどこで確認できますか。

ホストベースのメトリックを使用した自動スケールのメトリック名については、「[Azure Monitor のサポートされるメトリック](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/)」を参照してください。

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Azure Service Bus のトピックとキューの長さに基づいた自動スケールの例はありますか。

はい。 Azure Service Bus のトピックとキューの長さに基づいた自動スケールの例については、「[Azure Monitor の自動スケールの一般的なメトリック](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)」を参照してください。

Service Bus キューの場合は、次の JSON を使用します。

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

ストレージ キューの場合は、次の JSON を使用します。

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

この例に使用されている値は、実際のリソースの URI (Uniform Resource Identifier) に置き換えてください。


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>自動スケールは、ホストベースのメトリックか診断拡張機能を使用して行う必要がありますか。

VM の自動スケール設定は、ホストレベルのメトリックまたはゲスト OS ベースのメトリックを使用するように作成できます。

サポートされるメトリックの一覧については、「[Azure Monitor の自動スケールの一般的なメトリック](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics)」を参照してください。

仮想マシン スケール セットの完全なサンプルについては、[仮想マシン スケール セット向けの Resource Manager テンプレートを使用した高度な自動スケール構成](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets)に関するページを参照してください。

サンプルには、ホストレベル CPU のメトリックとメッセージ数のメトリックが使用されています。



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>仮想マシン スケール セットに対するアラート ルールの設定方法を教えてください。

仮想マシン スケール セットのメトリックに対するアラートは、PowerShell または Azure CLI で作成できます。 詳細については、「[Azure Monitor の PowerShell クイックスタート サンプル](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules)」と「[Azure Monitor クロスプラットフォーム CLI のクイックスタート サンプル](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts)」を参照してください。

仮想マシン スケール セットの TargetResourceId は次のような形式になっています。

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

アラートは、メトリックとなる VM のパフォーマンス カウンターを自由に選んで設定することができます。 詳細については、「[Azure Monitor の自動スケールの一般的なメトリック](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)」の記事の [Resource Manager ベースの Windows VM のゲスト OS メトリック](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms)と [Linux VM のゲスト OS メトリック](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms)に関するセクションを参照してください。

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>仮想マシン スケール セットに対する自動スケールを PowerShell で設定する方法を教えてください。

PowerShell を使用して仮想マシン スケール セットに対する自動スケールを設定するには、[仮想マシン スケール セットを自動的にスケーリングする](tutorial-autoscale-powershell.md)に関するページを参照してください。 また、[Azure CLI](tutorial-autoscale-cli.md) および [Azure テンプレート](tutorial-autoscale-template.md)を使用して、自動スケールを構成することもできます。


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>VM が停止 (割り当て解除) されている場合、その VM は自動スケール操作の一環として起動されますか?

いいえ。 自動スケール ルールによって、追加の VM インスタンスがスケール セットの一部として必要とされている場合は、新しい VM インスタンスが作成されます。 停止 (割り当て解除) されている VM インスタンスは、自動スケール イベントの一部としては起動されません。 ただし、そのような停止 (割り当て解除) された VM は、任意の VM インスタンスが VM インスタンス ID の順序に基づいて削除される場合があるのと同様に、インスタンスの数をスケールインする自動スケール イベントの一環として削除される場合があります。



## <a name="certificates"></a>証明書

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>証明書を VM に安全に配布するにはどうすればよいですか。 Web サイトの SSL が証明書の構成から安全に配布される Web サイトを実行するための仮想マシン スケール セットのプロビジョニング方法を教えてください。 (一般的な証明書交換操作は構成の更新操作とほぼ同じです。)具体的な例はありますか。

VM に対して証明書を安全に配布するには、お客様のキー コンテナーから Windows の証明書ストアに直接、証明書をインストールします。

次の JSON を使用します。

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

このコードは、Windows と Linux をサポートします。

詳細については、[仮想マシン スケール セットの作成または更新](https://msdn.microsoft.com/library/mt589035.aspx)に関するページを参照してください。


### <a name="example-of-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Azure Service Fabric クラスター用にプロビジョニングされた自己署名証明書の例
最新の例については、Azure シェル内で次の Azure CLI ステートメントを使用し、Service Fabric CLI モジュール例のドキュメント (StdOut に出力されます) を参照してください。

```bash
az sf cluster create -h
```

Azure の最新の API でサポートされる証明書操作については、Key Vault のドキュメントを参照してください。

証明機関によって提供される分散型の信頼を自己署名証明書を使用して実現することはできません。エンタープライズ運用ソリューションのホストとして使用することを目的とした Service Fabric クラスターに自己署名証明書は使用しないでください。Service Fabric の詳しいセキュリティ ガイダンスについては、「[Azure Service Fabric セキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices)」と「[Service Fabric クラスターのセキュリティに関するシナリオ](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)」を参照してください。

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Resource Manager テンプレートから、Linux 仮想マシン スケール セットで SSH 認証に使用する SSH キー ペアを指定することはできますか。

はい。 **osProfile** の REST API は、標準の VM REST API と似ています。

お使いのテンプレートに **osProfile** を追加してください。

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

この JSON ブロックは、[GitHub の 101-vm-sshkey クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)で使用されています。

この OS プロファイルは、[GitHub の grelayhost.json クイック スタート テンプレート](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json)でも使用されています。

詳細については、[仮想マシン スケール セットの作成または更新](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration)に関するページを参照してください。

### <a name="how-do-i-remove-deprecated-certificates"></a>非推奨の証明書を削除するにはどうすればよいですか?

今後非推奨の証明書を削除するには、コンテナーの証明書一覧からその古い証明書を削除します。 コンピューターに残しておきたい証明書はすべて一覧に残しておいてください。 この操作ですべての VM から証明書が削除されるわけではありません。 また、仮想マシン スケール セットに作成された新しい VM にその証明書が追加されることもありません。

既存の VM から証明書を削除するには、証明書ストアから証明書を手動で削除するカスタム スクリプト拡張機能を作成する必要があります。

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>プロビジョニング時に、既存の SSH 公開キーを仮想マシン スケール セットの SSH レイヤーに挿入するにはどうすればよいですか。 SSH 公開キー値を Azure Key Vault に格納し、Resource Manager テンプレートで使用したいと思っています。

VM に提供するのが SSH の公開キーだけであれば、その公開キーを Key Vault に配置する必要はありません。 公開キーはシークレットではないためです。

SSH 公開キーは、Linux VM の作成時にプレーン テキストで提供できます。

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

linuxConfiguration の要素名 | 必須 | Type | 説明
--- | --- | --- | --- 
ssh | いいえ  | コレクション | Linux OS の SSH キーの構成を指定します。
path | はい | String | SSH キーまたは証明書を配置する Linux ファイル パスを指定します。
keyData | はい | String | Base64 でエンコードされた SSH 公開キーを指定します。

実際の例については、[GitHub の 101-vm-sshkey クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)を参照してください。

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>同じキー コンテナーから複数の証明書を追加した後に `Update-AzVmss` を実行すると、次のメッセージが表示されます。

>Update-AzVmss:リスト secret の中でインスタンス /subscriptions/\<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev が繰り返されていますが、これは許可されていません。

既存のソース コンテナーの新しいコンテナー証明書ではなく、同じコンテナーを再度追加しようとすると、このメッセージが表示されることがあります。 シークレットを二重に追加しようとすると、`Add-AzVmssSecret` コマンドが正しく動作しません。

同じキー コンテナーからシークレットをさらに追加する場合は、$vmss.properties.osProfile.secrets[0].vaultCertificates リストを更新してください。

適切な入力構造については、[仮想マシン セットの作成または更新](https://msdn.microsoft.com/library/azure/mt589035.aspx)に関するページを参照してください。

キー コンテナーに存在する仮想マシン スケール セット オブジェクトから該当するシークレットを見つけてください。 その後、そのコンテナーに関連付けられているリストに証明書参照 (URL とシークレット ストア名) を追加します。

> [!NOTE]
> 現時点では、仮想マシン スケール セット API を使用して VM から証明書を削除することはできません。
>

新しい VM に古い証明書が割り当てられることはありません。 ただし、証明書が存在していて、既にデプロイされていた VM には古い証明書が引き続き存在します。

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>証明書がシークレット ストアにあるときに、パスワードを指定せずに証明書を仮想マシン スケール セットにプッシュすることはできますか。

スクリプトにパスワードをハードコーディングする必要はありません。 パスワードは、デプロイ スクリプトの実行時に使用するアクセス許可で動的に取得することができます。 シークレット ストアのキー コンテナーから証明書を移動するスクリプトがある場合、シークレット ストアの `get certificate` コマンドによって、.pfx ファイルのパスワードも出力されます。

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>仮想マシン スケール セットの virtualMachineProfile.osProfile の Secrets プロパティはどのように機能しますか。 certificateUrl プロパティで証明書の絶対 URI を指定する必要があるときに sourceVault の値が必要なのはなぜですか。

OS プロファイルの Secrets プロパティに Windows リモート管理 (WinRM) 証明書参照が存在する必要があります。

ソース コンテナーを示す目的は、ユーザーの Azure Cloud Service モデル内に存在するアクセス制御リスト (ACL) ポリシーを適用することです。 ソース コンテナーを指定しないと、キー コンテナーへのシークレットのデプロイやシークレットへのアクセスに必要なアクセス許可のないユーザーが、コンピューティング リソース プロバイダー (CRP) を使用してこれらを実行できるようになります。 存在しないリソースにも ACL は存在します。

キー コンテナーの有効な URL を指定しても、指定したソース コンテナー ID が正しくない場合、操作をポーリングしたときにエラーが報告されます。

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>既存の仮想マシン スケール セットにシークレットを追加した場合、シークレットは既存の VM に挿入されるのでしょうか。それとも新しい VM にのみ挿入されるのでしょうか。

証明書は、既存の VM も含め、すべての VM に追加されます。 仮想マシン スケール セットの upgradePolicy プロパティが **手動** に設定されている場合は、VM で手動更新を実行したときに証明書が VM に追加されます。

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Linux VM の証明書はどこに配置されますか。

Linux VM の証明書をデプロイする方法については、[ユーザーが管理するキー コンテナーから証明書を VM にデプロイする方法](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/)についてのブログを参照してください。

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>新しいコンテナー証明書を新しい証明書オブジェクトに追加するにはどうすればよいですか。

コンテナー証明書を既存のシークレットに追加する方法については、次の PowerShell サンプルを参照してください。 シークレット オブジェクトは 1 つだけ使用します。

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>VM を再イメージ化した場合、証明書はどうなりますか?

VM を再イメージ化した場合、証明書は削除されます。 再イメージ化によって OS ディスク全体が削除されます。

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Key Vault から証明書を削除するとどうなりますか?

キー コンテナーからシークレットを削除し、すべての VM に対して `stop deallocate` を実行した後で VM を再度起動すると、エラーが発生します。 CRP はキー コンテナーからシークレットを取得する必要がありますが、それができないためにこのエラーが発生します。 この場合、仮想マシン スケール セット モデルから証明書を削除してください。

CRP コンポーネントは、ユーザーのシークレットを保持しません。 仮想マシン スケール セットのすべての VM に対して `stop deallocate` を実行すると、キャッシュが削除されます。 この場合、シークレットがキー コンテナーから取得されます。

スケールアウト時には、この問題が発生することはありません。(単一ファブリック テナント モデルでは) Azure Service Fabric にシークレットのコピーがキャッシュされているためです。

### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>「[Service Fabric クラスターのセキュリティに関するシナリオ](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)」によれば、証明書 URL (\/\/\<コンテナーの名前>.vault.azure.net:443/secrets/\<正確な場所>) には場所を厳密に指定する必要がありますが、それはなぜですか。

Azure Key Vault のドキュメントに記載されているように、Get Secret REST API は、バージョンが指定されていない場合にシークレットの最新バージョンを返します。

Method | URL
--- | ---
GET | <https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}>

{*secret-name*} を取得するシークレットの名前に置き換え、{*secret-version*} をそのシークレットのバージョンに置き換えます。 シークレットのバージョンを除外しても間違いではありません。 その場合は、最新バージョンが取得されます。

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Key Vault を使用するときに、証明書のバージョンを指定する必要があるのはなぜですか。

証明書のバージョンを指定するという Key Vault の要件の目的は、VM にデプロイされる証明書をユーザーに明確に示すためです。

VM を作成してからキー コンテナーのシークレットを更新した場合、その新しい証明書は既存の VM にダウンロードされません。 しかし一見、既存の VM は新しい証明書を参照しているように見えます。また、新しい VM は新しいシークレットを取得します。 これを避けるために、シークレットのバージョンを参照する必要があります。

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>.cer 公開キーとして配布された複数の証明書をチームで使用しています。 これらの証明書を仮想マシン スケール セットにデプロイする場合の推奨される方法を教えてください。

仮想マシン スケール セットに .cer 公開キーをデプロイするには、.cer ファイルだけを含んだ .pfx ファイルを生成します。 これを行うには、`X509ContentType = Pfx` を実行します。 たとえば、C# または PowerShell で .cer ファイルを x509Certificate2 オブジェクトとして読み込んで、そのメソッドを呼び出します。

詳細については、「[X509Certificate.Export メソッド (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx))」を参照してください。

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>ユーザーが Base64 文字列として証明書を渡す方法がわかりません。 他のリソース プロバイダーには、多くの場合、この方法が用意されています。

Base64 文字列として証明書を渡す動作をエミュレートするには、Resource Manager テンプレート内で、バージョン管理された最新の URL を抽出してください。 Resource Manager テンプレートに次の JSON プロパティを追加します。

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>キー コンテナーで JSON オブジェクトに証明書をラップする必要はありますか。

仮想マシン スケール セットと VM では、JSON オブジェクトに証明書がラップされている必要があります。

コンテンツ タイプとして application/x-pkcs12 もサポートされています。

.cer ファイルは現在サポートされていません。 .cer ファイルを使用するには、それらを .pfx コンテナーにエクスポートする必要があります。



## <a name="compliance-and-security"></a>コンプライアンスとセキュリティ

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>仮想マシン スケール セットは PCI に準拠していますか。

仮想マシン スケール セットは、CRP の上のシン API レイヤーです。 どちらのコンポーネントも、Azure サービス ツリー内のコンピューティング プラットフォームの一部です。

コンプライアンスの観点から言うと、仮想マシン スケール セットは Azure コンピューティング プラットフォームの基礎となる部分です。 仮想マシン スケール セットは、チーム、ツール、プロセス、デプロイ方法、セキュリティ制御、Just-In-Time (JIT) コンパイル、監視、アラートなどを CRP 自体と共有します。 CRP は現行の PCI Data Security Standard (DSS) 構成証明の一部であるため、仮想マシン スケール セットは PCI (Payment Card Industry) に準拠しています。

詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/TrustCenter/Compliance/PCI)を参照してください。

### <a name="does-managed-identities-for-azure-resourceshttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/msi-overview) は仮想マシン スケール セットで機能しますか。

はい。 Azure Quickstart テンプレートで、いくつかのサンプル MSI テンプレートを確認できます。 Linux: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi)。 Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi)。


## <a name="extensions"></a>Extensions

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>仮想マシン スケール セットの拡張機能を削除する方法を教えてください。

仮想マシン スケール セットの拡張機能を削除するには、次の PowerShell コマンドを使用します。

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

`$vmss` に extensionName の値が確認できます。

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Azure Monitor ログと統合する仮想マシン スケール セット テンプレートの例はありますか。

Azure Monitor ログと統合する仮想マシン スケール セット テンプレートの例については、[Azure Service Fabric クラスターをデプロイし、Azure Monitor ログを使って監視を有効にする方法](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric)に関するページの 2 番目の例を参照してください。

### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>拡張機能が仮想マシン スケール セットで並列実行されているようです。 それが原因で、カスタム スクリプト拡張機能が失敗します。 修正するにはどうすればよいですか。

仮想マシン スケール セットにおける拡張機能の実行順序については、[Azure Virtual Machine Scale Sets における拡張機能の実行順序](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)に関するページを参照してください。

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>仮想マシン スケール セットの VM のパスワードをリセットするにはどうすればよいですか。

スケール セット内の VM のパスワードを変更する主な方法は 2 つあります。

- 仮想マシン スケール セット モデルを直接変更する。 Compute API 2017-12-01 以降で実行できます。

    スケール セット モデルの管理者資格情報を直接更新します (たとえば Azure Resource Explorer、PowerShell、CLI を使用します)。 スケール セットが更新されると、すべての新しい VM では新しい資格情報が使用されます。 既存の VM では、新しい資格情報は、それらが再イメージ化された場合のみ使用されます。

- VM アクセス拡張機能を使用してパスワードをリセットする。

    次の PowerShell サンプルを使用します。

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>仮想マシン スケール セットのすべての VM に拡張機能を追加するにはどうすればよいですか。

更新ポリシーが**自動**に設定されている場合は、拡張機能の新しいプロパティが含まれたテンプレートを再デプロイすると、すべての VM が更新されます。

更新ポリシーが**手動**に設定されている場合は、まず拡張機能を更新したうえで、VM のすべてのインスタンスを手動で更新する必要があります。

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>既存の仮想マシン スケール セットに関連付けられている拡張機能を更新した場合、既存の VM に影響はありますか  (つまり、既存の VM は仮想マシン スケール セット モデルと "*一致しなくなる*" のでしょうか)。それとも VM は無視されますか。 既存のマシンでサービス復旧や再イメージ化などを実行する場合、仮想マシン スケール セットで現在構成されているスクリプトと、その VM を最初に作成したときに構成されたスクリプトのどちらが使用されるのでしょうか。

仮想マシン スケール セット モデル内の拡張機能の定義を更新した場合、upgradePolicy プロパティが**自動**に設定されていれば VM が更新されます。 upgradePolicy プロパティが**手動**に設定されている場合は、拡張機能がモデルと一致しないことを示すフラグが設定されます。

既存の VM でサービス復旧を実行した場合、これは VM の再起動と同様であり、拡張機能は再実行されません。 既存の VM を再イメージ化した場合、これは OS ドライブをソース イメージに置き換えた場合と同様です。 最新モデルの特性 (拡張機能など) が実行されます。

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>仮想マシン スケール セットを Active Directory ドメインに参加させるにはどうすればよいですか。

仮想マシン スケール セットを Active Directory (AD) ドメインに参加させるには、拡張機能を定義してください。

拡張機能を定義するには、JsonADDomainExtension プロパティを使用します。

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>私の仮想マシン スケール セット拡張機能が、何か再起動を必要とするものをインストールしようとしています。 たとえば、"commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools" などです。

仮想マシン スケール セット拡張機能で、再起動を必要とするものをインストールしようとしている場合、Azure Automation Desired State Configuration (Automation DSC) 拡張機能を使用してください。 オペレーティング システムが Windows Server 2012 R2 の場合、Azure は Windows Management Framework (WMF) 5.0 セットアップをプルし、再起動して構成を続行します。

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>仮想マシン スケール セットでマルウェア対策を有効にするにはどうすればよいですか。

仮想マシン スケール セットでマルウェア対策を有効にするには、次の PowerShell コマンドを使用します。

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>プライベート ストレージ アカウントでホストされているカスタム スクリプトを実行する必要があります。 ストレージがパブリックのときは正常に実行されますが、Shared Access Signature (SAS) を使用しようとすると失敗します。 次のメッセージが表示されます。"有効な Shared Access Signature で必須のパラメーターがありません" Link+SAS は、ローカル ブラウザーから正常に動作しています。

プライベート ストレージ アカウントでホストされているカスタム スクリプトを実行するには、ストレージ アカウント キーとストレージ アカウント名を使用して保護された設定を構成します。 詳細については、「[Windows でのカスタムのスクリプト拡張機能](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings)」を参照してください。

## <a name="networking"></a>ネットワーク

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>ネットワーク セキュリティ グループ (NSG) をスケール セットに割り当てることで、セット内のすべての VM NIC に適用できますか?

はい。 ネットワーク プロファイルの networkInterfaceConfigurations セクションで参照することで、ネットワーク セキュリティ グループをスケール セットに直接適用できます。 例:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>同じサブスクリプションで同じリージョンの仮想マシン スケール セットの VIP スワップを実行するにはどうすればよいですか。

2 つの仮想マシン スケール セットと Azure Load Balancer フロントエンドがあり、それらが同じサブスクリプションおよびリージョンにある場合、それぞれのパブリック IP アドレスの割り当てを解除し、もう一方に割り当てることができます。 例については、「[VIP Swap:Blue-green deployment in Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/)」(VIP スワップ: Azure Resource Manager での Blue-green デプロイ) をご覧ください。 しかし、この場合、リソースの割り当て解除/割り当てがネットワーク レベルで行われるため、遅延が生じます。 高速なオプションは、Azure Application Gateway と 2 つのバックエンド プールおよびルーティング規則を使うものです。 または、[Azure App Service](https://azure.microsoft.com/services/app-service/) でアプリケーションをホストすることもできます。このサービスでは、ステージング スロットと運用スロット間のすばやい切り替えをサポートしています。

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>静的プライベート IP アドレスの割り当て用に、プライベート IP アドレスの範囲を指定するにはどうすればよいですか。

IP アドレスは指定したサブネットから選択されます。

仮想マシン スケール セットに対する IP アドレスの割り当て方法は常に "動的" ですが、これらの IP アドレスは変わることがあります。 この場合の "動的" とは、あくまで PUT 要求での IP アドレスの指定が手動ではないということです。 サブネットを使用して静的なセットを指定してください。

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>既存の Azure 仮想ネットワークに仮想マシン スケール セットをデプロイするにはどうすればよいですか。

既存の Azure 仮想ネットワークに仮想マシン スケール セットをデプロイする方法については、[仮想マシン スケール セットを既存の仮想ネットワークにデプロイする方法](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet)に関するページを参照してください。

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>仮想マシン スケール セットの最初の VM の IP アドレスをテンプレートの出力に追加するにはどうすればよいですか。

仮想マシン スケール セットの最初の VM の IP アドレスをテンプレートの出力に追加する方法については、「[Azure Resource Manager:Get virtual machine scale sets private IPs](https://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips)」(Azure Resource Manager: 仮想マシン スケール セットのプライベート IP を取得する) をご覧ください。

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>高速ネットワークでスケール セットを使用できますか?

はい。 高速ネットワークを使用するには、スケール セットの networkInterfaceConfigurations 設定で enableAcceleratedNetworking を true に設定します。 たとえば、次のように入力します。
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>スケール セットによって使用される DNS サーバーは、どのように構成すればよいですか?

カスタム DNS 構成を持つ仮想マシン スケール セットを作成するには、スケール セットの networkInterfaceConfigurations セクションに dnsSettings JSON パケットを追加します。 例:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>各 VM にパブリック IP アドレスを割り当てるには、スケール セットをどのように構成すればよいですか?

各 VM にパブリック IP アドレスを割り当てる仮想マシンスケール セットを作成するには、Microsoft.Compute/virtualMachineScaleSets リソースの API バージョンが 2017-03-30 であることを確認してから、スケール セットの ipConfigurations セクションに _publicipaddressconfiguration_ JSON パケットを追加します。 例:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>複数の Application Gateway で動作するようにスケール セットを構成できますか?

はい。 複数の Application Gateway バックエンド アドレス プールのリソース ID を、スケール セット ネットワーク プロファイルの _ipConfigurations_ セクションの _applicationGatewayBackendAddressPools_ リストに追加できます。

## <a name="scale"></a>スケール

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>VM 数が 2 台未満の仮想マシン スケール セットを作成するのは、どのようなケースでしょうか。

VM 数が 2 台未満の仮想マシン スケール セットを作成する理由の 1 つは、仮想マシン スケール セットのエラスティック プロパティを使用することです。 たとえば、VM のランニング コストをかけずにインフラストラクチャのみを定義するために、VM が含まれていない仮想マシン スケール セットをデプロイできます。 その後 VM をデプロイする準備ができたら、実稼働インスタンス数に合わせて仮想マシン スケール セットの "容量" を増やします。

2 台未満の VM で仮想マシン スケール セットを作成するもう 1 つの理由は、個別の VM で可用性セットを使うときほど可用性が問題にならないケースです。 仮想マシン スケール セットにより、代替可能な区別されていないコンピューティング ユニットを使用できるようになります。 この均一性は、仮想マシン スケール セットと可用性セットの主な差別化要素の 1 つです。 多くのステートレス ワークロードでは、個々のユニットが追跡されません。 ワークロードが減少したら 1 つのコンピューティング ユニットにスケールダウンし、ワークロードが増加したときにユニット数をスケールアップすることができます。

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの VM の数を変更するにはどうすればよいですか。

Azure Portal で仮想マシン スケール セットの VM 数を変更するには、仮想マシン スケール セットのプロパティ セクションで、[スケーリング] ブレードをクリックし、スライダー バーを使用します。 インスタンス数を変更する他の方法については、「[Change the instance count of a virtual machine scale set (仮想マシン スケール セットのインスタンス数を変更する)](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/)」をご覧ください。

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>特定のしきい値に達したときのカスタム アラートを定義するにはどうすればよいですか。

アラートを処理する方法にはある程度の柔軟性があります。 たとえばカスタマイズされた webhook を定義できます。 次の webhook は、Resource Manager テンプレートからの例です。

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```

この例では、しきい値に達すると、アラートが Pagerduty.com に送信されます。

## <a name="patching-and-operations"></a>修正プログラムの適用と操作

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>既存のリソース グループでスケール セットを作成するにはどうすればよいですか。

Azure Portal で既存のリソース グループにスケール セットを作成することはできませんが、Azure Resource Manager テンプレートからスケール セットを展開するときに、既存のリソース グループを指定できます。 Azure PowerShell または CLI を使用してスケール セットを作成するときに、既存のリソース グループを指定することもできます。

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>別のリソース グループにスケール セットを移動できますか。

はい。スケール セット リソースは、新しいサブスクリプションまたはリソース グループに移動できます。

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>仮想マシン スケール セットを新しいイメージに更新する方法を教えてください。 パッチはどのように管理するのでしょうか。

仮想マシン スケール セットを新しいイメージに更新し、パッチの適用を管理する方法については、「[仮想マシン スケール セットのアップグレード](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set)」を参照してください。

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>イメージを変更せずに、再イメージ化操作を使用して VM をリセットすることはできますか  (つまり、新しいイメージにではなく、出荷時の設定に VM をリセットできますか)。

はい。イメージを変更せずに、再イメージ化操作を使用して VM をリセットできます。 ただし、`version = latest` が指定されたプラットフォーム イメージを仮想マシン スケール セットが参照している場合、`reimage` を呼び出すと、VM が新しい OS イメージに更新される可能性があります。

詳細については、[仮想マシン スケール セットのすべての VM の管理](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set)に関するページを参照してください。

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>複数のスケール セットを Azure Monitor ログに統合することはできますか。

はい、スケール セットの VM に Azure Monitor 拡張機能をインストールすることで可能です。 Azure CLI の例を次に示します。
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Azure portal の Log Analytics ワークスペースに、必要な workspaceId と workspaceKey が表示されます。 [概要] ページで、[設定] タイルをクリックします。 上部の [接続されたソース] タブをクリックします。

> [!NOTE]
> スケール セット _upgradePolicy_ が手動に設定されている場合、セット内のすべての VM でアップグレードを呼び出して拡張機能を適用することが必要になります。 CLI ではこれは _az vmss update-instances_ になります。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="how-do-i-turn-on-boot-diagnostics"></a>ブート診断を有効にするにはどうすればよいですか。

ブート診断を有効にするにはまず、ストレージ アカウントを作成します。 そのうえで、仮想マシン スケール セットの **virtualMachineProfile** に次の JSON ブロックを配置して、仮想マシン スケール セットを更新します。

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

新しい VM を作成すると、VM の InstanceView プロパティにスクリーンショットなどの詳細が表示されます。 次に例を示します。

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>仮想マシン プロパティ

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>複数の呼び出しを必要とせずに、各 VM のプロパティ情報を取得するにはどうすればよいですか。 たとえば、仮想マシン スケール セットに存在する 100 台の VM について、それぞれの障害ドメインを取得するにはどうすればよいですか。

複数回の呼び出しを行わずに各 VM のプロパティ情報を取得するには、次のリソースの URI に対して REST API `GET` を実行することで `ListVMInstanceViews` を呼び出してください。

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの VM にそれぞれ異なる拡張機能の引数を渡すことはできますか。

いいえ。仮想マシン スケール セットの VM にそれぞれ異なる拡張機能の引数を渡すことはできません。 ただし、拡張機能は、それが実行される VM の一意のプロパティ (マシン名など) に基づいて動作することができます。 また、拡張機能から http://169.254.169.254 でインスタンスのメタデータを照会して、VM についての詳細情報を取得することもできます。

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>仮想マシン スケール セット VM のマシン名や VM ID に欠落があるのはなぜですか  例: 0、1、3...

仮想マシン スケール セット VM のマシン名や VM ID に欠落があるのは、仮想マシン スケール セットの**過剰プロビジョニング** プロパティが既定値の **true** に設定されているためです。 過剰プロビジョニングが **true** の場合、要求した数よりも多くの VM が作成されます。 その後、余分な VM が削除されます。 この場合、連続した名前付けと連続した NAT (ネットワーク アドレス変換) 規則が失われる代わりに、デプロイの信頼性が向上します。

このプロパティは **false** に設定することができます。 仮想マシン スケール セットの規模が小さい場合、デプロイの信頼性はさほど変わりません。

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>仮想マシン スケール セットの VM の削除と VM の割り当て解除の違いは何ですか。 どちらかを選択する必要があるのはどのような場合ですか?

仮想マシン スケール セットの VM の削除と VM の割り当て解除の大きな違いは、`deallocate` では、仮想ハード ディスク (VHD) が削除されないことです。 `stop deallocate` の実行には、ストレージ コストが伴います。 どちらかを選択するのは次のような場合です。

- コンピューティングの料金の支払いは停止し、VM のディスクの状態は保持したい。
- 仮想マシン スケール セットをスケールアウトするよりも迅速に一連の VM を立ち上げたい。
  - このシナリオに関連して、独自の自動スケール エンジンを作成しており、エンドツーエンドのスケーリングを高速化したい。
- 仮想マシン スケール セットが障害ドメインまたは更新ドメインに対して均等に分散されていない。 その原因として、VM を選択的に削除したか、過剰プロビジョニング後に VM を削除したことが考えられます。 仮想マシン スケール セットに対して `stop deallocate` の後に `start` を実行すると、障害ドメインまたは更新ドメインに VM が均等に分散されます。

### <a name="how-do-i-take-a-snapshot-of-a-vmss-instance"></a>VMSS インスタンスのスナップショットはどのように取得しますか。
VMSS のインスタンスからスナップショットを作成します。

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"
 
$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
``` 
 
スナップショットからマネージド ディスクを作成します。

```azurepowershell-interactive
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk') 
```
