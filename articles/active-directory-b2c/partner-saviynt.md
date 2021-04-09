---
title: Saviynt と Azure Active Directory B2C を構成するチュートリアル
titleSuffix: Azure AD B2C
description: アプリケーション間の統合のために Saviynt を使用して Azure Active Directory B2C を構成し、IT 最新化の高率を高め、セキュリティ、ガバナンス、およびコンプライアンスを向上させるチュートリアルです。 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b1889a693c08ce78a8bd06b6fe348c0c67bf3812
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465676"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Saviynt と Azure Active Directory B2C を構成するチュートリアル

このサンプル チュートリアルでは、Azure Active Directory (AD) B2C を [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) と統合する方法についてのガイダンスを提供します。 Saviynt の Security Manager プラットフォームは、1 つの統合されたプラットフォームであり、現在のビジネス ニーズに応じた可視性、セキュリティ、およびガバナンスを提供します。 Saviynt には、アプリケーションのリスクとガバナンス、インフラストラクチャ管理、特権アカウント管理、および顧客リスク分析が組み込まれています。

このサンプル チュートリアルでは、Azure AD B2C ユーザーにきめ細かいアクセス制御ベースの委任管理を提供するように Saviynt を設定します。 Saviynt では、ユーザーが Azure AD B2C ユーザーを管理する権限を持っているかどうかを判断するために、次のチェックが実行されます。

- ユーザーが特定の操作を実行できるかどうかを判断するための機能レベルのセキュリティ。 たとえば、ユーザーの作成、ユーザーの更新、ユーザー パスワードのリセットなどです。

- ユーザー管理操作中にユーザーが別のユーザーの特定の属性について読み取りまたは書き込みを行うことができるかどうかを判断するためのフィールド レベルのセキュリティ。 たとえば、ヘルプ デスク エージェントは電話番号のみを更新することができ、他のすべての属性は読み取り専用です。

- ユーザーが特定のユーザーに対して特定の操作を実行できるかどうかを判断するためのデータ レベルのセキュリティ。 たとえば、英国地域のヘルプ デスク管理者は、英国のユーザーのみを管理できます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- [Azure AD B2C テナント](./tutorial-create-tenant.md)。 テナントは Azure サブスクリプションにリンクされています。

- Saviynt の[サブスクリプション](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>シナリオの説明

Saviynt 統合には、次のコンポーネントが含まれています。

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) - 顧客のサインアップ、サインイン、およびプロファイルの管理方法のカスタム制御を可能にするサービスとして企業-消費者間 ID。

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) - Azure AD B2C ユーザーのユーザー ライフサイクル管理とアクセス ガバナンスのためのきめ細かい委任管理機能を提供する ID ガバナンス プラットフォーム。  

- [Microsoft Graph API](/graph/use-the-api) - この API には、Saviynt で Azure AD B2C ユーザーと Azure AD B2C でのアクセスを管理するためのインターフェイスが用意されています。

次のアーキテクチャの図に、この実装を示します。

![saviynt のアーキテクチャ図を示す画像](./media/partner-saviynt/saviynt-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | 代理管理者が、Saviynt を介して Azure AD B2C ユーザーの管理操作を開始します。
| 2. | Saviynt では、承認エンジンが使用され、代理管理者が特定の操作を実行できるかどうかが検証されます。
| 3. | Saviynt の承認エンジンから、承認の成功または失敗の応答が送信されます。
| 4. | Saviynt によって、代理管理者は必要な操作を実行できるようになります。
| 5. | Saviynt から、Azure AD B2C でユーザーを管理するようにユーザー属性を指定した Microsoft Graph API が呼び出されます。
| 6. | Microsoft Graph API によって、Azure AD B2C のユーザーの作成、更新、または削除が行われます。
| 7. | Azure AD B2C から成功または失敗の応答が送信されます。
| 8. | Microsoft Graph API から Saviynt に応答が返されます。

## <a name="onboard-with-saviynt"></a>Saviynt によるオンボード

1. Saviynt アカウントを作成するには、[Saviynt](https://saviynt.com/contact-us/) にお問い合わせください

2. 代理管理ポリシーを作成し、さまざまな役割を持つ代理管理者としてユーザーを割り当てます。

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Azure AD B2C と Saviynt を構成する

### <a name="create-an-azure-ad-application-for-saviynt"></a>Saviynt 用 Azure AD アプリケーションを作成する

1. [Azure portal](https://portal.azure.com/#home) にサインインします。

2. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。

3. Azure portal で、 **[Azure AD B2C]** を検索して選択します。

4. **[アプリの登録]**  >  **[新しい登録]** の順に選びます。

5. アプリケーションの名前を入力します。 たとえば、Saviynt です。 **[作成]** を選択します。

6. **[API のアクセス許可]** に移動し、 **[+ アクセス許可の追加]** を選択します。

7. [API アクセス許可の要求] ページが表示されます。 **[Microsoft API]** タブを選択し、一般的に使用される Microsoft API として **[Microsoft Graph]** を選択します。

8. 次のページに進み、 **[アプリケーションのアクセス許可]** を選択します。

9. **[ディレクトリ]** を選択し、 **[Directory.Read.All]** と **[Directory.ReadWrite.All]** の各チェックボックスをオンにします。

10. **[アクセス許可の追加]** を選択します。 追加されたアクセス許可を確認します。

11. **[既定のディレクトリに管理者の同意を与えます]**  >  **[保存]** を選択します。

12. **[Certificates and Secrets]\(証明書とシークレット\)** に移動し、 **[+ Add Client Secret]\(クライアント シークレットの追加\)** を選択します。 クライアント シークレットの説明を入力し、有効期限オプションを選択して、 **[追加]** を選択します。

13. 秘密鍵が生成され、[クライアント シークレット] セクションに表示されます。

    >[!NOTE]
    > 後でこのクライアント シークレットが必要になります。

14. **[概要]** に移動し、 **[クライアント ID]** と **[テナント ID]** を取得します。

15. Saviynt での設定を完了するには、テナント ID、クライアント ID、およびクライアント シークレットが必要です。

### <a name="enable-saviynt-to-delete-users"></a>Saviynt でユーザーを削除できるようにする

以下の手順では、Saviynt で Azure AD B2C 内のユーザー削除操作を実行できるようにする方法について説明します。

>[!NOTE]
>[管理者ロールにサービス プリンシパルへのアクセスを許可する前に、リスクを評価します。](../active-directory/develop/app-objects-and-service-principals.md)

1. 最新バージョンの MSOnline PowerShell モジュールを Windows ワークステーションまたはサーバーにインストールします。

2. AzureAD PowerShell モジュールに接続し、次のコマンドを実行します。

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>ソリューションをテストする

Saviynt アプリケーション テナントを参照し、ユーザーのライフサイクル管理とアクセス ガバナンスのユース ケースをテストします。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)

- [Web API アプリケーションを作成する](./add-web-api-application.md)