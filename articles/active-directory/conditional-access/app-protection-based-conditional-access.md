---
title: Azure Active Directory の条件付きアクセスを使用してクラウド アプリへのアクセスにアプリ保護ポリシーを要求する | Microsoft Docs
description: Azure Active Directory の条件付きアクセスを使用して、クラウド アプリへのアクセスにアプリ保護ポリシーを要求する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 4/4/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e2e43f13352c56f947f5e560049ab0acf871599
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509476"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>条件付きアクセスを使用してクラウド アプリへのアクセスにアプリ保護ポリシーを要求する (プレビュー)

従業員は個人的な作業と業務上の作業のどちらにもモバイル デバイスを使用します。 そこで、従業員の生産性を確保しながら、データの損失を防止する必要があります。 Azure Active Directory (Azure AD) の条件付きアクセスを使用すると、クラウド アプリへのアクセスを制限することによって会社のデータを保護することができます。 まず、アプリ保護ポリシーのクライアント アプリを使用してください。

この記事では、データへのアクセスが許可される前にアプリ保護ポリシーを要求できる条件付きアクセス ポリシーを構成する方法について説明します。

## <a name="overview"></a>概要

[Azure AD の条件付きアクセス](overview.md)を使用すると、承認されたユーザーがリソースにアクセスする方法を微調整できます。 たとえば、クラウド アプリへのアクセスを信頼済みデバイスのみに制限できます。

