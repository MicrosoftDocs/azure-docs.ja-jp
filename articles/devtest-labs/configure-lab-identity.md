---
title: Azure DevTest Labs でラボ ID を構成する
description: Azure DevTest でラボ ID を構成する方法について説明します。
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88719575"
---
# <a name="configure-a-lab-identity"></a>ラボ ID を構成する

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードで資格情報をどのように管理するかです。 資格情報を安全に保つことは重要な課題です。 資格情報は開発者のワークステーションに表示されないこと、またソース管理にチェックインされないことが理想です。 Azure Key Vault では資格情報、シークレット、その他のキーを安全に保管する手段が提供されますが、それらを取得するには、コードで Key Vault に対して認証を行う必要があります。 

Azure Active Directory (Azure AD) の Azure リソース用マネージド ID 機能により、この問題が解決されます。 Azure AD で自動的に管理される ID を Azure サービスに提供する機能となります。 この ID を使用すれば、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。 [Azure でのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。 

マネージド ID には、次の 2 種類があります。 

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID  

 **システム割り当てマネージド ID** は、Azure サービス インスタンス上で直接有効にされます。 この ID を有効にすると、そのインスタンスのサブスクリプションによって信頼されている Azure AD テナントに対し、対応するインスタンスの ID が Azure によって作成されます。 ID が作成されると、その資格情報がインスタンスにプロビジョニングされます。 システム割り当て ID のライフ サイクルは、その ID が有効にされた Azure サービス インスタンスに直接関連付けられます。 インスタンスが削除された場合、Azure は Azure AD の資格情報および ID を自動的にクリーンアップします。 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>ラボのシステム割り当て ID を使用するシナリオ  

すべての DevTest Labs は、ラボの有効期間中は有効なシステム割り当て ID を使用して作成されます。 システム割り当て ID は、次の目的に使用されます。  

- 複数の VM およびサービスとしてのプラットフォーム環境を稼働させるために使用される [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) ベースのすべてのデプロイは、ラボのシステム割り当て ID を使用して実行されます  
- カスタマー マネージド キーを使用するラボ ディスク向けのディスク暗号化は、ラボのシステム割り当て ID によってサポートされます。 ディスク暗号化セットにアクセスするためのラボの ID に対する明示的なアクセスを提供すると、ラボではユーザーに代わってすべての仮想マシン ディスクを暗号化することができます。 カスタマー マネージド キーを使用してラボ ディスクの[ディスク暗号化を有効にする方法](encrypt-disks-customer-managed-keys.md)の詳細を確認してください。  

### <a name="configure-identity"></a>ID の構成

このセクションでは、ラボの ID ポリシーを構成する方法について説明します。

> [!NOTE]
> 2020 年 8 月 10 日より前に作成されたラボでは、システム割り当て ID はオフに設定されます。 ラボの所有者は、前のセクションで示した目的にラボを使用する場合、それをオンにすることができます。  
>
> 2020 年 8 月 10 日より後に作成された新しいラボでは、ラボのシステム割り当て ID は既定でオンに設定され、ラボの所有者はラボのライフサイクルの間、これをオフにすることはできません。  

1. [Azure portal](https://portal.azure.com) にサインインする
1. **DevTest Labs** を見つけます。
1. ラボの一覧から、目的のラボを選択します。
1. **[構成とポリシー]**  ->  **[ID (プレビュー)]** を選択します。 

> [!div class="mx-imgBorder"]
> ![ID の構成](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID  

ユーザー割り当てマネージド ID は、スタンドアロンの Azure リソースとして作成されます。 作成プロセスで、使用されているサブスクリプションによって信頼されている Azure AD テナントに、Azure が ID を作成します。 作成された ID は、1 つまたは複数の Azure サービス インスタンスに割り当てることができます。 ユーザー割り当て ID のライフ サイクルは、その ID が割り当てられている Azure サービス インスタンスのライフサイクルとは個別に管理されます。 

DevTest Labs によって、仮想マシンと Azure Resource Manager ベースの環境の両方について、ユーザー割り当て ID がサポートされています。  詳細については、次のトピックを参照してください。

- [ユーザー割り当て ID を追加してラボの Azure Resource Manager 環境をデプロイする](use-managed-identities-environments.md)
- [ユーザー割り当て ID を追加してラボの仮想マシンをデプロイする](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>次のステップ

[コスト管理の構成](devtest-lab-configure-cost-management.md)を確認します