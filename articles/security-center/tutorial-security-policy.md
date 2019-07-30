---
title: セキュリティ ポリシーの操作 | Microsoft Docs
description: この記事では、Azure Security Center でセキュリティ ポリシーを操作する方法について説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/05/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ff05421222ff0f4312d703366cfd443eee06450
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551716"
---
# <a name="working-with-security-policies"></a>セキュリティ ポリシーの操作

この記事では、セキュリティ ポリシーの構成方法と、それの Security Center での表示方法について説明します。 Azure Security Center では、オンボードされている各サブスクリプションに、[組み込みのセキュリティ ポリシー](security-center-policy-definitions.md)が自動的に割り当てられます。 これらは、ポリシーを管理グループや複数のサブスクリプションに渡って設定できる [Azure Policy](../governance/policy/overview.md) で構成することも可能です。

PowerShell を使用してポリシーを設定する手順については、「[クイック スタート: Azure PowerShell モジュールを使用してポリシーの割り当てを作成し、準拠していないリソースを特定する](../governance/policy/assign-policy-powershell.md)」を参照してください。

>[!NOTE]
> Security Center では、Azure Policy との統合が開始されました。 既存のお客様は、Security Center における以前のセキュリティ ポリシーではなく、Azure Policy の新しい組み込みイニシアティブに自動的に移行されます。 この変更は、Azure Policy に新しいイニシアティブが存在すること以外、お客様のリソースや環境には影響を与えません。

## <a name="what-are-security-policies"></a>セキュリティ ポリシーとは
セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Azure Policy では、Azure サブスクリプションのポリシーを定義して、ワークロードの種類やデータの機密性に合わせてそれを調整することが可能です。 たとえば、個人情報や顧客データのような、規制されたデータが使用されるアプリケーションには、他のワークロードより高いレベルのセキュリティが必要です。 設定を管理グループまたはサブスクリプション間でのポリシーを設定する [Azure Policy](../governance/policy/overview.md) します。

Azure Security Center から取得するセキュリティに関する推奨事項は、ご使用のセキュリティ ポリシーによって駆動されます。 ユーザーは、それへの準拠を監視して、潜在的な脆弱性を特定し、脅威を軽減することができます。 自分に合った選択肢を判断する方法の詳細については、[組み込みのセキュリティ ポリシー](security-center-policy-definitions.md)の一覧を参照してください。

Security Center を有効にすると、Security Center に組み込まれたセキュリティ ポリシーは、Azure Policy で、Security Center カテゴリの組み込みイニシアティブとして反映されます。 ビルトインのイニシアティブは、Security Center のすべての登録済みサブスクリプション (Free または Standard レベル) に対して自動的に割り当てられます。 組み込みイニシアティブに含まれるのは監査ポリシーだけです。


### <a name="management-groups"></a>管理グループ
組織に多数のサブスクリプションがある場合は、これらのサブスクリプションのアクセス、ポリシー、およびコンプライアンスを効率的に管理する方法が必要になることがあります。 Azure 管理グループの範囲は、サブスクリプションを上回ります。 "管理グループ" と呼ばれるコンテナーにサブスクリプションを整理して、管理グループに管理ポリシーを適用できます。 管理グループ内のすべてのサブスクリプションは、管理グループに適用されたポリシーを自動的に継承します。 各ディレクトリには、"ルート" 管理グループと呼ばれる 1 つの最上位管理グループがあります。 このルート管理グループは階層に組み込まれており、すべての管理グループとサブスクリプションはルート管理グループにまとめられます。 ルート管理グループにより、グローバル ポリシーと RBAC の割り当てをディレクトリ レベルで適用できます。 Azure Security Center で使用する管理グループを設定するには、「[Azure Security Center に対するテナント全体の可視性を確保する](security-center-management-groups.md)」の指示に従ってください。

