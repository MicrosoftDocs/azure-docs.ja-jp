---
title: ISO 27001 共有サービス ブループリント サンプルをデプロイする
description: ブループリント アーティファクト パラメーターの詳細を含む ISO 27001 Shared Services ブループリント サンプルのデプロイ手順です。
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 2eb9ed2199b28ad7cb349e6088c3cc583d8f011d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577322"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 共有サービス ブループリント サンプルをデプロイする

Azure Blueprints ISO 27001 共有サービスのブループリント サンプルをデプロイするには、以下の手順を実行します。

> [!div class="checklist"]
> - サンプルから新しいブループリントを作成する
> - サンプルのコピーを **発行済み** としてマークする
> - ブループリントのコピーを既存のサブスクリプションに割り当てる

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="create-blueprint-from-sample"></a>サンプルからブループリントを作成する

最初に、サンプルをスターターとして使用して環境内に新しいブループリントを作成することにより、ブループリント サンプルを実装します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[はじめに]** ページで、 _[ブループリントの作成]_ の下にある **[作成]** ボタンを選択します。

1. 検索、 **ISO 27001。Shared Services\(ISO 27001: 共有サービス\)** ブループリント サンプルを検索し、**このサンプルを使用する** を選択します。

1. ブループリント サンプルの _[基本]_ を入力します。

   - **[ブループリントの名前]** : ISO 27001 共有サービスのブループリント サンプルのコピーの名前を指定します。
   - **[定義の場所]** :省略記号を使用して、サンプルのコピーを保存する管理グループを選択します。

1. ページの上部にある _[アーティファクト]_ タブまたはページの下部にある **[次へ: アーティファクト]** を選択します。

1. ブループリント サンプルを構成するアーティファクトの一覧を確認します。 多くのアーティファクトには、後で定義するパラメーターがあります。 ブループリント サンプルの確認が完了したら、 **[下書きの保存]** を選択します。

## <a name="publish-the-sample-copy"></a>サンプルのコピーを発行する

環境でのブループリント サンプルのコピーの作成が完了しました。 これは **下書き** モードで作成されており、割り当ておよびデプロイの前に **発行** する必要があります。 ブループリント サンプルのコピーは、お使いの環境や必要性に応じてカスタマイズできますが、それが原因で ISO 27001 標準を満たさなくなる場合もあります。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ページの上部にある **[ブループリントを発行する]** を選択します。 右側の新しいページで、ブループリント サンプルのコピーの **[バージョン]** を指定します。 このプロパティは、後で変更を加える場合に役立ちます。 **[変更に関するメモ]** (例:「ISO 27001 ブループリント サンプルから発行する最初のバージョン」) を入力します。 次に、ページの下部にある **[発行]** を選択します。

## <a name="assign-the-sample-copy"></a>サンプルのコピーを割り当てる

正常に **発行** されたブループリント サンプルのコピーは、保存先の管理グループ内のサブスクリプションに割り当てることができます。 この手順では、ブループリント サンプルのコピーの各デプロイを一意にするためのパラメーターを指定します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 ブループリント サンプルのコピーを、フィルターを使用して検索し、選択します。

1. ブループリント定義ページの上部にある **[ブループリントの割り当て]** を選択します。

