---
title: Azure Active Directory 参加の実装を計画する方法
description: 環境内に Azure AD 参加済みデバイスを実装するために必要な手順について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3acaf4929158b24ff50655aa18c05b41aeec4b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435452"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>方法:Azure AD Join の実装を計画する

Azure AD 参加により、ユーザーの生産性とセキュリティを維持しながら、デバイスをオンプレミスの Active Directory に参加させる必要なしに Azure AD に直接参加させることができます。 Azure AD 参加は、大規模デプロイとスコープ付きのデプロイの両方についてエンタープライズ対応になっています。   

この記事では、Azure AD 参加の実装を計画するために必要な情報を提供します。
 
## <a name="prerequisites"></a>前提条件

この記事では、[Azure Active Directory でのデバイス管理の概要](./overview.md)を理解していることを前提とします

## <a name="plan-your-implementation"></a>実装の計画

Azure AD 参加の実装を計画するには、以下を理解する必要があります。

> [!div class="checklist"]
> - シナリオをレビューする
> - ID インフラストラクチャをレビューする
> - デバイス管理を評価する
> - アプリケーションとリソースに関する考慮事項を把握する
> - プロビジョニングのオプションを把握する
> - 企業の状態ローミングを構成する
> - 条件付きアクセスを構成する

## <a name="review-your-scenarios"></a>シナリオをレビューする 

特定のシナリオでは Hybrid Azure AD 参加が望ましい場合がありますが、Azure AD 参加により、Windows を使用するクラウド中心のモデルに移行できます。 デバイス管理の最新化とデバイス関連の IT コストの削減を計画している場合は、Azure AD 参加により、これらの目標を実現するための優れた基盤が提供されます。  
 
目標が次の条件と一致する場合は、Azure AD 参加を検討してください。

- ユーザー用の生産性スイートとして Microsoft 365 を採用している。
- クラウド デバイス管理ソリューションを使用してデバイスを管理したい。
- 地理的に分散したユーザーのデバイス プロビジョニングを簡略化したい。
- アプリケーション インフラストラクチャの最新化を計画している。

## <a name="review-your-identity-infrastructure"></a>ID インフラストラクチャをレビューする  

Azure AD 参加は、マネージド環境とフェデレーション環境の両方で動作します。  

### <a name="managed-environment"></a>マネージド環境

マネージド環境は、[パスワード ハッシュ同期](../hybrid/how-to-connect-password-hash-synchronization.md)または[パススルー認証](../hybrid/how-to-connect-pta-quick-start.md)のいずれかとシームレス シングル サインオンを使用してデプロイできます。

これらのシナリオでは、フェデレーション サーバーを認証用に構成する必要はありません。

### <a name="federated-environment"></a>フェデレーション環境

フェデレーション環境には、WS-Trust と Ws-Fed の両方のプロトコルをサポートしている ID プロバイダーが必要です。

- **WS-Fed:** このプロトコルは、デバイスを Azure AD に参加させるために必要です。
- **WS-Trust:** このプロトコルは、Azure AD 参加済みデバイスにサインインするために必要です。