> [!NOTE]
> 管理グループとサブスクリプションの階層を理解することが重要です。 管理グループ、ルート管理、および管理グループへのアクセスの詳細については、「[Organize your resources with Azure Management Groups (Azure 管理グループを使用してリソースを整理する)](../governance/management-groups/index.md#root-management-group-for-each-directory)」を参照してください。
>

## <a name="how-security-policies-work"></a>セキュリティ ポリシーのしくみ
Security Center では、Azure サブスクリプションごとに自動で既定のセキュリティ ポリシーが作成されます。 Azure Policy のポリシーを編集すると、以下の操作を行うことができます。
- 新しいポリシー定義を作成する。
- 組織全体または組織内の部署を表す、管理グループおよびサブスクリプションにまたがってポリシーを割り当てる。
- ポリシーのコンプライアンスを監視する。

Azure Policy の詳細については、「[コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)」を参照してください。

Azure ポリシーは、次のコンポーネントで構成されています。

- **ポリシー**は、規則です。
- **イニシアティブ**は、ポリシーのコレクションです。
- **割り当て**は、特定のスコープ (管理グループ、サブスクリプション、またはリソース グループ) にイニシアティブまたはポリシーを適用することです。

## <a name="view-security-policies"></a>セキュリティ ポリシーの表示

Security Center でセキュリティ ポリシーを表示するには:

1. **Security Center** ダッシュボードで **[セキュリティ ポリシー]** を選択します。

    ![[ポリシー管理] ウィンドウ](./media/security-center-policies/security-center-policy-mgt.png)

   **[ポリシー管理]** 画面には、管理グループ、サブスクリプション、およびワークスペースの数、および管理グループの構造が表示されます。

   > [!NOTE]
   > - Security Center のダッシュボードの **[サブスクリプションの対象範囲]** に表示されるサブスクリプション数が、 **[ポリシー管理]** に表示されるサブスクリプション数より多い場合があります。 [サブスクリプションの対象範囲] は、Standard、Free、および "対象外の" サブスクリプションの数を示します。 "対象外の" サブスクリプションでは、Security Center が有効になっておらず、 **[ポリシー管理]** に表示されません。
   >

2. ポリシーを参照するサブスクリプションまたは管理グループを選択します。

   - **[セキュリティ ポリシー]** 画面には、選択したサブスクリプションまたは管理グループに割り当てられているポリシーによって実行される動作が表示されます。
   - 上部のリンクを使用すると、サブスクリプションまたは管理グループに**割り当て**られているそれぞれのポリシーを開くことができます。 このリンクを使用すると、割り当てにアクセスしたり、ポリシーを編集または無効にしたりすることができます。 たとえば、割り当てられている特定のポリシーによってエンドポイントが実質的に保護されないようになっている場合、そのリンクを使用してポリシーにアクセスし、それを編集または無効にできます。
   - ポリシーの一覧では、サブスクリプションまたは管理グループに対して実際に適用されているポリシーを確認できます。 つまり、スコープに適用されている各ポリシーの設定が考慮され、ユーザーにはポリシーで実行されたアクションの累積的な結果が提供されます。 たとえば、1 つの割り当てでポリシーは無効になっているが、別では AuditIfNotExist に設定されている場合、累積的な効果により AuditIfNotExist が適用されます。 よりアクティブな効果が常に優先されます。
   - 設定できるポリシーの効果は、Append、Audit、AuditIfNotExists、Deny、DeployIfNotExists、Disabled です。 効果が適用されるしくみの詳細については、[Policy の効果](../governance/policy/concepts/effects.md)に関するページを参照してください。

   ![ポリシー画面](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - 割り当てられているポリシーを確認すると、複数の割り当てが表示され、各割り当てのそれぞれの構成を参照できます。

## <a name="edit-security-policies"></a>セキュリティ ポリシーの編集
[Azure Policy](../governance/policy/tutorials/create-and-manage.md) では、各 Azure サブスクリプションおよび管理グループの既定のセキュリティ ポリシーを編集できます。 セキュリティ ポリシーを変更するには、サブスクリプションまたはそれが含まれる管理グループの所有者、共同作成者、セキュリティ管理者のいずれかである必要があります。

Azure Policy でセキュリティ ポリシーを編集する方法の詳細については、「[コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)」を参照してください。

REST API 経由または Windows PowerShell を使用して、Azure Policy ポータルからセキュリティ ポリシーを編集できます。 次の例では、REST API を使用して編集する手順を示します。


## <a name="disable-security-policies"></a>セキュリティ ポリシーの無効化
既定のセキュリティ ポリシーによって、お使いの環境に関係のない推奨事項が生成される場合は、推奨事項を送信するポリシー定義を無効にして、推奨事項の生成を停止できます。
推奨事項の詳細については、[セキュリティに関する推奨事項の管理](security-center-recommendations.md)についてのページを参照してください。

1. Security Center 内の **[Policy & Compliance]\(ポリシーとコンプライアンス\)** セクションで **[セキュリティ ポリシー]** をクリックします。

   ![ポリシー管理](./media/tutorial-security-policy/policy-management.png)

2. 推奨設定を無効にするサブスクリプションまたは管理グループをクリックします。

   > [!Note]
   > 管理グループでは、そのポリシーがそのサブスクリプションに適用されることに注意してください。 そのため、サブスクリプションのポリシーを無効にしても、そのサブスクリプションが、同じポリシーをまだ使用している管理グループに属していると、引き続きポリシー推奨事項を受け入れることになります。 ポリシーは管理レベルから引き続き適用され、推奨事項は引き続き生成されます。

1. 割り当てられたポリシーをクリックします。

   ![ポリシーの無効化](./media/tutorial-security-policy/security-policy.png)

1. **[パラメーター]** セクションで、推奨事項を呼び出すポリシーのうち、無効にするものを検索します。次に、ドロップダウン リストから **[無効]** を選択します。

   ![ポリシーの無効化](./media/tutorial-security-policy/disable-policy.png)
1. **[Save]** をクリックします。
   > [!Note]
   > ポリシー無効化の変更が有効になるまでに、最大 12 時間かかる場合があります。


### <a name="configure-a-security-policy-using-the-rest-api"></a>REST API を使用してセキュリティ ポリシーを構成する

Azure Policy とのネイティブ統合の一環として、Azure Security Center では、Azure Policy の REST API を利用してポリシー割り当てを作成できます。 次の手順では、ポリシー割り当ての作成と既存の割り当てのカスタマイズの手順について説明します。 

Azure Policy で重要な概念: 

- **ポリシー定義**はルールです 

- **イニシアティブ**はポリシー定義 (ルール) のコレクションです 

- **割り当て**は、特定のスコープ (管理グループ、サブスクリプションなど) にイニシアティブまたはポリシーを適用することです 

Security Center では、そのセキュリティ ポリシーをすべて含んだ組み込みイニシアティブがあります。 Azure リソースに対する Security Center のポリシーを評価するために、評価する管理グループやサブスクリプションで割り当てを作成する必要があります。  

組み込みイニシアティブでは、既定で Security Center のポリシーがすべて有効になっています。 組み込みイニシアティブから特定のポリシーを無効にできます。たとえば、**Web アプリケーション ファイアウォール**の効果パラメーターを **[無効]** に変更することで、このポリシーを除くすべての Security Center のポリシーを適用できます。 

### <a name="api-examples"></a>API の例

以下の例では、次のように変数を置き換えてください。

- **{scope}** には、ポリシーを適用する管理グループまたはサブスクリプションの名前を入力します。
- **{poicyAssignmentName}** には、[関連するポリシー割り当ての名前](#policy-names)を入力します。
- **{name}** 名、またはポリシーの変更を承認する管理者の名前を入力します。

この例では、サブスクリプションまたは管理グループで組み込み Security Center イニシアティブを割り当てる方法を示します
 
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 

この例では、次のポリシーを無効にして、サブスクリプションで組み込み Security Center イニシアティブを割り当てる方法を示します。 

- システムの更新プログラム ("systemUpdatesMonitoringEffect") 

- セキュリティ構成 ("systemConfigurationsMonitoringEffect") 

- エンドポイント保護 ("endpointProtectionMonitoringEffect") 


    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 

この例では、割り当てを削除する方法を示します。

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


### ポリシー名参照<a name="policy-names"></a>

|Security Center でのポリシー名|Azure Policy で表示されるポリシー名 |ポリシー効果パラメーター名|
|----|----|----|
|SQL の暗号化 |暗号化されていない SQL データベースの Azure Security Center での監視 |sqlEncryptionMonitoringEffect| 
|SQL 監査 |未監査の SQL データベースの Azure Security Center での監視 |sqlAuditingMonitoringEffect|
|システムの更新プログラム |システムの更新プログラムの不足の Azure Security Center での監視 |systemUpdatesMonitoringEffect|
|ストレージ暗号化 |ストレージ アカウントでの BLOB 暗号化の不足の監査 |storageEncryptionMonitoringEffect|
|JIT ネットワーク アクセス |可能なネットワーク Just In Time (JIT) アクセスの Azure Security Center での監視 |jitNetworkAccessMonitoringEffect |
|アダプティブ アプリケーション制御 |可能なアプリのホワイトリスト登録の Azure Security Center での監視 |adaptiveApplicationControlsMonitoringEffect|
|ネットワーク セキュリティ グループ |制限が少なすぎるネットワーク アクセスの Azure Security Center での監視 |networkSecurityGroupsMonitoringEffect| 
|セキュリティ構成 |OS の脆弱性の Azure Security Center での監視 |systemConfigurationsMonitoringEffect| 
|エンドポイント保護 |Endpoint Protection の不足の Azure Security Center での監視 |endpointProtectionMonitoringEffect |
|ディスクの暗号化 |暗号化されていない VM ディスクの Azure Security Center での監視 |diskEncryptionMonitoringEffect|
|脆弱性評価 |VM の脆弱性の Azure Security Center での監視 |vulnerabilityAssessmentMonitoringEffect|
|Web アプリケーション ファイアウォール |保護されていない Web アプリケーションの Azure Security Center での監視 |webApplicationFirewallMonitoringEffect |
|次世代のファイアウォール |保護されていないネットワーク エンドポイントの Azure Security Center での監視| |


### <a name="who-can-edit-security-policies"></a>セキュリティ ポリシーを編集できるユーザーは誰ですか。
Security Center ではロールベースのアクセス制御 (RBAC) が使用されています。RBAC が提供する組み込みのロールは、Azure でユーザー、グループ、サービスに割り当てることができます。 ユーザーが Security Center を開くと、アクセス権のあるリソースに関する情報のみが表示されます。 これは、サブスクリプションまたはリソースが属するリソース グループについて、所有者、共同作成者、閲覧者のいずれかのロールがユーザーに割り当てられていることを意味します。 これらのロールに加え、Security Center には、次の 2 つの固有のロールがあります。

- セキュリティ閲覧者:Security Center に対する閲覧権限を持ちます。推奨事項、アラート、ポリシー、および正常性を確認できますが、変更を加えることはできません。
- セキュリティ管理者:セキュリティ閲覧者と同じ閲覧権限を持ちますが、セキュリティ ポリシーの更新と推奨事項とアラートの解除を実行することもできます。



## <a name="next-steps"></a>次の手順
この記事では、Azure Policy でのセキュリティ ポリシーの編集方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md):Azure Security Center について計画し、設計上の考慮事項を理解する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md):Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md):セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md):パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center に対するテナント全体の可視性を確保する](security-center-management-groups.md):Azure Security Center の管理グループを設定する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md):このサービスの使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/):Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

Azure Policy について詳しくは、「[Azure Policy とは](../governance/policy/overview.md)」をご覧ください。
