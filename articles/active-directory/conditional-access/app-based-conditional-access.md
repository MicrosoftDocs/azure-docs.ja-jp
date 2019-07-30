---
title: Azure Active Directory の条件付きアクセスを使用してクラウド アプリへのアクセスに承認されたクライアント アプリを要求する方法 | Microsoft Docs
description: Azure Active Directory の条件付きアクセスを使用して、クラウド アプリへのアクセスに承認されたクライアント アプリを要求する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4e81f5533622c8afbfb0d7c683c76b530aefec
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509493"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>方法:条件付きアクセスを使用してクラウド アプリへのアクセスに承認されたクライアント アプリを要求する 

従業員は個人的な作業と業務上の作業のどちらにもモバイル デバイスを使用します。 そこで、従業員の生産性を確保しながら、データの損失を防止する必要があります。 Azure Active Directory (Azure AD) の条件付きアクセスを使用すると、クラウド アプリへのアクセスを、承認されたクライアント アプリに制限して、会社のデータを保護することができます。  

このトピックでは、承認されたクライアント アプリを必要とする条件付きアクセス ポリシーを構成する方法について説明します。

## <a name="overview"></a>概要

[Azure AD の条件付きアクセス](overview.md)を使用すると、承認されたユーザーがリソースにアクセスする方法を微調整できます。 たとえば、クラウド アプリへのアクセスを信頼済みデバイスのみに制限できます。

会社のデータの保護には、[Intune のアプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy)を利用できます。 Intune のアプリ保護ポリシーでは、モバイル デバイス管理 (MDM) ソリューションデバイスは必要ありません。このため、デバイスをデバイス管理ソリューションに登録しているかどうかに関係なく、会社のデータを保護することができます。

Azure Active Directory の条件付きアクセスを使用すると、クラウド アプリへのアクセスを、Intune のアプリ保護ポリシーをサポートしているクライアント アプリに制限できます。 たとえば、Exchange Online に対するアクセスを Outlook アプリのみに制限することができます。

条件付きアクセスに関する用語では、このようなクライアント アプリを**承認されたクライアント アプリ**と呼んでいます。  

![条件付きアクセス](./media/app-based-conditional-access/05.png)

承認されたクライアント アプリの一覧は、[承認されたクライアント アプリの要件](technical-reference.md#approved-client-app-requirement)に関するセクションを参照してください。

アプリベースの条件付きアクセス ポリシーを[デバイス ベースの条件付きアクセス ポリシー](require-managed-devices.md)などの他のポリシーと組み合わせることで、個人のデバイスと会社のデバイスの両方で柔軟にデータを保護することができます。

## <a name="before-you-begin"></a>開始する前に

このトピックは、次の事項を熟知していることを前提としています。

- [承認されたクライアント アプリの要件](technical-reference.md#approved-client-app-requirement)に関するテクニカル リファレンス。
- [Azure Active Directory の条件付きアクセス](overview.md)の基本的な概念。
- [条件付きアクセス ポリシーの構成](app-based-mfa.md)方法。
- [条件付きアクセス ポリシーの移行](best-practices.md#policy-migration)。

## <a name="prerequisites"></a>前提条件

アプリベースの条件付きアクセス ポリシーを作成するには、Enterprise Mobility + Security または Azure Active Directory Premium のサブスクリプションが必要です。また、ユーザーが EMS または Azure AD のライセンスを取得している必要があります。 

## <a name="exchange-online-policy"></a>Exchange Online ポリシー 

このシナリオは、Exchange Online にアクセスするためのアプリベースの条件付きアクセス ポリシーで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。

- iOS または Android でネイティブ メール アプリケーションを使用して Exchange に接続する電子メールを作成する
- Outlook アプリを使用したアクセスのみを利用できることを示す電子メールを受信する
- リンクを使用してアプリケーションをダウンロードする
- Outlook アプリケーションを開き、Azure AD 資格情報を使用してサインインする
- 続行するには Authenticator (iOS) またはポータル サイト (Android) をインストールすることを求められる
- アプリケーションをインストールした後、Outlook アプリに戻って続行できる
- デバイスを登録することを求められる
- 電子メールにアクセスできる

すべての Intune アプリ保護ポリシーは、会社データへのアクセス時にアクティブになり、ユーザーにアプリケーションの再起動や追加 PIN の使用などを求めることができます (アプリケーションとプラットフォームがそのように構成されている場合)。

### <a name="configuration"></a>構成 

**手順 1 - Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-based-conditional-access/01.png)

1. 条件付きアクセス ポリシーの**名前**。
1. **ユーザーとグループ**:各条件付きアクセス ポリシーでは、少なくとも 1 人のユーザーまたは 1 つのグループが選択されている必要があります。
1. **クラウド アプリ**:クラウド アプリとして、 **[Office 365 Exchange Online]** を選択する必要があります。

   ![条件付きアクセス](./media/app-based-conditional-access/07.png)

1. **条件**:**条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。
   1. **デバイス プラットフォーム**として、 **[Android]** と **[iOS]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/03.png)

   1. **クライアント アプリ (プレビュー)** として、 **[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[先進認証クライアント]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/91.png)

1. **アクセス コントロール**として、 **[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。

   ![条件付きアクセス](./media/app-based-conditional-access/05.png)

**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-based-conditional-access/06.png)

1. 条件付きアクセス ポリシーの**名前**。
1. **ユーザーとグループ**:各条件付きアクセス ポリシーでは、少なくとも 1 人のユーザーまたは 1 つのグループが選択されている必要があります。
1. **クラウド アプリ**:クラウド アプリとして、 **[Office 365 Exchange Online]** を選択する必要があります。

   ![条件付きアクセス](./media/app-based-conditional-access/07.png)

1. **条件**:**条件**として、**クライアント アプリ (プレビュー)** を構成する必要があります。 
   1. **クライアント アプリ (プレビュー)** として、 **[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[Exchange ActiveSync クライアント]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/92.png)

   1. **アクセス コントロール**として、 **[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。

      ![条件付きアクセス](./media/app-based-conditional-access/05.png)

**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**

![条件付きアクセス](./media/app-based-conditional-access/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online と SharePoint Online のポリシー

このシナリオは、承認されたアプリで Exchange Online と SharePoint Online にアクセスするためのモバイル アプリ管理ポリシーによる条件付きアクセスで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。

- SharePoint アプリを使用して、会社のサイトに接続し、表示もしようとしている
- Outlook アプリの資格情報と同じ資格情報でサインインしようとしている
- リソースの再登録なしでリソースにアクセスできる

### <a name="configuration"></a>構成

**手順 1 - Exchange Online と SharePoint Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-based-conditional-access/71.png)

1. 条件付きアクセス ポリシーの**名前**。
1. **ユーザーとグループ**:各条件付きアクセス ポリシーでは、少なくとも 1 人のユーザーまたは 1 つのグループが選択されている必要があります。
1. **クラウド アプリ**:クラウド アプリとして、 **[Office 365 Exchange Online]** と **[Office 365 SharePoint Online]** を選択する必要があります。 

   ![条件付きアクセス](./media/app-based-conditional-access/02.png)

1. **条件**:**条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。
   1. **デバイス プラットフォーム**として、 **[Android]** と **[iOS]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/03.png)

   1. **クライアント アプリ (プレビュー)** として、 **[モバイル アプリとデスクトップ クライアント]** と **[先進認証クライアント]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/91.png)

