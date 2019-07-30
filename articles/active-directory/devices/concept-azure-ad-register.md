---
title: Azure AD 登録済みデバイスとは
description: デバイス ID 管理を活用して、環境内のリソースにアクセスしているデバイスを管理する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462531"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 登録済みデバイス

Azure AD 登録済みデバイスの目標は、Bring Your Own Device (BYOD) またはモバイル デバイス シナリオのサポートをユーザーに提供することです。 これらのシナリオでは、ユーザーは個人所有のデバイスを使用して、組織の Azure Active Directory の管理下にあるリソースにアクセスできます。

|   | Azure AD 登録済み |
| --- | --- |
| **定義** | デバイスにサインインするための組織アカウントを必要としない Azure AD に登録します |
| **主な対象** | 次の条件に該当するすべてのユーザーに適用できます。 |
|   | Bring Your Own Device (BYOD) |
|   | モバイル デバイス |
| **デバイスの所有権** | ユーザーまたは組織 |
| **オペレーティング システム** | Windows 10、iOS、Android、および MacOS |
| **プロビジョニング** | Windows 10 – 設定 |
|   | iOS/Android – ポータル サイトまたは Microsoft Authenticator アプリ |
|   | MacOS – ポータル サイト |
| **デバイスのサインイン オプション** | エンドユーザーのローカル資格情報 |
|   | パスワード |
|   | Windows Hello |
|   | PIN |
|   | 他のデバイスの生体認証またはパターン |
| **デバイス管理** | モバイル デバイス管理 (例:Microsoft Intune) |
|   | モバイル アプリケーション管理 |
| **主な機能** | クラウド リソースへの SSO |
|   | Intune への登録時の条件付きアクセス |
|   | アプリ保護ポリシーを使用する条件付きアクセス |
|   | Microsoft Authenticator アプリでの電話によるサインインを有効にします |

![Azure AD 登録済みデバイス](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 登録済みデバイスには、Windows 10 デバイスの Microsoft アカウントなどのローカル アカウントを使用してサインインしますが、さらに、組織リソースにアクセスするために Azure AD アカウントが関連付けられます。 組織内のリソースへのアクセスは、その Azure AD アカウントと、デバイス ID に適用される条件付きアクセス ポリシーに基づいて、より制限することができます。

管理者は、Microsoft Intune などの Mobile Device Management (MDM) ツールを使用して、これらの Azure AD 登録済みデバイスをセキュリティで保護し、さらに制御することができます。 MDM では、ストレージの暗号化、パスワードの複雑さ、セキュリティ ソフトウェアを常に最新の状態に保つことを求めるなど、組織に必要な構成を適用する手段が提供されます。 

Windows 10 の設定メニューを使用して手動でまたは初めて作業アプリケーションにアクセスするときに、Azure AD の登録を行うことができます。 

## <a name="scenarios"></a>シナリオ

組織内のユーザーは、メールのツールにアクセスし、休暇届を出し、自宅の PC から登録したいと考えています。 あなたの組織では、Intune 準拠デバイスからのアクセスを必要とする条件付きアクセス ポリシーの背後にこれらのツールが存在します。 ユーザーが組織アカウントを追加し、Azure AD に自宅の PC を登録すると、必要な Intune ポリシーが適用され、ユーザーにリソースへのアクセス権が付与されます。

別のユーザーは、ルート化されている個人用の Android フォンで組織のメールにアクセスしたいと考えています。 会社では準拠デバイスが必要であり、ルート化されたすべてのデバイスをブロックするために Intune コンプライアンス ポリシーが作成されています。 このデバイス上の組織リソースへの従業員からのアクセスは阻止されます。

## <a name="next-steps"></a>次の手順

- [Azure portal を使用してデバイス ID を管理する](device-management-azure-portal.md)
- [Azure AD で古いデバイスを管理する](manage-stale-devices.md)