1. ブループリント割り当て用のパラメーター値を指定します。

   - 基本

     - **サブスクリプション**:ブループリント サンプルのコピーを保存した管理グループ内の 1 つ以上のサブスクリプションを選択します。 複数のサブスクリプションを選択すると、入力したパラメーターを使用して、それぞれに対して割り当てが作成されます。
     - **割り当て名**:名前は、ブループリントの名前に基づいてあらかじめ設定されています。
       必要に応じて変更することも、そのままにしておくこともできます。
     - **[場所]** :マネージド ID を作成するリージョンを選択します。 Azure Blueprint は、この管理対象 ID を使用して、割り当てられたブループリント内にすべての成果物をデプロイします。 詳細については、[Azure リソースの管理対象 ID の概要](../../../../active-directory/managed-identities-azure-resources/overview.md)に関するページをご覧ください。
     - **ブループリント定義ラベル**:ブループリント サンプルのコピーの **発行済み** バージョンを選択します。

   - ロックの割り当て

     お使いの環境のブループリントのロック設定を選択します。 詳細については、[ブループリント リソースのロック](../../concepts/resource-locking.md)に関するページを参照してください。

   - マネージド ID

     既定の _システム割り当て_ マネージド ID オプションはそのままにします。

   - ブループリントのパラメーター

     このセクションで定義するパラメーターは、一貫性を維持するために、ブループリント定義のアーティファクトの多くで使用されます。

     - **組織名**:組織の短縮名を入力します。 このプロパティは、主にリソースの名前付けのために使用されます。
     - **共有サービスのサブネット アドレス プレフィックス**:デプロイされたリソースをまとめてネットワーク接続するために CIDR 表記の値を指定します。
     - **共有サービスの場所**:アーティファクトをデプロイする場所を決定します。 すべてのサービスがすべての場所で利用できるわけではありません。 このようなサービスをデプロイするアーティファクトには、そのアーティファクトをデプロイする場所についてのパラメーター オプションが用意されています。
     - **Allowed location (Policy:ISO 27001 用ブループリント イニシアティブ)** :リソース グループとリソースに許可されている場所を示す値。
     - **VM エージェントの Log Analytics ワークスペース (ポリシー:ISO 27001 用ブループリント イニシアティブ)** :ワークスペースのリソース ID を指定します。 このパラメーターでは、`concat` 関数を使用して、リソース ID が作成されます。

   - アーティファクトのパラメーター

     このセクションで定義するパラメーターは、定義対象のアーティファクトに適用されます。 これらのパラメーターはブループリントの割り当て時に定義されるので、[動的パラメーター](../../concepts/parameters.md#dynamic-parameters)です。 アーティファクトのパラメーターとその説明を含む詳しい一覧については、「[アーティファクトのパラメーター表](#artifact-parameters-table)」を参照してください。

1. すべてのパラメーターの入力が完了したら、ページの下部にある **[割り当て]** を選択します。 ブループリントの割り当てが作成され、アーティファクトのデプロイが開始されます。 デプロイに要する時間は、約 1 時間です。 デプロイの状態を確認するには、ブループリントの割り当てを開きます。

> [!WARNING]
> Azure Blueprints サービスと、組み込まれているブループリント サンプルは、**無料** でご利用になれます。 Azure リソースは、[製品ごとに課金](https://azure.microsoft.com/pricing/)されます。 このブループリント サンプルでデプロイされるリソースの実行コストを見積もるには、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

## <a name="artifact-parameters-table"></a>アーティファクトのパラメーター表

以下の表は、ブループリント アーティファクトのパラメーターの一覧を示しています。

|アーティファクト名|アーティファクトの種類|パラメーター名|説明|
|-|-|-|-|
|\[プレビュー\]:Linux VM スケール セット (VMSS) 用の Log Analytics エージェントのデプロイ|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Linux OS を持つ VM イメージの一覧|(省略可能) 既定値は " _["なし"]_ " です。|
|\[プレビュー\]:Linux VM への Log Analytics エージェントのデプロイ|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Linux OS を持つ VM イメージの一覧|(省略可能) 既定値は " _["なし"]_ " です。|
|\[プレビュー\]:Windows VM Scale Sets (VMSS) 用の Log Analytics エージェントのデプロイ|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Windows OS を持つ VM イメージの一覧|(省略可能) 既定値は " _["なし"]_ " です。|
|\[プレビュー\]:Windows VM への Log Analytics エージェントのデプロイ|ポリシー割り当て|省略可能:スコープに追加するため、サポートされている Windows OS を持つ VM イメージの一覧|(省略可能) 既定値は " _["なし"]_ " です。|
|許可されるリソースの種類|ポリシー割り当て|許可されるリソースの種類|デプロイできるリソースの種類の一覧。 この一覧は、共有サービスでデプロイされるすべてのリソースの種類から構成されています。|
|許可されるストレージ アカウントの SKU|ポリシー割り当て|Allowed storage SKU (許可されるストレージの SKU)|許可される診断ログのストレージ アカウントの SKU の一覧。 既定値は _["Standard_LRS"]_ です。|
|許可される仮想マシンの SKU|ポリシー割り当て|デプロイが許可される仮想マシンの SKU の一覧。 既定値は " _["Standard_DS1_v2", "Standard_DS2_v2"]\(["Standard_DS1_v2"、"Standard_DS2_v2"]\)_ " です。|
|ISO 27001 用ブループリント イニシアティブ|ポリシー割り当て|診断ログを監査するリソースの種類|診断ログ設定が無効になっていないかを監査するリソースの種類の一覧。 使用できる値は、[Azure Monitor 診断ログのスキーマ](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas)に関するページで確認できます。|
|Log Analytics resource group (Log Analytics リソース グループ)|Resource group|名前|**[ロック済み]** - **組織名** と `-sharedsvsc-log-rg` を連結して、リソース グループを一意にします。|
|Log Analytics resource group (Log Analytics リソース グループ)|Resource group|場所|**[ロック済み]** - ブループリントのパラメーターを使用します。|
|Log Analytics テンプレート|Resource Manager テンプレート|サービス階層|Log Analytics ワークスペースの階層を設定します。 既定値は _[PerNode]_ です。|
|Log Analytics テンプレート|Resource Manager テンプレート|ログ保有期間日数|データ保有期間の日数。 既定値は _[365]_ です。|
|Log Analytics テンプレート|Resource Manager テンプレート|場所|Log Analytics ワークスペースを作成するために使用されるリージョン。 既定値は _米国西部 2_ です。|
|Network resource group (ネットワーク リソース グループ)|Resource group|名前|**[ロック済み]** - **組織名** と `-sharedsvcs-net-rg` を連結して、リソース グループを一意にします。|
|Network resource group (ネットワーク リソース グループ)|Resource group|場所|**[ロック済み]** - ブループリントのパラメーターを使用します。|
|Azure Firewall テンプレート|Resource Manager テンプレート|Azure Firewall のプライベート IP|[Azure Firewall](../../../../firewall/overview.md) のプライベート IP を構成します。 この値は、共有サービスのサブネット上の既定のルート テーブルとしても使用されます。 **[Azure Firewall サブネットのアドレス プレフィックス]** に定義されている CIDR 表記の一部にする必要があります。 既定値は _10.0.4.4_ です。|
|Azure Firewall テンプレート|Resource Manager テンプレート|ログ保有期間日数|データ保有期間の日数。 既定値は _[365]_ です。|
|ネットワーク セキュリティ グループ テンプレート|Resource Manager テンプレート|ログ保有期間日数|データ保有期間の日数。 既定値は _[365]_ です。|
|仮想ネットワークとルート テーブルのテンプレート|Resource Manager テンプレート|仮想ネットワーク アドレス プレフィックス|Virtual Network の CIDR 表記。 既定値は _10.0.0.0/16_ です。|
|仮想ネットワークとルート テーブルのテンプレート|Resource Manager テンプレート|Virtual Network DDoS 保護を有効にする|Virtual Network に対する DDoS 保護を構成します。 既定値は " _[はい]_ " です。|
|仮想ネットワークとルート テーブルのテンプレート|Resource Manager テンプレート|共有サービス サブネット アドレス プレフィックス|共有サービスのサブネットの CIDR 表記。 既定値は _10.0.0.0/24_ です。|
|仮想ネットワークとルート テーブルのテンプレート|Resource Manager テンプレート|DMZ サブネット アドレス プレフィックス|DMZ サブネットの CIDR 表記。 既定値は _10.0.1.0/24_ です。|
|仮想ネットワークとルート テーブルのテンプレート|Resource Manager テンプレート|Application Gateway サブネット アドレス プレフィックス|Application Gateway サブネットの CIDR 表記。 既定値は _10.0.2.0/24_ です。|
|仮想ネットワークとルート テーブルのテンプレート|Resource Manager テンプレート|Virtual Network ゲートウェイ サブネット アドレス プレフィックス|Virtual Network ゲートウェイ サブネットの CIDR 表記。 既定値は _10.0.3.0/24_ です。|
|仮想ネットワークとルート テーブルのテンプレート|Resource Manager テンプレート|Azure Firewall サブネット アドレス プレフィックス|[Azure ファイアウォール](../../../../firewall/overview.md) サブネットの CIDR 表記。 **Azure ファイアウォールのプライベート IP** パラメーターを含める必要があります。|
|Key Vault リソース グループ|Resource group|名前|**[ロック済み]** - **組織名** と `-sharedsvcs-kv-rg` を連結して、リソース グループを一意にします。|
|Key Vault リソース グループ|Resource group|場所|**[ロック済み]** - ブループリントのパラメーターを使用します。|
|キー コンテナー テンプレート|Resource Manager テンプレート|Jumpbox 管理者ユーザー名|Jumpbox のユーザー名。 **[Jumpbox のテンプレート]** 内の同じプロパティの値に一致する必要があります。 既定値は _[jb-admin-user]_ です。|
|キー コンテナー テンプレート|Resource Manager テンプレート|Jumpbox 管理者の SSH キーまたはパスワード|Jumpbox のアカウントのキーまたはパスワード。 **[Jumpbox のテンプレート]** 内の同じプロパティの値に一致する必要があります。 既定値はありませんが、空白のままにしておくことはできません。|
|キー コンテナー テンプレート|Resource Manager テンプレート|ドメイン管理者のユーザー名|Active Directory VM にアクセスしたり、他の VM をドメインに参加させたりするために使用するユーザー名。 **[Active Directory Domain Services テンプレート]** 内の **[Domain admin user]\(ドメイン管理者ユーザー\)** プロパティの値に一致する必要があります。 既定値は _[domain-admin-user]_ です。|
|キー コンテナー テンプレート|Resource Manager テンプレート|ドメイン管理者のパスワード|ドメイン管理ユーザーのパスワード。 既定値はありませんが、空白のままにしておくことはできません。|
|キー コンテナー テンプレート|Resource Manager テンプレート|AAD オブジェクト ID|キー コンテナー インスタンスにアクセスする必要があるアカウントの AAD オブジェクト識別子。 既定値はありませんが、空白のままにしておくことはできません。 Azure portal でこの値を検索するには、 _[サービス]_ で [ユーザー] を検索して選択します。 _[名前]_ ボックスを使用してアカウント名をフィルター処理し、そのアカウントを選択します。 _[ユーザー プロファイル]_ ページで、 _[オブジェクト ID]_ の横にある [クリックしてコピー] アイコンを選択します。  |
|キー コンテナー テンプレート|Resource Manager テンプレート|ログ保有期間日数|データ保有期間の日数。 既定値は _[365]_ です。|
|キー コンテナー テンプレート|Resource Manager テンプレート|キー コンテナー SKU|作成されるキー コンテナーの SKU を指定します。 既定値は _[Premium]_ です。|
|Jumpbox resource group (Jumpbox リソース グループ)|Resource group|名前|**[ロック済み]** - **組織名** と `-sharedsvcs-jb-rg` を連結して、リソース グループを一意にします。|
|Jumpbox resource group (Jumpbox リソース グループ)|Resource group|場所|**[ロック済み]** - ブループリントのパラメーターを使用します。|
|Jumpbox のテンプレート|Resource Manager テンプレート|Jumpbox 管理者ユーザー名|Jumpbox VM にアクセスするために使用するユーザー名。 **キー コンテナー テンプレート** 内の同じプロパティの値に一致する必要があります。 既定値は _[jb-admin-user]_ です。|
|Jumpbox のテンプレート|Resource Manager テンプレート|Jumpbox 管理者のパスワード (Key Vault リソース ID)|キー コンテナーのリソース ID。 "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" を使用し、`{subscriptionId}` をお使いのサブスクリプション ID、`{orgName}` を **[組織名]** ブループリント パラメーターに置き換えます。|
|Jumpbox のテンプレート|Resource Manager テンプレート|Jumpbox 管理者のパスワード (Key Vault シークレット名)|Jumpbox 管理者のユーザー名。 **[Key Vault テンプレート]** プロパティの **[Jumpbox 管理者ユーザー名]** の値に一致する必要があります。|
|Jumpbox のテンプレート|Resource Manager テンプレート|Jumpbox オペレーティング システム|Jumpbox VM のオペレーティングシステムを決定します。 既定値は _[Windows]_ です。|
|Active Directory Domain Services resource group (Active Directory Domain Services のリソース グループ)|Resource group|名前|**[ロック済み]** - **組織名** と `-sharedsvcs-adds-rg` を連結して、リソース グループを一意にします。|
|Active Directory Domain Services resource group (Active Directory Domain Services のリソース グループ)|Resource group|場所|**[ロック済み]** - ブループリントのパラメーターを使用します。|
|Active Directory Domain Services テンプレート|Resource Manager テンプレート|ドメイン管理者のユーザー名|ADDS Jumpbox のユーザー名。 **キー コンテナー テンプレート** 内の同じプロパティの値に一致する必要があります。 既定値は _[adds-admin-user]_ です。|
|Active Directory Domain Services テンプレート|Resource Manager テンプレート|ドメイン管理者のパスワード (Key Vault リソース ID)|キー コンテナーのリソース ID。 "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" を使用し、`{subscriptionId}` をお使いのサブスクリプション ID、`{orgName}` を **[組織名]** ブループリント パラメーターに置き換えます。|
|Active Directory Domain Services テンプレート|Resource Manager テンプレート|ドメイン管理者のパスワード (Key Vault シークレット名)|ドメイン管理者のユーザー名。 **[Key Vault テンプレート]** 内のプロパティ **[ドメイン管理者のユーザー名]** の値に一致する必要があります。|
|Active Directory Domain Services テンプレート|Resource Manager テンプレート|ドメイン名|サンプルで作成された Active Directory の名前。 既定値は _[contoso.com]_ です。|
|Active Directory Domain Services テンプレート|Resource Manager テンプレート|Domain admin user (ドメイン管理者ユーザー)|管理者の AD アカウント用およびデバイスを AD ドメインに参加させるためのユーザー名。 **[Key Vault テンプレート]** 内の **[AD admin username]\(AD 管理者のユーザー名\)** プロパティの値に一致する必要があります。 既定値は _[domain-admin-user]_ です。|
|Active Directory Domain Services テンプレート|Resource Manager テンプレート|ドメイン管理者のパスワード|パスワードを格納するための Key Vault の詳細を設定します。 既定値はありませんが、空白のままにしておくことはできません。|

## <a name="next-steps"></a>次のステップ

ISO 27001 共有サービスのブループリント サンプルをデプロイする手順を確認したので、以下の記事に進み、アーキテクチャおよびコントロールのマッピングの詳細を確認します。

> [!div class="nextstepaction"]
> [ISO 27001 共有サービスのブループリント - 概要](./index.md)
> [ISO 27001 共有サービスのブループリント - コントロールのマッピング](./control-mapping.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
