---
title: Azure ロールベースのアクセス制御への移行 | Microsoft Docs
description: コンテナー アクセス ポリシーから Azure ロールに移行する方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e7a8fd53e78e1aeab9db5af0432d0c3f1d786823
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526954"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>コンテナー アクセス ポリシーから Azure ロールベースのアクセス制御のアクセス許可モデルへの移行

コンテナー アクセス ポリシー モデルは、キー、シークレット、証明書へのアクセスを提供するために Key Vault に組み込まれた既存の承認システムです。 Key Vault スコープで、セキュリティ プリンシパル (ユーザー、グループ、サービス プリンシパル、マネージド ID) に個々のアクセス許可を割り当てることにより、アクセスを制御できます。 

Azure ロールベースのアクセス制御 (Azure RBAC) は [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 上に構築された承認システムであり、Azure リソースに対するアクセスをきめ細かく管理できます。 Azure RBAC では、ロールの割り当てを作成することによって、リソースへのアクセスを制御します。これには、セキュリティ プリンシパル、ロールの定義 (定義済みの一連のアクセス許可)、スコープ (リソースのグループまたは個々のリソース) の 3 つの要素が含まれます。 詳細については、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) に関するページを参照してください。

Azure RBAC に移行する前に、その利点と制限事項を理解しておくことが重要です。

コンテナー アクセス ポリシーよりも優れている Azure RBAC の主な利点は以下のとおりです。
- Azure リソースの統一されたアクセス制御モデルを提供 - Azure サービス間で同じ API を使用
- 管理者向けの一元化されたアクセス管理 - すべての Azure リソースを 1 つのビューで管理
- 時間ベースのアクセス制御を実現する [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) との統合
- 拒否の割り当て - 特定のスコープでセキュリティ プリンシパルを除外する機能 詳細については、「[Azure 拒否割り当てについて](../../role-based-access-control/deny-assignments.md)」を参照してください

Azure RBAC の短所は以下のとおりです。
- ロールの割り当ての待機時間 - ロールの割り当てが適用されるまでに数分かかることがあります。 コンテナー アクセス ポリシーはすぐに割り当てられます。
- ロールの割り当て数が制限 - サブスクリプションあたり 2000 のロール割り当てと Key Vault あたり 1024 のアクセス ポリシー

## <a name="access-policies-to-azure-roles-mapping"></a>Azure ロール マッピングへのアクセス ポリシー

Azure RBAC には、ユーザー、グループ、サービス プリンシパル、マネージド ID に割り当てることのできる Azure 組み込みロールがいくつかあります。 組み込みロールが組織の特定のニーズを満たさない場合は、独自の [Azure カスタム ロール](../../role-based-access-control/custom-roles.md)を作成することができます。

キー、証明書、およびシークレットのアクセス管理のための Key Vault 組み込みロールは以下のとおりです。
- Key Vault Administrator
- Key Vault Reader
- Key Vault Certificate Officer
- Key Vault Crypto Officer
- Key Vault Crypto User
- Key Vault Crypto Service Encryption User
- Key Vault Secrets Officer
- Key Vault Secrets User

既存の組み込みロールの詳細については、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

コンテナー アクセス ポリシーは、個別に選択したアクセス許可、または定義済みのアクセス許可テンプレートを使用して割り当てることができます。

アクセス ポリシーの定義済みアクセス許可テンプレート:
- キー、シークレット、および証明書の管理
- キーおよびシークレットの管理
- シークレットおよび証明書の管理
- キー管理
- シークレットの管理
- 証明書管理
- SQL Server コネクタ
- Azure Data Lake Storage または Azure Storage
- Azure Backup
- Exchange Online カスタマー キー
- SharePoint Online カスタマー キー
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Azure ロール マッピングへのアクセス ポリシー テンプレート
| アクセス ポリシー テンプレート | 操作 | Azure ロール |
| --- | --- | --- |
| キー、シークレット、および証明書の管理 | キー: すべての操作 <br>証明書: すべての操作<br>シークレット: すべての操作 | Key Vault Administrator |
| キーおよびシークレットの管理 | キー: すべての操作 <br>シークレット: すべての操作| Key Vault Crypto Officer <br> Key Vault Secrets Officer |
| シークレットおよび証明書の管理 | 証明書: すべての操作 <br>シークレット: すべての操作| Key Vault Certificates Officer <br> Key Vault Secrets Officer|
| キー管理 | キー: すべての操作| Key Vault Crypto Officer|
| シークレットの管理 | シークレット: すべての操作| Key Vault Secrets Officer|
| 証明書管理 | 証明書: すべての操作 | Key Vault Certificates Officer|
| SQL Server コネクタ | キー: 取得、一覧表示、キーを折り返す、キーの折り返しを解除 | Key Vault Crypto Service Encryption User|
| Azure Data Lake Storage または Azure Storage | キー: 取得、一覧表示、キーの折り返しを解除 | N/A<br> カスタム ロールが必要|
| Azure Backup | キー: 取得、一覧表示、バックアップ<br> 証明書: 取得、一覧表示、バックアップ | N/A<br> カスタム ロールが必要|
| Exchange Online カスタマー キー | キー: 取得、一覧表示、キーを折り返す、キーの折り返しを解除 | Key Vault Crypto Service Encryption User|
| Exchange Online カスタマー キー | キー: 取得、一覧表示、キーを折り返す、キーの折り返しを解除 | Key Vault Crypto Service Encryption User|
| Azure Information BYOK | キー: 取得、復号化、署名 | N/A<br>カスタム ロールが必要|


