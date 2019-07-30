---
title: ベースライン ポリシーでサービス管理のために MFA を要求する (プレビュー) - Azure Active Directory
description: Azure Resource Manager で MFA を要求する条件付きアクセス ポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 203b752f9da67ebf60e373fe7ce0893b4fd7fcb5
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560960"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>ベースライン ポリシー:サービス管理のために MFA を要求する (プレビュー)

組織ではさまざまな Azure サービスが使用されている可能性があります。 これらのサービスは、Azure Resource Manager API を使用して管理できます。

* Azure ポータル
* Azure PowerShell
* Azure CLI

Azure Resource Manager を使用してご自身のサービスを管理する操作は、高い権限が与えられているアクションです。 Azure Resource Manager では、サービス設定、サブスクリプションの課金など、テナント全体の構成を変更できます。 単一要素認証は、フィッシング、パスワード スプレーなどの幅広い攻撃に対して脆弱です。 したがって、Azure Resource Manager へのアクセスと構成の更新を求めるユーザーに対しては、アクセスを許可する前に、多要素認証を要求してそのユーザーの ID を確認することが重要です。

**サービス管理のために MFA を要求する**ポリシーは、Azure portal、Azure PowerShell、または Azure CLI にアクセスするすべてのユーザーに対して MFA を要求する[ベースライン ポリシー](concept-baseline-protection.md)です。 このポリシーは、Azure Resource Manager にアクセスするすべてのユーザーに対して、そのユーザーが管理者かどうかに関係なく適用されます。

このポリシーがテナントで有効になると、Azure 管理リソースにログインするすべてのユーザーに多要素認証が求められます。 ユーザーが MFA に登録されていない場合、そのユーザーが操作を続行するには、Microsoft Authenticator アプリを使用してユーザー登録を行う必要があります。

[Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) を使用して対話型サインインを実行するには、[Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用します。

```PowerShell
Connect-AzAccount
```

このコマンドレットを実行すると、トークン文字列が表示されます。 サインインするには、この文字列をコピーし、ブラウザーで  [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  に貼り付けます。 Azure に接続するために PowerShell セッションが認証されます。

[Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) を使用して対話型サインインを実行するには、 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login)  コマンドを実行します。

```azurecli
az login
```

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザー ページを開いて、お使いのブラウザーで  [https://aka.ms/devicelogin](https://aka.ms/devicelogin) に移動した後、コマンド ラインの指示に従って承認コードを入力します。 その後、ブラウザーでアカウントの資格情報を使用してサインインします。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

**サービス管理のために MFA を要求する**ポリシーは、すべての Azure Resource Manager ユーザーに適用されます。したがって、デプロイが確実かつスムーズに行われるようにするための考慮事項がいくつかあります。 たとえば、MFA を実行できない、または実行すべきではないユーザーやサービス プリンシパルを Azure AD で特定する、ご自身の組織で使用されている先進認証に対応していないアプリケーションやクライアントを特定する、といった考慮事項です。

## <a name="enable-the-baseline-policy"></a>ベースライン ポリシーを有効にする

**ベースライン ポリシー: サービス管理のために MFA を要求する (プレビュー)** ポリシーは事前構成され、Azure portal の [条件付きアクセス] ブレードに移動すると上部に表示されます。

このポリシーを有効にして管理者を保護するには:

1.  **Azure portal**  にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[条件付きアクセス]** の順に移動します。
1. ポリシーの一覧で、 **[Baseline policy: Require MFA for service management (preview)]\(サービス管理のために MFA を要求する (プレビュー)\)** を選択します。
1. **[ポリシーを有効にする]** を **[ポリシーをすぐに使用する]** に設定します。
1.  **[保存]** をクリックします。

## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

* [条件付きアクセス ベースライン保護ポリシー](concept-baseline-protection.md)
* [ID インフラストラクチャをセキュリティ保護する 5 つのステップ](../../security/azure-ad-secure-steps.md)
* [Azure Active Directory の条件付きアクセスとは](overview.md)