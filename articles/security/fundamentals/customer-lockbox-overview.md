---
title: Microsoft Azure 用カスタマー ロックボックス
description: Microsoft Azure 用カスタマー ロックボックスの技術の概要。Microsoft が顧客データにアクセスする必要がある場合にクラウド プロバイダーのアクセスを制御する機能があります。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 02/19/2021
ms.openlocfilehash: 0146e4fcaf70d37975dc587a266c47bf4b3f4601
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461676"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure 用カスタマー ロックボックス

> [!NOTE]
> この機能を使用するには、**Developer** レベル以上の [Azure サポート プラン](https://azure.microsoft.com/support/plans/)が組織に必要です。

Microsoft Azure 用カスタマー ロックボックスには、お客様が顧客データへのアクセス要求を承認または拒否するインターフェイスが用意されています。 これは、Microsoft のエンジニアがサポート リクエストの際に顧客データにアクセスする必要がある場合に使用されます。

この記事では、カスタマー ロックボックスを有効にする方法、およびロックボックス要求を開始する、追跡する、後のレビューと監査のために保存する方法について説明します。

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios-general-availability"></a>サポートされるサービスとシナリオ (一般提供)

カスタマー ロックボックスで一般提供されるようになったサービスは次のとおりです。

- Azure API Management
- Azure App Service
- Azure Cognitive Services
- Azure Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure Data Explorer
- Azure Data Factory
- Azure Database for PostgreSQL
- Azure Functions
- Azure HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL Database
- Azure サブスクリプションの譲渡
- Azure Synapse Analytics
- Azure の仮想マシン (リモート デスクトップ アクセス、メモリ ダンプへのアクセス、マネージド ディスクを含む)

## <a name="enable-customer-lockbox"></a>カスタマー ロックボックスを有効にする

カスタマー ロックボックス ブレードの [[Adminisitration]\(管理\) モジュール](https://aka.ms/customerlockbox/administration)からカスタマー ロックボックスを有効にできるようになりました。  

> [!NOTE]
> カスタマー ロックボックスを有効にするには、ユーザー アカウントに[全体管理者の役割が割り当てられている](../../active-directory/roles/manage-roles-portal.md)必要があります。

## <a name="workflow"></a>ワークフロー

次の手順は、カスタマー ロックボックス要求の一般的なワークフローの概要です。

1. 組織の誰かに Azure のワークロードの問題があります。

2. このユーザーが問題のトラブルシューティング行っても解決できない場合、[Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) からサポート チケットを開きます。 このチケットは Azure カスタマー サポート エンジニアに割り当てられます。

3. Azure サポート エンジニアがサービス要求を確認し、問題を解決するための次の手順を判断します。

4. サポート エンジニアが標準のツールとテレメトリを使用して問題のトラブルシューティングを行うことができない場合、次の手順は、Just-In-Time (JIT) アクセス サービスを使用して管理者特権のアクセス許可を要求することです。 問題は Azure DevOps チームにエスカレートされているため、これは元のサポート エンジニアまたは別のエンジニアからの要求である可能性があります。

5. Azure エンジニアがアクセス要求を送信した後、Just-In-Time サービスは次のような要素を考慮して要求を評価します。
    - リソースのスコープ
    - 要求送信者が分離 ID か、多要素認証を使用しているか
    - アクセス許可レベル

    JIT ルールに基づき、この要求には Microsoft 社内承認者からの承認も含まれる場合があります。 たとえば、承認者は、カスタマー サポートのリーダーや DevOps マネージャーの場合があります。

6. 要求に顧客データへの直接アクセスが必要な場合、カスタマー ロックボックス要求が開始されます。 たとえば、お客様の仮想マシンへのリモート デスクトップ アクセスです。

    要求が **[Customer Notified]\(お客様に通知済み\)** 状態になり、アクセスを許可する前のお客様の承認待ちになります。

7. お客様の組織で、Azure サブスクリプションの[所有者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)を持つユーザーに、保留中のアクセス要求について通知するメールが Microsoft から送信されます。 カスタマー ロックボックス要求では、このユーザーが承認者に指定されます。

    電子メールの例:

    ![Azure カスタマー ロックボックス - メール通知](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. メール通知には、**カスタマー ロックボックス** ブレードの [Administration]\(管理\) モジュールへのリンクが記載されます。 指定された承認者は、このリンクを使用し、Azure portal にサインインして、組織がカスタマー ロックボックスについて保留しているすべての要求を表示します。

    ![Azure カスタマー ロックボックス - ランディング ページ](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   要求は、カスタマー キューに 4 日間残ります。 この時間が経過すると、アクセス要求は自動的に期限切れになり、Microsoft のエンジニアにはアクセスが許可されません。

9. 保留中の要求の詳細を取得するにために、指定された承認者は **[保留中の要求]** からロックボックス要求を選択できます。

    ![Azure カスタマー ロックボックス - 保留中の要求を表示する](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 指定された承認者は **[サービス要求 ID]** を選択して、元のユーザーが作成したサポート チケット要求を表示することもできます。 この情報には、Microsoft サポートが対応している理由と、報告された問題の履歴に関する状況が提供されます。 次に例を示します。

    ![Azure カスタマー ロックボックス - サポート チケット要求を表示する](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 要求を確認した後、指定された承認者は **[承認]** または **[拒否]** を選択します。

    ![Azure カスタマー ロックボックス - [承認] または [拒否] を選択する](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    選択の結果は次のようになります。
    - **承認**:Microsoft のエンジニアにアクセスが許可されます。 アクセスが許可されるのは既定の 8 時間です。
    - **拒否**:Microsoft のエンジニアによる管理者特権のアクセス要求は拒否され、以降の操作は行われません。

監査のために、このワークフローで実行されたアクションは [[Customer Lockbox request logs]\(カスタマー ロックボックス要求ログ\)](#auditing-logs) に記録されます。

## <a name="auditing-logs"></a>監査ログ

カスタマー ロックボックス ログはアクティビティ ログに格納されます。 Azure portal で **[アクティビティ ログ]** を選択し、カスタマー ロックボックス要求に関連する監査情報を表示します。 次のように特定のアクションをフィルター処理することができます。
- **[Deny Lockbox Request]\(ロックボックス要求を拒否する\)**
- **[Create Lockbox Request]\(ロックボックス要求を作成する\)**
- **[Approve Lockbox Request]\(ロックボックス要求を承認する\)**
- **[Lockbox Request Expiry]\(ロックボックス要求の有効期限\)**

例

![Azure カスタマー ロックボックス - アクティビティ ログ](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>カスタマー ロックボックスと Azure セキュリティ ベンチマークとの統合

カスタマー ロックボックス適用性を対象とする新しいベースライン コントロール ([3.13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) が Azure セキュリティ ベンチマークに導入されました。 お客様は、ベンチマークを利用して、サービスのカスタマー ロックボックス適用性を確認できるようになりました。

## <a name="exclusions"></a>除外

カスタマー ロックボックス要求は、次のエンジニアリング サポート シナリオではトリガーされません。

- Microsoft のエンジニアは、標準的な操作手順から外れるアクティビティを実行する必要があります。 たとえば、予期しないシナリオや予測不可能なシナリオでサービスを回復または復元する場合などです。
- Microsoft のエンジニアは、トラブルシューティングの一環として Azure プラットフォームにアクセスし、意図せずに顧客データへのアクセス権を持ちます。 たとえば、Azure Network チームはトラブルシューティングを行い、その結果、ネットワーク デバイスでパケット キャプチャが行われます。 このシナリオでは、お客様が送信中のデータを暗号化した場合、エンジニアはデータを読み取ることができません。

## <a name="next-steps"></a>次のステップ

カスタマー ロックボックスは、**Developer** レベル以上の [Azure サポート プラン](https://azure.microsoft.com/support/plans/)をお持ちのすべてのお客様が利用できます。 カスタマー ロックボックス ブレードの [[Adminisitration]\(管理\) モジュール](https://aka.ms/customerlockbox/administration)からカスタマー ロックボックスを有効にできます。

サポート ケースを進めるためにこのアクションが必要な場合は、Microsoft のエンジニアによってカスタマー ロックボックス要求が開始されます。