1. **アクセス コントロール**として、 **[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。

   ![条件付きアクセス](./media/app-based-conditional-access/05.png)

**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-based-conditional-access/06.png)

1. 条件付きアクセス ポリシーの**名前**。
1. **ユーザーとグループ**:各条件付きアクセス ポリシーでは、少なくとも 1 人のユーザーまたは 1 つのグループが選択されている必要があります。
1. **クラウド アプリ**:クラウド アプリとして、 **[Office 365 Exchange Online]** を選択する必要があります。 オンライン 

   ![条件付きアクセス](./media/app-based-conditional-access/07.png)

1. **条件**:**条件**として、**クライアント アプリ**を構成する必要があります。
   1. **クライアント アプリ (プレビュー)** として、 **[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[Exchange ActiveSync クライアント]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/92.png)

   1. **アクセス コントロール**として、 **[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。

      ![条件付きアクセス](./media/app-based-conditional-access/05.png)

**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**

![条件付きアクセス](./media/app-based-conditional-access/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online と SharePoint Online 用のアプリベースまたは準拠しているデバイス ベースのポリシー

このシナリオは、Exchange Online にアクセスするためのアプリベースまたは準拠しているデバイス ベースの条件付きアクセス ポリシーで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。
 
- (会社のデバイスを使用しているかどうかに関わらず) 一部のユーザーは既に登録されている
- アプリで保護されたアプリケーションを使用して Azure AD に登録していないユーザーがリソースにアクセスするには、デバイスを登録する必要がある
- アプリで保護されたアプリケーションを使用して登録しているユーザーは、デバイスを再登録する必要はない

### <a name="configuration"></a>構成

**手順 1 - Exchange Online と SharePoint Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-based-conditional-access/62.png)

1. 条件付きアクセス ポリシーの**名前**。
1. **ユーザーとグループ**:各条件付きアクセス ポリシーでは、少なくとも 1 人のユーザーまたは 1 つのグループが選択されている必要があります。
1. **クラウド アプリ**:クラウド アプリとして、 **[Office 365 Exchange Online]** と **[Office 365 SharePoint Online]** を選択する必要があります。 

     ![条件付きアクセス](./media/app-based-conditional-access/02.png)

1. **条件**:**条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。 
   1. **デバイス プラットフォーム**として、 **[Android]** と **[iOS]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/03.png)

   1. **クライアント アプリ (プレビュー)** として、 **[モバイル アプリとデスクトップ クライアント]** と **[先進認証クライアント]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/91.png)

1. **アクセスコントロール**として、以下を選択する必要があります。
   - **デバイスは準拠としてマーク済みである必要があります**
   - **承認されたクライアント アプリが必要です (プレビュー)**
   - **選択したコントロールのいずれかが必要です**   
 
      ![条件付きアクセス](./media/app-based-conditional-access/11.png)

