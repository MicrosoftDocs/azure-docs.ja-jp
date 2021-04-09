---
title: Azure HDInsight でクラスターを作成する - エラー ディクショナリ
description: Azure HDInsight クラスターの作成時に発生するエラーのトラブルシューティング方法について説明します
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: 469adf1c0e5108f7a8c89a7c3a4bebba5d42b431
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183966"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: クラスター作成時のエラー

この記事では、クラスターの作成時に発生する可能性のあるエラーの解決方法について説明します。

> [!NOTE]
> この記事で説明されている最初の 3 つのエラーは、検証エラーです。 Azure HDInsight 製品で **CsmDocument_2_0** クラスが使用されている場合に発生する可能性があります。

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>エラー コード: DeploymentDocument 'CsmDocument_2_0' で検証に失敗しました

**Error**: ""スクリプト操作の場所を次のアクセスされた URI にすることはできません:\<SCRIPT ACTION URL\>"

### <a name="error-message-1"></a>エラー メッセージ 1

"リモート サーバーがエラーを返しました: (404) 見つかりません。"

#### <a name="cause"></a>原因

HDInsight サービスから、クラスター作成要求の一部として指定したスクリプト操作 URL にアクセスできません。 サービスでは、スクリプト操作にアクセスしようとすると、上記のエラー メッセージを受け取ります。

#### <a name="resolution"></a>解決方法

- HTTP または HTTPS URL の場合は、incognito ブラウザー ウィンドウからアクセスを試行して、URL を確認します。
- WASB URL の場合は、要求で指定したストレージ アカウントにスクリプトが存在することを確認します。 また、このストレージ アカウント用のストレージ キーが正しいことを確認します。
- ADLS URL の場合は、ストレージ アカウントにスクリプトが存在することを確認します。

---

### <a name="error-message-2"></a>エラー メッセージ 2

"指定されたスクリプト URI \<SCRIPT_URI\> は ADLS 上にありますが、このクラスターには Data Lake Storage プリンシパルがありません"

#### <a name="cause"></a>原因

HDInsight サービスから、クラスター作成要求の一部として指定したスクリプト操作 URL にアクセスできません。 サービスでは、スクリプト操作にアクセスしようとすると、上記のエラー メッセージを受け取ります。

#### <a name="resolution"></a>解決方法

対応する Azure Data Lake Storage Gen 1 アカウントをクラスターに追加します。 また、Data Lake Storage Gen 1 アカウントにアクセスするサービス プリンシパルをクラスターに追加します。

---

### <a name="error-message-3"></a>エラー メッセージ 3

"要求で指定された VM サイズ '\<CUSTOMER_SPECIFIED_VM_SIZE\>' が無効であるか、'\<ROLE\>' ロールでサポートされていません。 有効な値: \<VALID_VM_SIZE_FOR_ROLE\>。"

#### <a name="cause"></a>原因

指定した仮想マシンのサイズがロールで許可されていません。 このエラーは、VM サイズの値が予期したとおりに動作しないか、コンピューターのロールに適していないことが原因で発生する可能性があります。

#### <a name="resolution"></a>解決方法

エラー メッセージには、VM サイズの有効な値が表示されます。 これらの値のいずれかを選択し、クラスター作成要求を再試行してください。

---

## <a name="error-codeinvalidvirtualnetworkid"></a>エラー コード: InvalidVirtualNetworkId  

### <a name="error"></a>エラー

"VirtualNetworkId が無効です。 VirtualNetworkId '\<USER_VIRTUALNETWORKID\>'*"

### <a name="cause"></a>原因

クラスター作成時に指定した **VirtualNetworkId** の値が正しい形式ではありません。

### <a name="resolution"></a>解決方法

**VirtualNetworkId** とサブネットの値が正しい形式であることを確認してください。 **VirtualNetworkId** の値を取得するには、次のようにします。

1. Azure Portal にアクセスします。
1. 仮想ネットワークを選択します。
1. **[プロパティ]** メニュー項目を選択します。 **ResourceID** プロパティの値は **VirtualNetworkId** 値です。

仮想ネットワーク ID の例を以下に示します。

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>エラー コード:CustomizationFailedErrorCode

### <a name="error"></a>エラー

"カスタム スクリプト操作でエラーが発生したため、クラスターのデプロイに失敗しました。 失敗した操作: \<SCRIPT_NAME\>。Ambari UI に移動して、エラーをさらにデバッグしてください。"