会社のデータの保護には、[Intune のアプリ保護ポリシー](https://docs.microsoft.com/intune/app-protection-policy)を利用できます。 Intune アプリ保護ポリシーでは、モバイル デバイス管理 (MDM) ソリューションは必要ありません。 会社のデータは、デバイス管理ソリューションでデバイスを登録してもしなくても保護できます。

Azure Active Directory の条件付きアクセスでは、クラウド アプリへのアクセスを、アプリ保護ポリシーを受信するときに Intune が Azure AD に報告したクライアント アプリケーションに制限します。 たとえば、Exchange Online に対するアクセスを、Intune のアプリ保護ポリシーがある Outlook アプリのみに制限することができます。

条件付きアクセスに関する用語では、このようなクライアント アプリを*アプリ保護ポリシー*で保護されたポリシーと呼んでいます。  

![条件付きアクセス](./media/app-protection-based-conditional-access/05.png)

ポリシーで保護されたクライアント アプリの一覧については、[アプリ保護ポリシーの要件](technical-reference.md#approved-client-app-requirement)に関するページを参照してください。

アプリ保護ベースの条件付きアクセス ポリシーは、[デバイス ベースの条件付きアクセス ポリシー](require-managed-devices.md)などの他のポリシーと組み合わせることができます。 これにより、個人デバイスと会社のデバイスの両方のデータを柔軟に保護することができます。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>アプリ保護ベースの条件付きアクセスを要求する利点

マネージド デバイス向けの iOS および Android 用の Intune によって報告されるコンプライアンスと同様に、アプリ保護ポリシーが適用されているかどうかが Intune から Azure AD に報告されるようになりました。 条件付きアクセスでは、このポリシーをアクセス チェックとして使用できます。 この新しい条件付きアクセス ポリシー (アプリ保護ポリシー) により、セキュリティが向上します。 これにより、次のような管理者のエラーを防止されます。

- Intune のライセンスを持っていないユーザー。
- Intune アプリ保護ポリシーを受信できないユーザー。
- ポリシーを受信するように構成されていない Intune アプリ保護ポリシーのアプリ。

## <a name="before-you-begin"></a>開始する前に

この記事では、次の内容を熟知していることを前提としています。

- [アプリ保護ポリシーの要件](technical-reference.md#app-protection-policy-requirement)に関するテクニカル リファレンス。
- [承認されたクライアント アプリの要件](technical-reference.md#approved-client-app-requirement)に関するテクニカル リファレンス。
- [Azure Active Directory の条件付きアクセス](overview.md)の基本的な概念。
- [条件付きアクセス ポリシーの構成](app-based-mfa.md)方法。

## <a name="prerequisites"></a>前提条件

アプリ保護ベースの条件付きアクセス ポリシーを作成するには、次のことが必要です。

- Enterprise Mobility + Security または Azure Active Directory Premium サブスクリプション + Intune を持っていること。
- ユーザーが Enterprise Mobility + Security または Azure AD + Intune に対してライセンス付与されていること。
- クライアント アプリが Intune でアプリ保護ポリシーを受信するように構成されていること。
- ユーザーが Intune で Intune アプリ保護ポリシーを受信するように構成されていること。

## <a name="app-protection-based-policy-for-exchange-online"></a>Exchange Online 用のアプリ保護ベース

このシナリオは、Exchange Online にアクセスするためのアプリ保護ベースの条件付きアクセス ポリシーで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。

- iOS または Android 上でネイティブ メール アプリケーションを使用して電子メールを構成することによって Exchange に接続します。
- Outlook アプリを使用してのみアクセスできることを示す電子メールを受信します。
- そのリンクを使用してアプリケーションをダウンロードします。
- Outlook アプリケーションを開き、Azure AD 資格情報を使用してサインインします。
- 続行するには、**Microsoft Authenticator アプリ**または **Intune ポータル サイト**をインストールするよう求められます。
- アプリケーションをインストールし、Outlook アプリに戻って続行します。
- デバイスを登録するよう求められます。
- Intune アプリ保護ポリシーを受信できます。
- 電子メールにアクセスできます。

会社のデータにアクセスするには、アプリケーションに何らかの Intune アプリ保護ポリシーが必要です。 これらのポリシーでは、ユーザーはアプリケーションを再起動するか、または追加の PIN を使用するよう求められることがあります。 これは、ポリシーがそのアプリケーションおよびプラットフォーム用に構成されている場合です。

### <a name="configuration"></a>構成

**手順 1:Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成します。

![条件付きアクセス](./media/app-protection-based-conditional-access/01.png)

1. 条件付きアクセス ポリシーの名前を入力します。
1. **[割り当て]** の **[ユーザーとグループ]** で、条件付きアクセス ポリシーごとに少なくとも 1 人のユーザーまたは 1 つのグループを選択します。
1. **[クラウド アプリ]** で、 **[Office 365 Exchange Online]** を選択します。

   ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

1. **[条件]** で、 **[デバイス プラットフォーム]** と **[クライアント アプリ (プレビュー)]** を構成します。
   1. **[デバイス プラットフォーム]** で、 **[Android]** と **[iOS]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/03.png)

   1. **[クライアント アプリ (プレビュー)]** で、 **[モバイル アプリとデスクトップ クライアント]** と **[先進認証クライアント]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/91.png)

1. **[アクセス制御]** で、 **[アプリの保護ポリシーが必要 (プレビュー)]** を選択します。

   ![条件付きアクセス](./media/app-protection-based-conditional-access/05.png)

**手順 2:ActiveSync (EAS) を使用する Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成します。

![条件付きアクセス](./media/app-protection-based-conditional-access/06.png)

1. 条件付きアクセス ポリシーの名前を入力します。
1. **[割り当て]** の **[ユーザーとグループ]** で、条件付きアクセス ポリシーごとに少なくとも 1 人のユーザーまたは 1 つのグループを選択します。
1. **[クラウド アプリ]** で、 **[Office 365 Exchange Online]** を選択します。

   ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

1. **[条件]** で、 **[クライアント アプリ (プレビュー)]** を構成します。 

   1. **[クライアント アプリ (プレビュー)]** で、 **[モバイル アプリとデスクトップ クライアント]** と **[Exchange ActiveSync クライアント]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/92.png)

   1. **[アクセス制御]** で、 **[アプリの保護ポリシーが必要 (プレビュー)]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/05.png)

**手順 3:iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**

![条件付きアクセス](./media/app-protection-based-conditional-access/09.png)