AD FS を使用している場合は、次の WS-Trust エンドポイントを有効にする必要があります。`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

ID プロバイダーによってこれらのプロトコルがサポートされていない場合、Azure AD 参加はネイティブには機能しません。 

>[!NOTE]
> 現時点で、Azure AD 参加は[プライマリ認証方法として外部認証プロバイダーで構成されている AD FS 2019](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary) では機能しません。 Azure AD 参加ではプライマリ メソッドの既定値として、パスワード認証が設定されます。これにより、このシナリオで認証エラーが発生します


### <a name="smartcards-and-certificate-based-authentication"></a>スマートカードと証明書ベースの認証

スマートカードまたは証明書ベースの認証を使用して、Azure AD にデバイスを参加させることはできません。 ただし、AD FS を構成してある場合は、スマートカードを使用して Azure AD 参加済みデバイスにサインインすることができます。

**推奨事項:** Windows 10 デバイスに対する強力なパスワードなしの認証用に Windows Hello for Business を実装してください。

### <a name="user-configuration"></a>ユーザー構成

ユーザーを以下の場所に作成する場合:

- **オンプレミスの Active Directory**: [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) を使用して Azure AD と同期する必要があります。 
- **Azure AD**: 追加のセットアップは不要です。

Azure AD の UPN とは異なるオンプレミスの UPN は、Azure AD 参加済みデバイスでサポートされていません。 お客様のユーザーがオンプレミスの UPN を使用している場合は、Azure AD 内でプライマリ UPN を使用するように切り替えることを計画してください。

UPN の変更は、Windows 10 2004 Update 以降でのみサポートされます。 この更新プログラムがインストールされたデバイスのユーザーには、UPN の変更後も問題は発生しません。 Windows 10 2004 Update より前のデバイスの場合は、ユーザーのデバイスで SSO および条件付きアクセスに関する問題が発生する可能性があります。 この問題を解決するために、ユーザーは新しい UPN を使用して [他のユーザー] タイルから Windows にサインインする必要があります。 

## <a name="assess-your-device-management"></a>デバイス管理を評価する

### <a name="supported-devices"></a>サポートされているデバイス

Azure AD 参加:

- Windows 10 デバイスに対してのみ適用されます。 
- 以前のバージョンの Windows や他のオペレーティング システムには適用されません。 Windows 7/8.1 デバイスを使用している場合は、Windows 10 にアップグレードして Azure AD 参加をデプロイする必要があります。
- FIPS 準拠の TPM 2.0 でサポートされていますが、TPM 1.2 ではサポートされていません。 FIPS 準拠の TPM 1.2 がデバイスにある場合は、Azure AD 参加を進める前に、それらを無効にする必要があります。 TPM の FIPS モードを無効にするためのツールは、TPM の製造元に依存するため、Microsoft では用意していません。 サポートが必要な場合は、お使いのハードウェアの OEM にお問い合わせください。
 
**推奨事項:** 更新された機能を利用するために、常に最新の Windows 10 リリースを使用してください。

### <a name="management-platform"></a>管理プラットフォーム

Azure AD 参加済みデバイスのデバイス管理は、Intune、MDM CSP などの MDM プラットフォームに基づいています。 Windows 10 には、互換性のあるすべての MDM ソリューションで動作する組み込みの MDM エージェントがあります。

> [!NOTE]
> グループ ポリシーは、オンプレミスの Active Directory に接続されていないため、Azure AD 参加済みデバイスではサポートされません。 Azure AD 参加済みデバイスの管理は MDM からのみ可能

Azure AD 参加済みデバイスの管理には 2 つのアプローチがあります。

- **MDM のみ** - デバイスは、Intune などの MDM プロバイダーによってのみ管理されます。 すべてのポリシーは、MDM の登録プロセスの一環として配信されます。 Azure AD Premium または EMS のお客様の場合、MDM の登録は、Azure AD 参加の一部である自動化された手順です。
- **共同管理** - デバイスは、MDM プロバイダーと SCCM によって管理されます。 このアプローチでは、SCCM エージェントは、MDM によって管理されるデバイスにインストールされて、特定の側面を管理します。

グループ ポリシーを使用している場合は、Microsoft Endpoint Manager の[グループ ポリシー分析](/mem/intune/configuration/group-policy-analytics)を使用して、GPO と MDM ポリシーのパリティを評価します。 

サポート対象のポリシーとサポート対象外のポリシーを確認して、グループ ポリシーの代わりに MDM ソリューションを使用できるかどうかを判断します。 サポート対象外のポリシーの場合は、以下を検討してください。

- Azure AD 参加済デバイスまたはユーザーにサポート対象外のポリシーが必要か?
- サポート対象外のポリシーはクラウド主導のデプロイに適用できるか?

MDM ソリューションを Azure AD アプリ ギャラリーから入手できない場合は、「[Azure Active Directory integration with MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm)」(Azure Active Directory と MDM の統合) に概説されているプロセスに従って追加できます。 

共同管理では、SCCM を使用してデバイスの特定の側面を管理しながら、MDM プラットフォームを通じてポリシーを配信できます。 Microsoft Intune では、SCCM との共同管理を行うことができます。 Windows 10 デバイスの共同管理については、「[共同管理とは](/configmgr/core/clients/manage/co-management-overview)」を参照してください。 Intune 以外の MDM 製品を使用する場合は、適用可能な共同管理のシナリオについて MDM のプロバイダーにお問い合わせください。

**推奨事項:** Azure AD 参加済みデバイスについては MDM のみの管理をご検討ください。

## <a name="understand-considerations-for-applications-and-resources"></a>アプリケーションとリソースに関する考慮事項を把握する

ユーザー エクスペリエンスとアクセスの制御を向上させるために、アプリケーションをオンプレミスからクラウドに移行することをお勧めします。 ただし、Azure AD 参加済みデバイスでは、オンプレミスとクラウドの両方のアプリケーションにシームレスにアクセスできます。 詳しくは、「[How SSO to on-premises resources works on Azure AD joined devices](azuread-join-sso.md)」(Azure AD 参加済みデバイス上でのオンプレミスのリソースへの SSO の動作) をご覧ください。

以下のセクションでは、さまざまな種類のアプリケーションとリソースに関する考慮事項を示します。

### <a name="cloud-based-applications"></a>クラウドベース アプリケーション

Azure AD アプリ ギャラリーにアプリケーションが追加されている場合、ユーザーは Azure AD 参加済みデバイスから SSO を取得できます。 追加の構成は不要です。 ユーザーは Microsoft Edge と Chrome の両方のブラウザーで SSO を取得できます。 Chrome の場合は、[Windows 10 アカウントの拡張機能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)をデプロイする必要があります。 

すべての Win32 アプリケーションが次のようになります。

- トークン要求を Web Account Manager (WAM) に依存する場合は、SSO も Azure AD 参加済みデバイス上で取得します。 
- WAM に依存しない場合は、ユーザーに認証を求めるプロンプトが表示されることがあります。 

### <a name="on-premises-web-applications"></a>オンプレミスの Web アプリケーション

アプリがカスタム ビルドであるかオンプレミスでホストされている場合は、以下の目的でそれらのアプリをブラウザーの信頼済みサイトに追加する必要があります。

- Windows 統合認証を動作させる 
- プロンプトなしの SSO エクスペリエンスをユーザーに提供する。 

AD FS を使用する場合は、「[AD FS によるシングル サインオンを確認および管理する](/previous-versions/azure/azure-services/jj151809(v%3dazure.100))」をご覧ください。 

**推奨事項:** エクスペリエンスを向上させるために、クラウド (たとえば、Azure) にホストし、Azure AD と統合することをご検討ください。

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>従来のプロトコルに依存するオンプレミスのアプリケーション

デバイスがドメイン コントローラーへのアクセス権を持つ場合、ユーザーは Azure AD 参加済みデバイスから SSO を取得します。 

**推奨事項:** [Azure AD アプリのプロキシ](../manage-apps/application-proxy.md)をデプロイして、これらのアプリケーションへのアクセスをセキュリティで保護できるようにします。

### <a name="on-premises-network-shares"></a>オンプレミスのネットワーク共有

デバイスにオンプレミスのドメイン コントローラーへのアクセス権がある場合、ユーザーには Azure AD 参加済みデバイスからの SSO があります。

### <a name="printers"></a>プリンター

プリンターの場合は、Azure AD 参加済みデバイス上でプリンターを検出するために[ハイブリッド クラウド印刷](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)をデプロイする必要があります。 

プリンターは、クラウドのみの環境内では自動的に検出できませんが、ユーザーはプリンターの UNC パスを使用してそれらを直接追加することもできます。 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>マシン認証に依存するオンプレミスのアプリケーション

Azure AD 参加済みデバイスでは、マシンの認証に依存するオンプレミスのアプリケーションはサポートされていません。 

**推奨事項:** これらのアプリケーションを廃止し、最新の代替アプリケーションに移行することをご検討ください。

### <a name="remote-desktop-services"></a>リモート デスクトップ サービス

Azure AD 参加済みデバイスにリモート デスクトップ接続を行うには、ホスト マシンが Azure AD 参加済みまたは Hybrid Azure AD 参加済みである必要があります。 参加していないデバイスまたは Windows 以外のデバイスからのリモート デスクトップはサポートされていません。 詳しくは、[Azure AD に参加しているリモート PC への接続](/windows/client-management/connect-to-remote-aadj-pc)に関する記事をご覧ください

Windows 10 2004 更新プログラム以降、ユーザーは Azure AD 登録済み Windows 10 デバイスから Azure AD 参加済みデバイスへのリモート デスクトップも使用できるようになります。 

## <a name="understand-your-provisioning-options"></a>プロビジョニングのオプションを把握する
**注**:Azure AD 参加済みデバイスは、システム準備ツール (Sysprep) または同様のイメージング ツールを使用してデプロイすることはできません

次のアプローチを使用して Azure AD 参加をプロビジョニングできます。

- **OOBE/設定内でのセルフサービス** - セルフサービス モードでは、ユーザーは Windows Out of Box Experience (OOBE) の実行中に、または Windows 設定から、Azure AD 参加のプロセスを実行します。 詳しくは、「[職場のデバイスを組織のネットワークに参加させる](../user-help/user-help-join-device-on-network.md)」をご覧ください。 
- **Windows Autopilot** - Windows Autopilot により、Azure AD 参加を実行する OOBE のエクスペリエンスを円滑にするためにデバイスの事前構成を行うことができます。 詳しくは、「[Windows Autopilot の概要](/windows/deployment/windows-autopilot/windows-10-autopilot)」をご覧ください。 
- **一括登録** - 一括登録により、一括プロビジョニング ツールを使用してデバイスを構成することで、管理者主導の Azure AD 参加が可能になります。 詳しくは、「[Windows デバイスの一括登録](/intune/windows-bulk-enroll)」をご覧ください。
 
この 3 つのアプローチの比較を次に示します 
 
| 要素 | セルフサービス セットアップ | Windows Autopilot | 一括登録 |
| --- | --- | --- | --- |
| セットアップにユーザーの操作が必要 | はい | はい | いいえ |
| IT 部門の作業が必要 | いいえ | はい | はい |
| 適用可能なフロー | OOBE と設定 | OOBE のみ | OOBE のみ |
| プライマリ ユーザーに対するローカル管理者権限 | 既定では、はい | 構成可能 | いいえ |
| デバイス OEM のサポートが必要 | いいえ | はい | いいえ |
| サポートされているバージョン | 1511+ | 1709+ | 1703+ |
 
上記の表を確認し、どのアプローチを採用するかについて次の考慮事項を検討して、1 つまたは複数のデプロイ アプローチを選択します。  

- ユーザーは技術的な知識に精通していてセットアップを自分で実行できるか? 
   - これらのユーザーにはセルフサービスが最適です。 ユーザー エクスペリエンスを強化するために Windows Autopilot をご検討ください。  
- ユーザーはリモートと企業プレミス内のどちらにいるか? 
   - 手間をかけずにセットアップするために、リモート ユーザーにはセルフサービスまたは Autopilot が最適です。 
- ユーザー主導の構成と管理者が管理する構成のどちらを優先するか? 
   - 管理者主導のデプロイで、ユーザーに渡す前にデバイスをセットアップするには、一括登録が適しています。     
- 1 社か 2 社の OEM からデバイスを購入するか、それとも広範な OEM のデバイスを使用するか?  
   - Autopilot もサポートしている限られた OEM から購入する場合は、Autopilot とのより緊密な統合を活用できます。 

## <a name="configure-your-device-settings"></a>デバイス設定を構成する

Azure portal では、組織内の Azure AD 参加済みデバイスのデプロイを制御することができます。 関連設定を構成するには、 **[Azure Active Directory]** ページで `Devices > Device settings` を選択します。

### <a name="users-may-join-devices-to-azure-ad"></a>ユーザーはデバイスを Azure AD に参加させることができます

デプロイのスコープと、どのユーザーが Azure AD 参加済みデバイスをセットアップできるようにするかに基づいて、このオプションを **[すべて]** または **[選択済み]** に設定します。 

![ユーザーはデバイスを Azure AD に参加させることができます](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Azure AD 参加済みデバイス上の追加のローカル管理者

**[選択済み]** を選択し、すべての Azure AD 参加済みデバイス上のローカル管理者のグループに追加するユーザーを選択します。 

![Azure AD 参加済みデバイス上の追加のローカル管理者](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>デバイスを参加させるには多要素認証が必要

デバイスを Azure AD に参加させるときにユーザーに MFA の実行を要求する場合は、 **[はい]** を選択します。 ユーザーが MFA を使用して Azure AD にデバイスを参加させる場合は、デバイス自体が第 2 要素になります。

![デバイスを参加させるには多要素認証が必要](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>モビリティ設定の構成

モビリティ設定を構成するには、最初に MDM プロバイダーを追加することが必要な場合があります。

**MDM プロバイダーを追加するには**:

1. **[Azure Active Directory]** ページの **[管理]** セクションで、[`Mobility (MDM and MAM)`] をクリックします。 
1. **[アプリケーションの追加]** をクリックします。
1. 一覧から、使用する MDM プロバイダーを選択します。

   :::image type="content" source="./media/azureadjoin-plan/04.png" alt-text="Azure Active Directory の [アプリケーションを追加する] ページのスクリーンショット。いくつかの MDM プロバイダーが一覧表示されます。" border="false":::

MDM プロバイダーを選択して関連設定を構成します。 

### <a name="mdm-user-scope"></a>MDM ユーザー スコープ

デプロイのスコープに基づいて **[一部]** または **[すべて]** を選択します。 

![MDM ユーザー スコープ](./media/azureadjoin-plan/05.png)

スコープに基づいて、次のいずれかのようになります。 

- **ユーザーが MDM のスコープ内にいる**:Azure AD Premium のサブスクリプションがある場合は、Azure AD 参加と共に MDM の登録が自動化されます。 スコープ内のすべてのユーザーには、MDM に対する適切なライセンスが必要です。 このシナリオで MDM の登録に失敗した場合は、Azure AD 参加もロールバックされます。
- **ユーザーが MDM のスコープ内にいない**:ユーザーが MDM のスコープ内にいない場合、Azure AD 参加は MDM 登録なしで完了します。 その結果、アンマネージド デバイスが生じます。

### <a name="mdm-urls"></a>MDM URL

MDM の構成に関連する URL は 3 つあります。

- MDM 使用条件 URL
- MDM 探索 URL 
- MDM 準拠 URL

:::image type="content" source="./media/azureadjoin-plan/06.png" alt-text="Azure Active Directory MDM 構成セクションの一部に、MDM の使用条件、検出、コンプライアンスのための URL フィールドがあるスクリーンショット。" border="false":::

各 URL には、定義済みの既定値があります。 これらのフィールドが空の場合、詳しくは MDM プロバイダーにお問い合わせください。

### <a name="mam-settings"></a>MAM 設定

MAM は、Azure AD 参加に適用されません。 

## <a name="configure-enterprise-state-roaming"></a>企業の状態ローミングを構成する

Azure AD に対して状態ローミングを有効にして、ユーザーがデバイス間で設定を同期できるようにするには、「[Azure Active Directory の Enterprise State Roaming を有効にする](enterprise-state-roaming-enable.md)」をご覧ください。 

**推奨事項**:この設定は、ハイブリッド Azure AD 参加済みデバイスに対しても有効にしてください。

## <a name="configure-conditional-access"></a>条件付きアクセスを構成する

Azure AD 参加済みデバイスに対して MDM プロバイダーが構成されている場合、プロバイダーは、デバイスが管理下に入るとすぐにデバイスに準拠のフラグを設定します。 

![準拠デバイス](./media/azureadjoin-plan/46.png)

この実装を使用して、[条件付きアクセスを使用してクラウド アプリへのアクセスにマネージド デバイスを要求する](../conditional-access/require-managed-devices.md)ことができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最初の実行中に新しい Windows 10 デバイスを Azure AD に参加させる](azuread-joined-devices-frx.md)
> [職場のデバイスを組織のネットワークに参加させる](../user-help/user-help-join-device-on-network.md)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
