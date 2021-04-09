---
title: 条件付きアクセス ポリシーのクラウド アプリまたは操作 - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーのクラウド アプリまたは操作とは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff9fe969c90a2bb4aa9f954b984f511fb490ba3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579146"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>条件付きアクセス:クラウド アプリまたはアクション

クラウド アプリまたは操作は、条件付きアクセス ポリシーの重要なシグナルです。 管理者は、条件付きアクセス ポリシーを使用して、特定のアプリケーションまたはアクションにコントロールを割り当てることができます。

- 管理者は、組み込みの Microsoft アプリケーションおよび [Azure AD 統合アプリケーション](../manage-apps/what-is-application-management.md) (ギャラリー、ギャラリー以外、[アプリケーション プロキシ](../manage-apps/what-is-application-proxy.md)経由で公開されたアプリケーションなど) を含むアプリケーションの一覧から選択できます。
- 管理者は、クラウド アプリケーションではなく、ユーザー アクションに基づいてポリシーを定義することを選択できます。 サポートされている唯一のアクションは、[セキュリティ情報の登録 (プレビュー)] です。これにより、条件付きアクセスは、[統合されたセキュリティ情報の登録エクスペリエンス](../authentication/howto-registration-mfa-sspr-combined.md)に関連したコントロールを適用できます。