### <a name="cause"></a>原因

クラスター作成要求時に指定したカスタム スクリプトは、クラスターが正常にデプロイされた後に実行されます。 このエラー コードは、\<SCRIPT_NAME\> という名前のカスタム スクリプトの実行中にエラーが発生したことを示しています。

### <a name="resolution"></a>解決方法

スクリプトはカスタム スクリプトであるため、問題のトラブルシューティングを行い、必要に応じてスクリプトを再実行することをお勧めします。 スクリプト エラーのトラブルシューティングを行うには、/var/lib/ambari-agent/* フォルダー内のログを調べます。 または、Ambari UI の **[操作]** ページを開き、**run_customscriptaction** 操作を選択して、エラーの詳細を表示します。

---

## <a name="error-codeinvaliddocumenterrorcode"></a>エラー コード: InvalidDocumentErrorCode

### <a name="error"></a>エラー

"データベース \<DATABASE_NAME\> の \<META_STORE_TYPE\> Metastore スキーマのバージョン \<METASTORE_MAJOR_VERSION\> とクラスターのバージョン \<CLUSTER_VERSION\> に互換性がありません"

### <a name="cause"></a>原因

カスタム メタストアと選択された HDInsight クラスターのバージョンに互換性がありません。 現在、HDInsight 4.0 クラスターでは Metastore バージョン 3.0 以降のみがサポートされていますが、HDInsight 3.6 クラスターでは Metastore バージョン 3.0 以降はサポートされていません。

### <a name="resolution"></a>解決方法

ご利用の HDInsight クラスターのバージョンでサポートされている Metastore のバージョンのみを使用します。 カスタム メタストアを指定しない場合、HDInsight では内部でメタストアが作成され、クラスターの削除時に削除されます。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>エラー コード:FailedToConnectWithClusterErrorCode 

### <a name="error"></a>エラー

"クラスター管理エンドポイントに接続してスケーリング操作を実行することができません。 ネットワーク セキュリティ ルールによってクラスターへの外部アクセスがブロックされていないこと、およびクラスター マネージャー (Ambari) の UI に正常にアクセスできることを確認してください。"

### <a name="cause"></a>原因

ネットワーク セキュリティ グループ (NSG) のファイアウォール規則により、重要な Azure の正常性および管理サービスとのクラスター通信がブロックされています。

### <a name="resolution"></a>解決方法

ネットワーク セキュリティ グループを使用してネットワーク トラフィックを制御する予定の場合は、HDInsight をインストールする前に、次の操作を実行します。

- HDInsight を使用する予定の Azure リージョンを特定します。
- HDInsight が必要とする IP アドレスを特定します。 詳細については、[HDInsight 管理 IP アドレス](./hdinsight-management-ip-addresses.md)に関する記事を参照してください。
  - HDInsight をインストールする予定のサブネットのネットワーク セキュリティ グループを作成または変更します。
  - ネットワーク セキュリティ グループの場合、IP アドレスからの受信トラフィックをポート 443 で許可します。 この構成により、HDInsight 管理サービスが仮想ネットワークの外部からクラスターに確実に到達できるようになります。

---

## <a name="error-code-storagepermissionsblockedformsi"></a>エラー コード:StoragePermissionsBlockedForMsi

### <a name="error"></a>エラー

"マネージド ID にストレージ アカウントに対するアクセス許可がありません。 ストレージ アカウントのマネージド ID に対して、"ストレージ BLOB データ所有者" ロールが割り当てられていることを確認してください。 ストレージ: /subscriptions/ \<Subscription ID\> /resourceGroups/\< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>、マネージド ID: /subscriptions/ \<Subscription ID\> /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>原因

ID の管理に必要なアクセス許可を指定しませんでした。 ユーザー割り当てマネージド ID に、Azure Data Lake Storage Gen2 ストレージ アカウントの Blob Storage 共同作成者ロールがありません。

### <a name="resolution"></a>解決方法

1. Azure portal を開きます。
1. ストレージ アカウントに移動します。
1. **[アクセス制御 (IAM)]** を確認します。
1. ストレージ BLOB データ共同作成者ロールまたはストレージ BLOB データ所有者ロールがユーザーに割り当てられていることを確認します。

詳細については、「[Data Lake Storage Gen2 アカウントにマネージド ID のアクセス許可を設定する](hdinsight-hadoop-use-data-lake-storage-gen2.md)」を参照してください。

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>エラー コード:InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>エラー

"サブネット /subscriptions/\<SubscriptionID\>/resourceGroups/\<Resource Group name\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name\>/subnets/\<Subnet Name\> で構成されている、ネットワーク セキュリティ グループ /subscriptions/\<SubscriptionID\>/resourceGroups/<リソース グループ名\> default/providers/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\> 内のセキュリティ規則で、必要な受信または送信接続が許可されていません。 詳細については、「[Azure HDInsight 用の仮想ネットワークを計画する](./hdinsight-plan-virtual-network-deployment.md)」にアクセスするか、サポートにお問い合わせください。"

### <a name="cause"></a>原因

ネットワーク セキュリティ グループまたはユーザー定義ルート (UDR) で HDInsight クラスターへの受信トラフィックを制御する場合は、クラスターで Azure の重要な正常性および管理サービスと通信できることを確認してください。

### <a name="resolution"></a>解決方法

ネットワーク セキュリティ グループを使用してネットワーク トラフィックを制御する予定の場合は、HDInsight をインストールする前に、次の操作を実行します。

- HDInsight で使用する予定の Azure リージョンを特定し、リージョンの安全な IP アドレスの一覧を作成します。 詳細については、「[正常性サービスと管理サービス: 特定のリージョン](./hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions)」を参照してください。
- HDInsight で必要な IP アドレスを特定します。 詳細については、「 [HDInsight の管理 IP アドレス](./hdinsight-management-ip-addresses.md)」を参照してください。
- HDInsight をインストールする予定のサブネットのネットワーク セキュリティ グループを作成または変更します。 ネットワーク セキュリティ グループの場合、IP アドレスからの受信トラフィックをポート 443 で許可します。 この構成により、HDInsight 管理サービスが仮想ネットワークの外部からクラスターに確実に到達できるようになります。

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>エラー コード:クラスターのセットアップで、1 つまたは複数のホストにコンポーネントをインストールできませんでした

### <a name="error"></a>エラー

"クラスターのセットアップで、1 つまたは複数のホストにコンポーネントをインストールできませんでした。 要求を再試行してください。"

### <a name="cause"></a>原因 

通常、このエラーは、一時的な問題または Azure の停止が発生した場合に生成されます。

### <a name="resolution"></a>解決方法

[Azure の状態](https://status.azure.com)ページで、クラスターのデプロイに影響する可能性がある Azure の停止を確認してください。 停止が発生していない場合は、クラスターのデプロイを再試行してください。

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Error Code:FailedToConnectWithClusterErrorCode

### <a name="error"></a>エラー

クラスター管理エンドポイントに接続することができません。 後で再試行してください。

### <a name="cause"></a>原因

HDInsight サービスが、クラスターを作成しようとしているときにクラスターに接続できません

### <a name="resolution"></a>解決方法

カスタム VNet ネットワーク セキュリティ グループ (NSG) とユーザー定義ルート (UDR) を使用している場合は、クラスターが HDInsight 管理サービスと通信できることを確認してください。 詳細については、[HDInsight 管理 IP アドレス](./hdinsight-management-ip-addresses.md)に関する記事を参照してください。

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Error Code:Deployments failed due to policy violation:'Resource '<Resource URI>' was disallowed by policy. Policy identifiers: '[{"policyAssignment":{"name":"<Policy Name> ","id":"/providers/Microsoft.Management/managementGroups/<Management Group Name> providers/Microsoft.Authorization/policyAssignments/<Policy Name>"},"policyDefinition": <Policy Definition>

### <a name="cause"></a>原因

サブスクリプションベースの Azure ポリシーによって、パブリック IP アドレスの作成が拒否されている可能性があります。 HDInsight クラスターの作成には、2 つのパブリック IP が必要です。

次のポリシーは、一般にクラスターの作成に影響を与えます。

* サブスクリプション内の IP アドレスまたはロード バランサーの作成を妨げるポリシー。
* ストレージ アカウントの作成を妨げるポリシー。
* IP アドレスやロード バランサーなどのネットワーク リソースの削除を妨げるポリシー。

### <a name="resolution"></a>解決方法

HDInsight クラスターの作成中は、サブスクリプションベースの Azure Policy の割り当てを削除または無効にします。

---

## <a name="next-steps"></a>次のステップ

クラスター作成時のエラーのトラブルシューティングについて詳しくは、「[Azure HDInsight のクラスター作成に失敗する問題のトラブルシューティング](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)」を参照してください。