**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-based-conditional-access/61.png)

1. 条件付きアクセス ポリシーの**名前**。
1. **ユーザーとグループ**:各条件付きアクセス ポリシーでは、少なくとも 1 人のユーザーまたは 1 つのグループが選択されている必要があります。
1. **クラウド アプリ**:クラウド アプリとして、 **[Office 365 Exchange Online]** を選択する必要があります。 

   ![条件付きアクセス](./media/app-based-conditional-access/07.png)

1. **条件**:**条件**として、**クライアント アプリ**を構成する必要があります。 

   **クライアント アプリ (プレビュー)** として、 **[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[Exchange ActiveSync クライアント]** を選択します。

   ![条件付きアクセス](./media/app-based-conditional-access/91.png)

1. **アクセス コントロール**として、 **[承認されたクライアント アプリが必要です (プレビュー)]** を選択する必要があります。
 
   ![条件付きアクセス](./media/app-based-conditional-access/11.png)

**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**

![条件付きアクセス](./media/app-based-conditional-access/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online と SharePoint Online 用のアプリベースで準拠しているデバイス ベースのポリシー

このシナリオは、Exchange Online にアクセスするためのアプリベースで準拠しているデバイス ベースの条件付きアクセス ポリシーで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。
 
- iOS または Android でネイティブ メール アプリケーションを使用して電子メールを作成して Exchange に接続する
- アクセスするにはデバイスを登録する必要があることを示す電子メールを受信する
- ポータル サイトをダウンロードしてサインインする
- メールを確認すると、Outlook アプリを使用することを求められる
- Outlook アプリをダウンロードする
- Outlook アプリを開き、登録で使用される資格情報を入力する
- 電子メールにアクセスできる

すべての Intune アプリ保護ポリシーは、会社データへのアクセス時にアクティブになり、ユーザーにアプリケーションの再起動や追加 PIN の使用などを求めることができます (アプリケーションとプラットフォームがそのように構成されている場合)。

### <a name="configuration"></a>構成

**手順 1 - Exchange Online と SharePoint Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-based-conditional-access/62.png)

1. 条件付きアクセス ポリシーの**名前**。
1. **ユーザーとグループ**:各条件付きアクセス ポリシーでは、少なくとも 1 人のユーザーまたは 1 つのグループが選択されている必要があります。
1. **クラウド アプリ**:クラウド アプリとして、 **[Office 365 Exchange Online]** と **[Office 365 SharePoint Online]** を選択する必要があります。 

   ![条件付きアクセス](./media/app-based-conditional-access/02.png)

1. **条件**:**条件**として、**デバイス プラットフォーム**と**クライアント アプリ**を構成する必要があります。 
   1. **デバイス プラットフォーム**として、 **[Android]** と **[iOS]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/03.png)

   1. **クライアント アプリ (プレビュー)** として、 **[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[先進認証クライアント]** を選択します。

      ![条件付きアクセス](./media/app-based-conditional-access/91.png)

1. **アクセスコントロール**として、以下を選択する必要があります。
   - **デバイスは準拠としてマーク済みである必要があります**
   - **承認されたクライアント アプリが必要です (プレビュー)**
   - **選択したコントロールすべてが必要です**   
 
      ![条件付きアクセス](./media/app-based-conditional-access/13.png)



**手順 2 - Active Sync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成する必要があります。

![条件付きアクセス](./media/app-based-conditional-access/61.png)

1. 条件付きアクセス ポリシーの**名前**。
1. **ユーザーとグループ**:各条件付きアクセス ポリシーでは、少なくとも 1 人のユーザーまたは 1 つのグループが選択されている必要があります。
1. **クラウド アプリ**:クラウド アプリとして、 **[Office 365 Exchange Online]** を選択する必要があります。 

   ![条件付きアクセス](./media/app-based-conditional-access/07.png)

1. **条件**:**条件**として、**クライアント アプリ (プレビュー)** を構成する必要があります。 

   **クライアント アプリ (プレビュー)** として、 **[Mobile apps and desktop apps]** \(モバイル アプリとデスクトップ アプリ\) と **[Exchange ActiveSync クライアント]** を選択します。

   ![条件付きアクセス](./media/app-based-conditional-access/92.png)

1. **アクセスコントロール**として、以下を選択する必要があります。
   - **デバイスは準拠としてマーク済みである必要があります**
   - **承認されたクライアント アプリが必要です (プレビュー)**
   - **選択したコントロールすべてが必要です**   
 
      ![条件付きアクセス](./media/app-based-conditional-access/64.png)

**手順 3 - iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**

![条件付きアクセス](./media/app-based-conditional-access/09.png)

詳細については、「[Microsoft Intune でアプリとデータを保護する](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)」を参照してください。

## <a name="next-steps"></a>次の手順

条件付きアクセス ポリシーの構成方法を確認する場合は、[Azure Active Directory の条件付きアクセスを使用した特定のアプリケーションに対する MFA の要求](app-based-mfa.md)に関する記事をご覧ください。

環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」をご覧ください。 
