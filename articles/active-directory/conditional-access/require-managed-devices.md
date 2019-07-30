---
title: 方法 - Azure Active Directory の条件付きアクセスを使用してクラウド アプリへのアクセスにマネージド デバイスを要求する | Microsoft Docs
description: クラウド アプリへのアクセスにマネージド デバイスを要求する Azure Active Directory (Azure AD) デバイスベースの条件付きアクセス ポリシーを構成する方法を説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9c99b8390cd43c3f0767123684fe06e0ae74f86
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509365"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>方法:条件付きアクセスを使用してクラウド アプリへのアクセスにマネージド デバイスを要求する

モバイル ファースト、クラウド ファーストの世界では、Azure Active Directory (Azure AD) を使用して、アプリおよびサービスにどこからでもシングル サインオンできます。 承認されたユーザーはモバイル デバイスと個人デバイスを含むさまざまなデバイスからクラウド アプリにアクセスできます。 ただし、多くの環境では、セキュリティとコンプライアンスの基準を満たすデバイスのみがアクセスする必要があるアプリが少なくともいくつかはあります。 これらのデバイスはマネージド デバイスとも呼ばれます。 

この記事では、環境内の特定のクラウド アプリへのアクセスにマネージド デバイスを要求する条件付きアクセス ポリシーを構成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

クラウド アプリへのアクセスにマネージド デバイスを要求すると、**Azure AD の条件付きアクセス**と **Azure AD のデバイス管理**が連携します。 これらの領域を十分に理解してない場合は、次のトピックを先に読んでおく必要があります。

- **[Azure Active Directory の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)** - この記事は、条件付きアクセスと関連する用語の概念的な概要を説明しています。
- **[Azure Active Directory のデバイス管理の概要](../devices/overview.md)** - この記事は、組織の管理下にあるデバイスを取得する際に使用できるさまざまなオプションの概要を説明しています。 

## <a name="scenario-description"></a>シナリオの説明

セキュリティと生産性のバランスを極めるのは困難です。 クラウド リソースへのアクセスでサポートされるデバイスの急増は、ユーザーの生産性の向上に役立ちます。 その一方で、環境内の特定のリソースは、保護レベルが不明なデバイスからアクセスされるのは望ましくない可能性があります。 影響を受けるリソースについては、ユーザーがマネージド デバイスを使用してのみアクセスできることを要求する必要があります。 

Azure AD 条件付きアクセスを使用すると、アクセス許可を付与するための次に示すポリシーを 1 つ使って、この要件に対応できます。

- 選択したクラウド アプリに対して
- 選択したユーザーおよびグループに対して
- マネージド デバイスの要求

## <a name="managed-devices"></a>マネージド デバイス  

簡単に言えば、マネージド デバイスとは、*何らかの*組織的な管理下に置かれたデバイスのことです。 Azure AD では、マネージド デバイスの前提条件は、デバイスが Azure AD に登録されていることです。 デバイスを登録すると、デバイスの ID がデバイス オブジェクトの形式で作成されます。 このオブジェクトは、デバイスに関する状態情報を追跡するために Azure によって使用されます。 Azure AD 管理者は既に、このオブジェクトを使用してデバイスの状態を切り替える (有効化/無効化する) ことができます。
  
![デバイス ベースの条件](./media/require-managed-devices/32.png)

Azure AD に登録されているデバイスを取得するには、次の 3 つのオプションがあります: 

- **Azure AD 登録済みデバイス** - Azure AD に登録されたパーソナル デバイスを取得します
- **Azure AD 参加済みデバイス** - Azure AD に登録されたオンプレミス AD に参加していない、組織の Windows 10 デバイスを取得します。 
- **ハイブリッド Azure AD 参加済みデバイス** - Azure AD に登録されたオンプレミス AD に参加している Windows 10 デバイスまたはサポートされるダウンレベルのデバイスを取得します。

これらの 3 つのオプションについては、「[デバイス ID とは](../devices/overview.md)」という記事で説明されています。

マネージド デバイスになることができる登録済みデバイスは、**ハイブリッド Azure AD 参加済みデバイス**または**準拠とマークされたデバイス**である必要があります。  

![デバイス ベースの条件](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイスが必要

条件付きアクセス ポリシーで、選択されたクラウド アプリにはマネージド デバイスを使用しなければアクセスできないことを宣言するために、 **[ハイブリッド Azure AD 参加済みのデバイスが必要]** を選択できます。 

![デバイス ベースの条件](./media/require-managed-devices/10.png)

この設定は、オンプレミス AD に参加している Windows 10 デバイス、または Windows 7 または Windows 8 などのダウンレベルのデバイスにのみ適用されます。 ハイブリッド Azure AD 参加を使用する方法でのみ、これらのデバイスを Azure AD に登録することができ、これは Windows 10 デバイスを登録するための[自動プロセス](../devices/hybrid-azuread-join-plan.md)です。 

![デバイス ベースの条件](./media/require-managed-devices/45.png)

ハイブリッド Azure AD 参加済みデバイスをマネージド デバイスにする条件は何でしょうか?  オンプレミス AD に参加しているデバイスの場合、これらのデバイスに対する統制は、**System Center Configuration Manager (SCCM)** などの管理ソリューションや、それらを管理するための**グループ ポリシー (GP)** を使用して適用されると想定されます。 これらの方法のいずれかがデバイスに適用されているかどうか Azure AD で判断する方法がないため、ハイブリッド Azure AD 参加済みデバイスを要求することは、マネージド デバイスを要求するための比較的弱いメカニズムです。 当該デバイスがハイブリッド Azure AD 参加済みデバイスでもある場合、オンプレミスのドメイン参加済みデバイスに適用されている方法がマネージド デバイスを構成するために十分に強いかどうかを判断することは管理者の責任です。

## <a name="require-device-to-be-marked-as-compliant"></a>デバイスは準拠としてマーク済みである必要がある

*デバイスは準拠としてマーク済みである必要がある*というオプションは、マネージド デバイスを要求するための最も厳密な形式です。

![デバイス ベースの条件](./media/require-managed-devices/11.png)

このオプションでは、デバイスが Azure AD に登録されている必要があり、次のものによって準拠とマークされている必要もあります。
         
- Intune。
- Azure AD 統合によって Windows 10 デバイスを管理するサードパーティ製のモバイル デバイス管理 (MDM) システム。 Windows 10 以外のデバイスの OS の種類のサードパーティ製 MDM システムはサポートされていません。
 
![デバイス ベースの条件](./media/require-managed-devices/46.png)

準拠としてマークされているデバイスの場合、次のことを想定できます。 

- 会社のデータにアクセスするために従業員が使用するモバイル デバイスが管理されている
- 従業員が使用するモバイル アプリが管理されている
- 従業員が情報にアクセスして共有する方法の統制を補助することによって、会社の情報が保護されている
- デバイスとそのアプリが会社のセキュリティ要件に準拠している

## <a name="next-steps"></a>次の手順

環境内でデバイスベースの条件付きアクセス ポリシーを構成する前に、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」を参照してください。