詳細については、[Microsoft Intune を使用したアプリおよびデータの保護](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)に関するページを参照してください。

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Exchange Online 用のアプリ保護ベースまたは準拠しているデバイス ポリシー

このシナリオは、Exchange Online にアクセスするためのアプリ保護ベースまたは準拠しているデバイス ベースの条件付きアクセス ポリシーで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。
 
- ユーザーが (会社のデバイスを使用して、または使用せずに) 既に登録されています。
- アプリで保護されたアプリケーションを使用して Azure AD に登録されていないユーザーがリソースにアクセスするには、デバイスを登録する必要があります。
- アプリで保護されたアプリケーションを使用している登録済みのユーザーは、デバイスを再登録する必要はありません。
- 登録されていない場合、ユーザーは Intune アプリ保護ポリシーを受信できます。
- 登録されていない場合、ユーザーは Outlook と Intune アプリ保護ポリシーを使用して電子メールにアクセスできます。
- デバイスが登録されていない場合、ユーザーは Outlook を使用して電子メールにアクセスできます。

### <a name="configuration"></a>構成

**手順 1:Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成します。

![条件付きアクセス](./media/app-protection-based-conditional-access/62.png)

1. 条件付きアクセス ポリシーの名前を入力します。
1. **[割り当て]** の **[ユーザーとグループ]** で、条件付きアクセス ポリシーごとに少なくとも 1 人のユーザーまたは 1 つのグループを選択します。
1. **[クラウド アプリ]** で、 **[Office 365 Exchange Online]** を選択します。 

   ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

1. **[条件]** で、 **[デバイス プラットフォーム]** と **[クライアント アプリ (プレビュー)]** を構成します。 
 
   1. **[デバイス プラットフォーム]** で、 **[Android]** と **[iOS]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/03.png)

   1. **[クライアント アプリ (プレビュー)]** で、 **[モバイル アプリとデスクトップ クライアント]** と **[先進認証クライアント]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/91.png)

5. **[アクセス制御]** で、次のオプションを選択します。
   - **デバイスは準拠としてマーク済みである必要があります**
   - **アプリの保護ポリシーが必要 (プレビュー)**
   - **選択したコントロールのいずれかが必要です**   
 
      ![条件付きアクセス](./media/app-protection-based-conditional-access/11.png)

**手順 2:ActiveSync を使用した Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成します。

![条件付きアクセス](./media/app-protection-based-conditional-access/06.png)

1. 条件付きアクセス ポリシーの名前を入力します。
1. **[割り当て]** の **[ユーザーとグループ]** で、条件付きアクセス ポリシーごとに少なくとも 1 人のユーザーまたは 1 つのグループを選択します。
1. **[クラウド アプリ]** で、 **[Office 365 Exchange Online]** を選択します。 

   ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

1. **[条件]** で、 **[クライアント アプリ (プレビュー)]** を構成します。 

   **[クライアント アプリ (プレビュー)]** で、 **[モバイル アプリとデスクトップ クライアント]** と **[Exchange ActiveSync クライアント]** を選択します。

   ![条件付きアクセス](./media/app-protection-based-conditional-access/92.png)

1. **[アクセス制御]** で、次のオプションを選択します。
   - **デバイスは準拠としてマーク済みである必要があります**
   - **アプリの保護ポリシーが必要 (プレビュー)**
   - **選択したコントロールのいずれかが必要です**

      ![条件付きアクセス](./media/app-protection-based-conditional-access/11.png)

**手順 3:iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**

![条件付きアクセス](./media/app-protection-based-conditional-access/09.png)

詳細については、[Microsoft Intune を使用したアプリおよびデータの保護](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)に関するページを参照してください。

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Exchange Online 用のアプリ保護ベースおよび準拠しているデバイス ポリシー

このシナリオは、Exchange Online にアクセスするためのアプリ保護ベースおよび準拠しているデバイス ベースの条件付きアクセス ポリシーで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。
 