![条件付きアクセス ポリシーを定義し、クラウド アプリを指定する](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft クラウド アプリケーション

既存の Microsoft クラウド アプリケーションの多くは、選択できるアプリケーションの一覧に含まれています。 

管理者は、次の Microsoft のクラウド アプリに条件付きアクセス ポリシーを割り当てることができます。 Office 365 や Microsoft Azure Management などの一部のアプリには、複数の関連する子アプリやサービスが含まれています。 次の一覧は完全なものではなく、変更される可能性があります。

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database と Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure Management](#microsoft-azure-management)
- Microsoft Azure Subscription Management
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control Portal
- Microsoft Commerce Tools Authentication Service
- Microsoft Flow
- Microsoft フォーム
- Microsoft Intune
- [Microsoft Intune Enrollment](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Search in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI サービス
- Project Online
- Skype for Business Online
- 仮想プライベート ネットワーク (VPN)
- Windows Defender ATP

### <a name="office-365"></a>Office 365

Microsoft 365 では、Exchange、SharePoint、Microsoft Teams などのクラウドベースの生産性およびコラボレーション サービスが提供されます。 Microsoft 365 クラウド サービスは、スムーズに共同作業を行うために緊密に統合されています。 Microsoft Teams などの一部のアプリは SharePoint や Exchange などの他のアプリに依存しているため、この統合により、ポリシーの作成時に混乱が生じる可能性があります。

Office 365 アプリを使用すると、これらのサービスを一度にすべてターゲットにすることができます。 Microsoft では、[サービスの依存関係](service-dependencies.md)の問題を回避するために、個々のクラウド アプリをターゲットにするのではなく、新しい Office 365 アプリを使用することをお勧めしています。 このアプリケーション グループをターゲットにすることで、ポリシーと依存関係の不整合が原因で発生する可能性のある問題を回避できます。

管理者は、Office 365 アプリを組み込み、選択した特定のアプリをポリシーで除外することにより、必要に応じて特定のアプリをポリシーから除外することを選択できます。

Office 365 クライアント アプリに含まれる主要なアプリケーション:

   - Microsoft Flow
   - Microsoft フォーム
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 Search サービス
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure の管理

Microsoft Azure Management アプリケーションには、基になる複数のサービスが含まれています。 

   - Azure portal
   - Azure Resource Manager プロバイダー
   - クラシック デプロイ モデル API
   - Azure PowerShell
   - Azure CLI
   - Visual Studio サブスクリプション管理者ポータル
   - Azure DevOps
   - Azure Data Factory ポータル

> [!NOTE]
> Microsoft Azure Management アプリケーションは、Azure Resource Manager API を呼び出す Azure PowerShell に適用されます。 Microsoft Graph を呼び出す Azure AD PowerShell には適用されません。

## <a name="other-applications"></a>他のアプリケーション

管理者は、Microsoft アプリに加えて、Azure AD に登録された任意のアプリケーションを条件付きアクセス ポリシーに追加することができます。 これらのアプリケーションには次が含まれます。 

- [Azure AD アプリケーション プロキシ](../manage-apps/what-is-application-proxy.md)経由で公開されるアプリケーション
- [ギャラリーから追加されたアプリケーション](../manage-apps/add-application-portal.md)
- [ギャラリーにないカスタム アプリケーション](../manage-apps/view-applications-portal.md)
- [アプリ配信コントローラーとネットワーク経由で公開されるレガシ アプリケーション](../manage-apps/secure-hybrid-access.md)
- [パスワードに基づくシングル サインオン](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)を使用するアプリケーション

> [!NOTE]
> 条件付きアクセス ポリシーでは、サービスにアクセスするための要件が設定されるため、クライアント (パブリック/ネイティブ) アプリケーションにそれを適用することはできません。 つまり、ポリシーはクライアント (パブリック/ネイティブ) アプリケーションに直接設定されるのではなく、クライアントがサービスを呼び出すときに適用されます。 たとえば、SharePoint サービスで設定したポリシーは、SharePoint を呼び出すクライアントに適用されます。 Exchange で設定されたポリシーは、Outlook クライアントを使用して電子メールにアクセスしようとしたときに適用されます。 このため、クラウド アプリの選択で、クライアント (パブリック/ネイティブ) アプリケーションを選択できず、テナントに登録されているクライアント (パブリック/ネイティブ) アプリケーションのアプリケーション設定で、[条件付きアクセス] オプションを選択できません。 

## <a name="user-actions"></a>ユーザー操作

ユーザー操作とは、ユーザーが実行できるタスクです。 現在、条件付きアクセスでは 2 つのユーザー操作がサポートされています。 

- **セキュリティに関する情報の登録**: このユーザー操作により、統合された登録が有効になったユーザーが自分のセキュリティに関する情報を登録しようとすると、条件付きアクセス ポリシーが適用されます。 詳細情報については、「[統合されたセキュリティ情報の登録](../authentication/concept-registration-mfa-sspr-combined.md)」を参照してください。

- **デバイスの登録または参加 (プレビュー)** : このユーザー操作により、ユーザーがデバイスを Azure AD に[登録](../devices/concept-azure-ad-register.md)するか[参加](../devices/concept-azure-ad-join.md)させるときに、管理者は条件付きアクセス ポリシーを適用することができます。 このユーザー操作には、次の 2 つの重要な考慮事項があります。 
   - `Require multi-factor authentication` は、このユーザー操作で使用できる唯一のアクセス制御であり、それ以外はすべて無効になっています。 この制限により、Azure AD デバイス登録に依存している、あるいは Azure AD デバイス登録に適用されないアクセス制御との競合を防ぐことができます。 
   - このユーザー操作によって条件付きアクセスポリシーを有効にした場合、 **[Azure Active Directory]**  >  **[デバイス]**  >  **[デバイス設定]**  - `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` を **[いいえ]** に設定する必要があります。 そうしないと、このユーザー操作による条件付きアクセス ポリシーが適切に適用されません。 このデバイス設定に関する詳細については、「[デバイス設定の構成](../devices/device-management-azure-portal.md#configure-device-settings)」を参照してください。 このユーザー操作は柔軟性を備えており、デバイス設定でテナント全体のポリシーを使用するのではなく、特定のユーザーとグループまたは条件に対してデバイスを登録するか参加させる際に、多要素認証を要求することができます。 
   
## <a name="next-steps"></a>次のステップ

- [条件付きアクセス:条件](concept-conditional-access-conditions.md)

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)
- [クライアント アプリケーションの依存関係](service-dependencies.md)
