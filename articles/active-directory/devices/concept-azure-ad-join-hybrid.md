---
title: ハイブリッド Azure AD 参加済みデバイスとは
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462527"
---
# <a name="hybrid-azure-ad-joined-devices"></a>ハイブリッド Azure AD 参加済みデバイス

10 年以上にわたって、多くの組織は、オンプレミスの Active Directory へのドメインの参加を使用して、次の操作を実行できるようにしてきました。

- IT 部門が一元化された場所から職場所有のデバイスを管理する。
- ユーザーが Active Directory の職場または学校アカウントを使用して自分のデバイスにサインインする。

通常、オンプレミスのフットプリントを持つ組織は、イメージング法を利用してデバイスをプロビジョニングし、多くの場合、**System Center Configuration Manager (SCCM)** または**グループ ポリシー(GP)** を使用してデバイスを管理します。

環境にオンプレミスの AD フットプリントがあるときに、Azure Active Directory が提供する機能も活用したい場合は、ハイブリッド Azure AD 参加済みデバイスを実装できます。 これらのデバイスは、オンプレミスの Active Directory に参加し、Azure Active Directory に登録されているデバイスです。

|   | Hybrid Azure AD Join |
| --- | --- |
| **定義** | デバイスにサインインするために組織アカウントを必要とする、オンプレミスの AD と Azure AD に参加します |
| **主な対象** | 既存のオンプレミス AD インフラストラクチャがあるハイブリッド組織に適しています |
|   | 組織内のすべてのユーザーに適用できます |
| **デバイスの所有権** | Organization |
| **オペレーティング システム** | Windows 10、8.1、7 |
|   | Windows Server 2008/R2、2012/R2、2016、2019 |
| **プロビジョニング** | Windows 10、Windows Server 2016/2019 |
|   | IT 担当者によりドメインに参加し、Azure AD Connect または ADFS の構成を使用して自動参加します |
|   | Windows Autopilot でドメインに参加し、Azure AD Connect または ADFS の構成を使用して自動参加します |
|   | Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 - MSI が必要です |
| **デバイスのサインイン オプション** | 以下を使用する組織アカウント: |
|   | パスワード |
|   | Win10 用の Windows Hello for Business |
| **デバイス管理** | グループ ポリシー |
|   | System Center Configuration Manager のスタンドアロンまたは Microsoft Intune との共同管理 |
| **主な機能** | クラウドとオンプレミスの両方のリソースへの SSO |
|   | ドメイン参加、または共同管理の場合は Intune を使用する条件付きアクセス |
|   | ロック画面でのセルフサービスによるパスワードのリセットおよび Windows Hello PIN のリセット |
|   | デバイス間での Enterprise State Roaming |

![ハイブリッド Azure AD 参加済みデバイス](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>シナリオ

以下に該当する場合は、ハイブリッド Azure AD 参加済みデバイスを使用します。

- Active Directory コンピューター認証に依存しているこれらのデバイスに展開される Win32 アプリがあります。
- 引き続き、グループ ポリシーを使用して、デバイス構成を管理したいと思っています。
- 引き続き、既存のイメージング ソリューションを使用して、デバイスをデプロイして構成したいと思っています。
- Windows 10 に加え、ダウンレベルの Windows 7 および 8.1 デバイスをサポートする必要があります

## <a name="next-steps"></a>次の手順

- [ハイブリッド Azure AD 参加の実装を計画する](hybrid-azuread-join-plan.md)
- [Azure portal を使用してデバイス ID を管理する](device-management-azure-portal.md)
- [Azure AD で古いデバイスを管理する](manage-stale-devices.md)
