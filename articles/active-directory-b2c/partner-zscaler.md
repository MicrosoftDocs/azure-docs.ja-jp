---
title: チュートリアル - Azure Active Directory B2C と Zscaler を構成する
titleSuffix: Azure AD B2C
description: Azure AD B2C 認証を Zscaler と統合する方法について説明します。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: dbeb3cd4fccf80f434e6c7ac08c658632f64b135
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096855"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C で Zscaler Private Access を構成する

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) 認証を [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access) と統合する方法について説明します。 ZPA は、仮想プライベート ネットワーク (VPN) のコスト、手間、またはセキュリティ上のリスクが生じることなく、非公開のアプリケーションおよび資産へのセキュリティで保護された、ポリシーベースのアクセスを実現します。 Zscaler のセキュリティで保護されたハイブリッド アクセス オファリングを Azure AD B2C と組み合わせて使用すると、コンシューマー向けアプリケーションの攻撃対象領域をなくすことができます。

## <a name="prerequisites"></a>前提条件

開始する前に、以下が必要になります。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。  
- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。  
- [ZPA サブスクリプション](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)。

## <a name="scenario-description"></a>シナリオの説明

ZPA 統合には、次のコンポーネントが含まれています。

- **Azure AD B2C**:ユーザーの資格情報の検証を受け持つ ID プロバイダー (IdP)。 また、新しいユーザーのサインアップも受け持ちます。  
- **ZPA**:[ゼロトラスト アクセス](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)を適用することによって Web アプリケーションのセキュリティ保護を受け持つサービス。  
- **Web アプリケーション**: ユーザーがアクセスしようとしているサービスをホストします。

次の図は、ZPA を Azure AD B2C と統合する方法を示しています。

![Zscaler アーキテクチャの図。ZPA を Azure AD B2C と統合する方法を示しています。](media/partner-zscaler/zscaler-architecture-diagram.png)

このシーケンスについて次の表で説明します。

|手順 | 説明 |
| :-----:| :-----------|
| 1 | ユーザーが、ZPA User Portal または ZPA Browser Access アプリケーションにアクセスします。
| 2 | ZPA は、ユーザーが Web アプリケーションにアクセスすることを許可するかどうかを決定する前に、ユーザー コンテキスト情報を必要とします。 ユーザーを認証するために、ZPA は Azure AD B2C ログイン ページへの SAML リダイレクトを実行します。  
| 3 | ユーザーが、Azure AB B2C ログイン ページにたどり着きます。 新しいユーザーがサインアップすると、アカウントが作成され、既存のユーザーは既存の資格情報でログインします。 Azure AD B2C により、ユーザーの ID が検証されます。
| 4 | 認証が成功すると、Azure AD B2C によってユーザーは SAML アサーションと共に ZPA にリダイレクトされます。 ZPA で SAML アサーションが検証され、ユーザー コンテキストが設定されます。
| 5 | ZPA で、ユーザーのアクセス ポリシーが評価されます。 ユーザーが Web アプリケーションへのアクセスを許可されている場合、接続は通過できます。

## <a name="onboard-to-zpa"></a>ZPA にオンボードする

このチュートリアルでは、ZPA の動作セットアップが既に行われていることを前提としています。 ZPA の使用を開始する場合は、「[ZPA の詳細な構成ガイド](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)」を参照してください。

## <a name="integrate-zpa-with-azure-ad-b2c"></a>ZPA と Azure AD B2C の統合

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>手順 1:ZPA で IdP として Azure AD B2C を構成する