- iOS または Android 上でネイティブ メール アプリケーションを使用して電子メールを構成することによって Exchange に接続します。
- アクセスするにはデバイスを登録する必要があることを示す電子メールを受信します。
- Intune Company Portal をダウンロードし、ポータルにサインインします。
- メールを確認し、Outlook アプリを使用するよう求められます。
- Outlook アプリをダウンロードします。
- Outlook アプリを開き、登録で使用される資格情報を入力します。
- Intune アプリ保護ポリシーを受信できます。
- Outlook と Intune アプリ保護ポリシーを使用して電子メールにアクセスできます。

会社のデータへのアクセスが許可される前に、何らかの Intune アプリ保護ポリシーがアクティブ化されます。 これらのポリシーでは、ユーザーはアプリケーションを再起動するか、または追加の PIN を使用するよう求められることがあります。 これは、ポリシーがそのアプリケーションおよびプラットフォーム用に構成されている場合です。

### <a name="configuration"></a>構成

**手順 1:Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成します。

![条件付きアクセス](./media/app-protection-based-conditional-access/01.png)

1. 条件付きアクセス ポリシーの名前を入力します。
1. **[割り当て]** の **[ユーザーとグループ]** で、条件付きアクセス ポリシーごとに少なくとも 1 人のユーザーまたは 1 つのグループを選択します。
1. **[クラウド アプリ]** で、 **[Office 365 Exchange Online]** を選択します。 

   ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

1. **[条件]** で、 **[デバイス プラットフォーム]** と **[クライアント アプリ (プレビュー)]** を構成します。 
   1. **[デバイス プラットフォーム]** で、 **[Android]** と **[iOS]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/03.png)

   1. **[クライアント アプリ (プレビュー)]** で、 **[モバイル アプリとデスクトップ クライアント]** と **[先進認証クライアント]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/91.png)

1. **[アクセス制御]** で、次のオプションを選択します。
   - **デバイスは準拠としてマーク済みである必要があります**
   - **アプリの保護ポリシーが必要 (プレビュー)**
   - **選択したコントロールすべてが必要です**   
 
      ![条件付きアクセス](./media/app-protection-based-conditional-access/13.png)

**手順 2:ActiveSync を使用した Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成します。

![条件付きアクセス](./media/app-protection-based-conditional-access/06.png)

1. 条件付きアクセス ポリシーの名前を入力します。
1. **[割り当て]** の **[ユーザーとグループ]** で、条件付きアクセス ポリシーごとに少なくとも 1 人のユーザーまたは 1 つのグループを選択します。
1. **[クラウド アプリ]** で、 **[Office 365 Exchange Online]** を選択します。 

   ![条件付きアクセス](./media/app-protection-based-conditional-access/07.png)

1. **[条件]** で、 **[クライアント アプリ (プレビュー)]** を構成します。 

   **[クライアント アプリ (プレビュー)]** で、 **[モバイル アプリとデスクトップ クライアント]** と **[Exchange ActiveSync クライアント]** を選択します。

   ![条件付きアクセス](./media/app-protection-based-conditional-access/92.png)

1. **[アクセス制御]** で、次のオプションを選択します。
   - **デバイスは準拠としてマーク済みである必要があります**
   - **アプリの保護ポリシーが必要 (プレビュー)**
   - **選択したコントロールすべてが必要です**   
 
      ![条件付きアクセス](./media/app-protection-based-conditional-access/13.png)

**手順 3:iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**

![条件付きアクセス](./media/app-protection-based-conditional-access/09.png)

詳細については、[Microsoft Intune を使用したアプリおよびデータの保護](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)に関するページを参照してください。

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online と SharePoint Online 用のアプリ保護ベースまたはアプリベースのポリシー

このシナリオは、Exchange Online および SharePoint Online にアクセスするためのアプリ保護ベースまたは承認されたアプリのポリシーで構成されます。

### <a name="scenario-playbook"></a>シナリオのプレイブック

