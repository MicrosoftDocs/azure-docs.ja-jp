---
title: Azure Active Directory のデバイス ID とは
description: デバイス ID 管理を活用して、環境内のリソースにアクセスするデバイスを管理する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1d7d2ad6adefb83c3c313063a9e630458ccebc5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851767"
---
# <a name="what-is-a-device-identity"></a>デバイス ID とは

あらゆる形状とサイズのデバイスの急増および Bring Your Own Device (BYOD) の概念によって、IT 担当者は、次の 2 つの対立する目標を達成することを迫られています。

- 場所や時間を問わず、常にエンド ユーザーの生産性を高められるようにすること
- 組織の資産を保護すること

これらの資産を保護するために、IT スタッフは、まずデバイス ID を管理する必要があります。 IT スタッフは、Microsoft Intune などのツールを使用して、デバイス ID に基づいてセキュリティとコンプライアンスの基準を満たすことができます。 Azure Active Directory (Azure AD) を使用すると、これらのデバイスを通じてどこからでもデバイス、アプリ、およびサービスにシングル サインオンできます。

- ユーザーは、必要な組織の資産へのアクセス権を取得します。 
- IT スタッフは、組織を保護するために必要な制御を取得します。

デバイス ID 管理は、[デバイス ベースの条件付きアクセス](../conditional-access/require-managed-devices.md)の基盤です。 デバイス ベースの条件付きアクセス ポリシーにより、マネージド デバイスのみが、ご使用の環境内のリソースにアクセスできるようにします。

## <a name="getting-devices-in-azure-ad"></a>Azure AD でのデバイスの取得

Azure AD にデバイスを追加する場合、次の複数のオプションがあります。

- **Azure AD 登録済み**
   - Azure AD 登録済みのデバイスは、通常、個人所有のデバイスかモバイル デバイスであり、個人の Microsoft アカウントまたは別のローカル アカウントを使用してサインインします。
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD 参加済み**
   - Azure AD 参加済みのデバイスは、組織所有であり、その組織に属する Azure AD アカウントを使用してサインインします。 これらはクラウド内にのみ存在します。
      - Windows 10 
- **ハイブリッド Azure AD 参加済み**
   - ハイブリッド Azure AD 参加済みのデバイスは、組織所有であり、その組織に属する Azure AD アカウントを使用してサインインします。 これらはクラウド内とオンプレミスに存在します。
      - Windows 7、8.1、または 10
      - Windows Server 2008 以降

![[Azure AD デバイス] ブレードに表示されるデバイス](./media/overview/azure-ad-devices-all-devices-overview.png)

## <a name="device-management"></a>デバイス管理

Azure AD のデバイスは、Microsoft Intune、System Center Configuration Manager、グループ ポリシー (ハイブリッド Azure AD 参加)、モバイル アプリケーション管理 (MAM) ツール、その他のサードパーティ ツールなど、モバイル デバイス管理 (MDM) ツールを使用して管理できます。

## <a name="resource-access"></a>リソース アクセス

登録および参加により、ユーザーはクラウド リソースにシームレスにサインオン (SSO) できるようになり、一方で管理者はそれらのリソースに対して条件付きアクセス ポリシーを適用できるようになります。 

Azure AD 参加済みまたはハイブリッド Azure AD 参加済みのデバイスには、組織のオンプレミス リソースとクラウド リソースに SSO できるという利点があります。 詳細については、記事「[Azure AD 参加済みデバイス上でオンプレミス リソースへの SSO が機能するしくみ](azuread-join-sso.md)」をご覧ください。

## <a name="device-security"></a>デバイスのセキュリティ

- **Azure AD 登録済みデバイス**では、エンド ユーザーが管理するアカウントが使用されます。このアカウントは、Microsoft アカウントであるか、または次の 1 つ以上の方法で保護される、ローカル管理の別の資格情報です。
   - パスワード
   - PIN
   - Pattern
   - Windows Hello
- **Azure AD 参加済みデバイス、またはハイブリッド Azure AD 参加済みデバイス**では、次の 1 つ以上の方法で保護される、Azure AD の組織アカウントが使用されます。
   - パスワード
   - Windows Hello for Business

## <a name="provisioning"></a>プロビジョニング

Azure AD へのデバイスの追加は、セルフ サービス方式、または管理者が制御するプロビジョニング プロセスで実行できます。

## <a name="summary"></a>まとめ

Azure AD のデバイス ID 管理では、以下が可能です。

- デバイスを Azure AD に設定して管理するプロセスを簡略化する
- 組織のクラウドベースのリソースへの使いやすいアクセス方法をユーザーに提供する

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>次の手順

- [Azure AD 登録済みデバイス](concept-azure-ad-register.md)について学習する
- [Azure AD 参加済みデバイス](concept-azure-ad-join.md)について学習する
- [ハイブリッド Azure AD 参加済みデバイス](concept-azure-ad-join-hybrid.md)について学習する
- Azure portal でデバイス ID を管理する方法の概要については、[Azure portal によるデバイス ID の管理](device-management-azure-portal.md)に関するページを参照してください。
- デバイスベースの条件付きアクセスの詳細については、[Azure Active Directory デバイスベースの条件付きアクセス ポリシーの構成](../conditional-access/require-managed-devices.md)に関するページを参照してください。