[ZPA で IdP](https://help.zscaler.com/zpa/configuring-idp-single-sign) として Azure AD B2C を構成するには、次の操作を行います。

1. [ZPA Admin Portal](https://admin.private.zscaler.com) にログインします。

1. **[Administration]\(管理\)**  >  **[IdP Configuration]\(IdP 構成\)** に移動します。

1. **[IdP 構成の追加]** を選択します。

   **[IdP 構成の追加]** ウィンドウが開きます。

   ![[IdP 構成の追加] ウィンドウの [IdP 情報] タブのスクリーンショット。](media/partner-zscaler/add-idp-configuration.png)

1. **[IdP 情報]** タブを選択し、次の操作を行います。

   a. **[名前]** ボックスに、「**Azure AD B2C**」と入力します。  
   b. **[シングル サインオン]** の **[ユーザー]** を選択します。  
   c. **[ドメイン]** ドロップダウン リストで、この IdP に関連付ける認証ドメインを選択します。

1. **[次へ]** を選択します。

1. **[SP メタデータ]** タブをクリックして、次の操作を行います。

   a. **[サービス プロバイダー URL]** の値を、後で使用するためにコピーするかメモしておきます。  
   b. **[サービス プロバイダー エンティティ ID]** の値を、後で使用するためにコピーするかメモしておきます。

   ![[IdP 構成の追加] ウィンドウの [SP メタデータ] タブのスクリーンショット。](media/partner-zscaler/sp-metadata.png)

1. **[一時停止]** を選択します。

Azure AD B2C を構成すると、残りの IdP の構成が再開されます。

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>手順 2:Azure AD B2C のカスタム ポリシーを構成する

>[!Note]
>この手順は、カスタム ポリシーをまだ設定していない場合にのみ必要です。 1 つ以上のカスタム ポリシーが既にある場合は、この手順を省略できます。

Azure AD B2C テナントでカスタム ポリシーを構成するには、「[Azure Active Directory B2C でのカスタム ポリシーの概要](./custom-policy-get-started.md)」を参照してください。

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>手順 3:ZPA を SAML アプリケーションとして Azure AD B2C に登録する

Azure AD B2C で SAML アプリケーションを構成するには、「[SAML アプリケーションを Azure AD B2C に登録する](./saml-service-provider.md)」を参照してください。 

「[ポリシーをアップロードする](./saml-service-provider.md#upload-your-policy)」手順で、Azure AD B2C によって使用される IdP SAML メタデータ URL をコピーまたはメモします。 この情報は後で必要になります。

「[Azure AD B2C でアプリケーションを構成する](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c)」手順の指示に従います。 手順 4.2 では、次のようにアプリケーション マニフェストのプロパティを更新します。

- **identifierUris** の場合:前の「手順 1.6.b」でコピーまたはメモしたサービス プロバイダー エンティティ ID を使用します。  
- **samlMetadataUrl** の場合:ZPA が SAML メタデータ URL をホストしないため、このプロパティはスキップします。  
- **replyUrlsWithType** の場合:前の「手順 1.6.a」でコピーまたはメモしたサービス プロバイダー URL を使用します。  
- **logoutUrl** の場合:ZPA ではログアウト URL がサポートされていないため、このプロパティはスキップします。

残りの手順は、このチュートリアルには関係ありません。

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>手順 4:Azure AD B2C から IdP SAML メタデータを抽出する

次に、次の形式で SAML メタデータ URL を取得する必要があります。

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

`<tenant-name>` は Azure AD B2C テナントの名前、`<policy-name>` は前の手順で作成したカスタム SAML ポリシーの名前であることに注意してください。

たとえば、URL は `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata` になります。

Web ブラウザーを開き、SAML メタデータ URL に移動します。 ページ上の任意の場所を右クリックし、 **[名前を付けて保存]** を選択して、次の手順で使用するためにコンピューターにファイルを保存します。

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>手順 5:ZPA で IdP 構成を完了する

[ZPA Admin Portal での IdP の構成](https://help.zscaler.com/zpa/configuring-idp-single-sign)を完了します。これは前に「手順 1: ZPA で IdP として Azure AD B2C を構成する」で一部を構成済みです。

1. [ZPA Admin Portal](https://admin.private.zscaler.com) で、 **[管理]**  >  **[IdP 構成]** に移動します。

1. 「手順 1」で構成した IdP を選択し、 **[再開]** を選択します。

1. **[IdP 構成の追加]** ウィンドウで、 **[IdP の作成]** タブを選択し、次の操作を行います。

   a. **[IdP メタデータ ファイル]** の下で、前に「手順 4: Azure AD B2C から IdP SAML メタデータを抽出する」で保存したメタデータ ファイルをアップロードします。  
   b. IdP 構成の **[状態]** が **[有効]** になっていることを確認します。  
   c. **[保存]** を選択します。

   ![[IdP 構成の追加] ウィンドウの [IdP の作成] タブのスクリーンショット。](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>ソリューションをテストする

ZPA User Portal または Browser Access アプリケーションにアクセスし、サインアップまたはサインインのプロセスをテストします。 このテストで SAML 認証が成功するはずです。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md)
- [SAML アプリケーションを Azure AD B2C に登録する](./saml-service-provider.md)
- [ZPA の詳細な構成ガイド](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [シングル サインオンのための IdP の構成](https://help.zscaler.com/zpa/configuring-idp-single-sign)