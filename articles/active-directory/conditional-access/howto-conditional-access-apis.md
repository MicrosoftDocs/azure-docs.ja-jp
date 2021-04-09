---
title: 条件付きアクセス API および PowerShell - Azure Active Directory
description: Azure AD 条件付きアクセス API と PowerShell を使用してコードのようにポリシーを管理する
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d4dd6e64053af41d66e3accbb7fc9b21d951fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860215"
---
# <a name="conditional-access-programmatic-access"></a>条件付きアクセス:プログラムによるアクセス

多くの組織では、できる限り多くの環境をコードのように管理する必要性を表明してきました。 Microsoft Graph を使用すると、環境内の他のコードと同じように、条件付きアクセス ポリシーを扱うことができます。

Microsoft Graph では、Microsoft 365、Windows 10、および Enterprise Mobility + Security 内のデータを操作するために組織が使用できる統合型プログラミング モデルを提供しています。 Microsoft Graph について詳しくは、「[Microsoft Graph の概要](/graph/overview)」の記事をご覧ください。

![Graph を構成する主要なリソースと関係を示す画像](./media/howto-conditional-access-apis/microsoft-graph.png)

以降の例は、サポートを備えていないことを想定して、提示されています。 これらの例は、組織内のツールの基本として使用できます。 

次の例の多くでは、[マネージド ID](../managed-identities-azure-resources/overview.md)、[Logic Apps](../../logic-apps/logic-apps-overview.md)、[OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage)、[Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)、および [Azure Key Vault](../../key-vault/general/overview.md) のようなツールが使用されています。

## <a name="configure"></a>構成

### <a name="powershell"></a>PowerShell

多くの管理者にとって、PowerShell は既に理解しているスクリプト ツールです。 次の例は、[Azure AD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureAD)を使用して、条件付きアクセス ポリシーを管理する方法を示しています。

- [Azure AD PowerShell コマンドを使用して条件付きアクセス ポリシーを構成する](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Graph API

この例では、条件付きアクセスの Graph API で使用できる基本的な作成、読み取り、更新、および削除 (CRUD) のオプションを示しています。 また、この例には、一部のサンプル ポリシーを作成するために使用できるいくつかの JSON テンプレートも含まれています。

- [Microsoft Graph API 呼び出しを使って条件付きアクセス ポリシーを構成する](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>テンプレートを使用して構成する

条件付きアクセス API を使用し、テンプレートを使って運用前環境に条件付きアクセス ポリシーをデプロイします。

- [Microsoft Graph API テンプレートを使って条件付きアクセス ポリシーを構成する](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>テスト

この例では、運用前環境などから運用環境などの別の環境に条件付きアクセス ポリシーをコピーできる承認ワークフローを使用して、より安全なデプロイ プラクティスをモデル化します。

- [条件付きアクセス ポリシーをテスト環境から昇格する](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>配置

この例では、段階的なデプロイを実行して条件付きアクセス ポリシーを対象ユーザーに徐々に適用するメカニズムを提示します。これにより、副次的な影響を管理し、問題を早期に発見することができます。

- [承認ワークフローを使って条件付きアクセス ポリシーを運用環境にデプロイする](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>モニター

この例では、条件付きアクセス ポリシーの変更を一定期間にわたって監視するメカニズムを示し、キー ポリシーが変更されたときにアラートをトリガーすることができます。

- [変更するデプロイ済みの条件付きアクセス ポリシーを監視してアラートをトリガーする](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>管理する

### <a name="backup-and-restore"></a>バックアップと復元

この例を使用して、Teams での承認がある条件付きアクセス ポリシーのバックアップと復元を自動化します。

- [Microsoft Graph API 呼び出しを使って条件付きアクセス ポリシーのバックアップと復元のプロセスを管理する](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>緊急アクセス アカウント

複数の管理者が条件付きアクセス ポリシーを作成し、[緊急アクセス アカウント](../roles/security-emergency-access.md)をこれらのポリシーの除外として追加することを忘れてしまう場合があります。 この例では、すべてのポリシーが指定した緊急アクセス アカウントを含むように更新されることを保証します。

- [Microsoft Graph API 呼び出しを使って条件付きアクセス ポリシーへの緊急アクセス アカウントの割り当てを管理する](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>代替計画

常に期待どおりに機能するわけではありません。そのような状況に陥ったら、作業を続行できる状態に戻す方法が必要になります。 次の例では、既知の適切な代替計画にポリシーを戻して、他の条件付きアクセス ポリシーを無効にする方法を示しています。

- [Microsoft Graph API 呼び出しを使って条件付きアクセスの代替ポリシーのアクティブ化を管理する](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>コミュニティへの貢献

これらのサンプルは、[GitHub リポジトリ](https://github.com/Azure-Samples/azure-ad-conditional-access-apis)で入手できます。 Microsoft では、完全な GitHub のイシューとプル リクエストを通じたコミュニティへの貢献を積極的にサポートしています。

## <a name="next-steps"></a>次の手順

- [Microsoft Graph の概要](/graph/overview)

- [条件付きアクセス API](/graph/api/resources/conditionalaccesspolicy)

- [ネームド ロケーション API](/graph/api/resources/namedlocation)