このシナリオでは、ユーザーを次のように想定しています。

- アプリ保護ポリシーの要件または承認されたアプリの要件をサポートするアプリの一覧にあるクライアント アプリケーションを構成します。  
- アプリ保護ポリシーの要件を満たし、Intune アプリ保護ポリシーを受信できるクライアント アプリケーションを使用します。
- Intune アプリ保護ポリシーをサポートする、承認されたアプリのポリシーの要件を満たすクライアント アプリケーションを使用します。
- 電子メールまたはドキュメントにアクセスするためのアプリケーションを開きます。
- Outlook アプリケーションを開き、Azure AD 資格情報を使用してサインインします。
- まだインストールされていない場合は、iOS での使用には Microsoft Authenticator を、Android での使用には Intune Company Portal をインストールするよう求められます。
- アプリケーションをインストールした後、Outlook アプリに戻って続行できます。
- デバイスを登録するよう求められます。
- Intune アプリ保護ポリシーを受信できます。
- Outlook と Intune アプリ保護ポリシーを使用して電子メールにアクセスできます。
- アプリ保護ポリシーの要件ではなく、承認されたアプリの要件にリストされているアプリを使用してサイトやドキュメントにアクセスできます。

会社のデータへのアクセスが許可される前に、何らかの Intune アプリ保護ポリシーが必要です。 これらのポリシーでは、ユーザーはアプリケーションを再起動するか、または追加の PIN を使用するよう求められることがあります。 これは、ポリシーがそのアプリケーションおよびプラットフォーム用に構成されている場合です。

**解説**

- アプリ保護ベースおよびアプリベースの両方の条件付きアクセス ポリシーをサポートする場合は、このシナリオを使用できます。
- この *OR* ポリシーでは、承認されたクライアント アプリの要件の前に、まずアプリ保護ポリシーの要件を持つアプリがアクセスに関して評価されます。

### <a name="configuration"></a>構成

**手順 1:Exchange Online 用の Azure AD 条件付きアクセス ポリシーを構成する**

この手順の条件付きアクセス ポリシーの場合、次のコンポーネントを構成します。

![条件付きアクセス](./media/app-protection-based-conditional-access/62.png)

1. 条件付きアクセス ポリシーの名前を入力します。
1. **[割り当て]** の **[ユーザーとグループ]** で、条件付きアクセス ポリシーごとに少なくとも 1 人のユーザーまたは 1 つのグループを選択します。
1. **[クラウド アプリ]** で、 **[Office 365 Exchange Online]** を選択します。 

   ![条件付きアクセス](./media/app-protection-based-conditional-access/02.png)

1. **[条件]** で、 **[デバイス プラットフォーム]** と **[クライアント アプリ (プレビュー)]** を構成します。 
   1. **[デバイス プラットフォーム]** で、 **[Android]** と **[iOS]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/03.png)

   1. **[クライアント アプリ (プレビュー)]** で、 **[モバイル アプリとデスクトップ クライアント]** と **[先進認証クライアント]** を選択します。

      ![条件付きアクセス](./media/app-protection-based-conditional-access/91.png)

1. **[アクセス制御]** で、次のオプションを選択します。
   - **承認済みクライアント アプリを必須にする**
   - **アプリの保護ポリシーが必要 (プレビュー)**
   - **選択したコントロールのいずれかが必要です**
 
      ![条件付きアクセス](./media/app-protection-based-conditional-access/12.png)

**手順 2:iOS および Android クライアント アプリケーション用の Intune アプリ保護ポリシーを構成する**

![条件付きアクセス](./media/app-protection-based-conditional-access/09.png)

詳細については、[Microsoft Intune を使用したアプリおよびデータの保護](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- 条件付きアクセス ポリシーの構成方法を確認する場合は、[Azure Active Directory の条件付きアクセスを使用した特定のアプリケーションに対する MFA の要求](app-based-mfa.md)に関する記事をご覧ください。
- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」をご覧ください。