## <a name="assignment-scopes-mapping"></a>割り当てスコープのマッピング  

Key Vault 用の Azure RBAC では、次のスコープでロールを割り当てることができます。
- 管理グループ
- サブスクリプション
- Resource group
- Key Vault リソース
- 個々のキー、シークレット、証明書

コンテナー アクセス ポリシーのアクセス許可モデルは、Key Vault リソース レベルでのみポリシーを割り当てることができるように制限されています。 

一般に、アプリケーションごとに 1 つのキー コンテナーを持ち、キー コンテナー レベルでアクセスを管理することをお勧めします。 他のスコープでアクセスを管理すると、アクセス管理が簡単になる場合があります。

- **インフラストラクチャ、セキュリティ管理者、およびオペレーター: 管理グループ、サブスクリプション、またはリソース グループ レベルのキー コンテナーのグループを、コンテナー アクセス ポリシーで管理するには、キー コンテナーごとにポリシーを維持する必要があります。 Azure RBAC では、管理グループ、サブスクリプション、またはリソース グループで 1 つのロールの割り当てを作成できます。 この割り当ては、同じスコープで作成された新しいキー コンテナーに適用されます。 このシナリオでは、永続アクセス権を付与するよりも、ジャストインタイム アクセスで Privileged Identity Management を使用することをお勧めします。
 
- **アプリケーション: 一部のシナリオでは、あるアプリケーションから他のアプリケーションへ、シークレットを共有する必要があります。 コンテナー アクセス ポリシーを使用して、すべてのシークレットへアクセス許可を付与しないように、個別のキー コンテナーを作成する必要がありました。 Azure RBAC では、単一のキー コンテナーを使用する代わりに、個々のシークレットのスコープでロールを割り当てることができます。

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>コンテナー アクセス ポリシーの Azure RBAC への移行の手順
Azure RBAC とコンテナー アクセス ポリシーのアクセス許可モデルにはさまざまな違いがあります。 移行中の停止を避けるために、以下の手順をお勧めします。
 
1. **ロールの特定と割り当て**: 上記のマッピングの表に基づいて組み込みのロールを特定し、必要に応じてカスタム ロールを作成します。 スコープのマッピングのガイダンスに基づいて、スコープでロールを割り当てます。 キー コンテナーにロールを割り当てる方法の詳細については、[Azure ロールベースのアクセス制御を使用した Key Vault へのアクセスの付与](rbac-guide.md)に関するページを参照してください
1. **ロールの割り当ての検証**: Azure RBAC でのロールの割り当ては、反映されるまでに数分かかることがあります。 ロールの割り当てを確認する方法のガイドについては、[スコープでロールの割り当てを一覧表示する](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)方法に関するページを参照してください
1. **キー コンテナーでの監視とアラートの構成**: ログの記録を有効にし、アクセス拒否の例外に対するアラートを設定することが重要です。 詳細については、「[Azure Key Vault の監視とアラート](./alert.md)」を参照してください。
1. **Key Vault で Azure ロールベースのアクセス制御の許可モデルを設定**: Azure RBAC のアクセス許可モデルを有効にすると、既存のアクセス ポリシーがすべて無効になります。 エラーが発生した場合は、既存のすべてのアクセス ポリシーをそのままに、アクセス許可モデルを元通りに切り替えることができます。

> [!NOTE]
> アクセス許可モデルを変更するには、[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールの一部である、"Microsoft.Authorization/roleAssignments/write" アクセス許可が必要です。 "サービス管理者" や "共同管理者" などの従来のサブスクリプション管理者ロールはサポートされていません。

> [!NOTE]
> Azure RBAC アクセス許可モデルが有効になっている場合、アクセス ポリシーの更新を試みるすべてのスクリプトが失敗します。 Azure RBAC を使用するように、これらのスクリプトを更新することが重要です。

## <a name="troubleshooting"></a>トラブルシューティング
-  数分経っても、ロールの割り当てが機能しません - ロールの割り当てに時間がかかる場合があります。 そのようなケースに対応するために、コードに再試行ロジックを記述することが重要です。
- Key Vault が削除 (論理的な削除) されてから復旧した場合に、ロールの割り当てが消失します - 現時点では、これはすべての Azure サービスにおける論理的な削除機能の制限となっています。 復旧後にすべてのロールの割り当てを再作成する必要があります。    

## <a name="learn-more"></a>詳細情報

- [Azure RBAC の概要](../../role-based-access-control/overview.md)
- [カスタム ロールのチュートリアル](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)