---
title: ESR のグループ ポリシーと MDM の設定 - Azure Active Directory
description: Enterprise State Roaming の管理設定
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85252935"
---
# <a name="group-policy-and-mdm-settings"></a>グループ ポリシーと MDM の設定

ここで取り上げるグループ ポリシーとモバイル デバイス管理 (MDM) の設定は、会社所有のデバイスに使用を限定してください。これらのポリシーはユーザーのデバイス全体に適用されます。 MDM ポリシーを適用して設定の同期を無効にすると、ユーザー所有のパーソナル デバイスの使用に悪影響が生じます。 加えて、そのデバイスに存在する他のユーザー アカウントにもポリシーの影響が波及します。

会社の管理下にない個人用デバイスのローミングを管理する必要がある場合は、グループ ポリシーや MDM ではなく Azure Portal を使用して、ローミングを有効または無効にしてください。
利用可能なポリシー設定を以下の表で説明します。

> [!NOTE]
> この記事は、2015年7月に Windows 10 で起動された Microsoft Edge レガシ HTML ベースのブラウザーに適用されます。 この記事は、2020 年 1 月 15 日にリリースされた新しい Microsoft Edge Chromium ベースのブラウザーには適用されません。 新しい Microsoft Edge の同期動作の詳細については、「[Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)」を参照してください。

## <a name="mdm-settings"></a>MDM の設定

MDM のポリシー設定は、Windows 10 と Windows 10 Mobile の両方に適用されます。  Windows 10 Mobile のサポートは、ユーザーの OneDrive アカウントによる Microsoft アカウント ベースのローミングに対してのみ存在します。 Azure AD ベースの同期用にサポートされているデバイスの詳細については、「[デバイスとエンドポイント](enterprise-state-roaming-windows-settings-reference.md)」を参照してください。

| 名前 | 説明 |
| --- | --- |
| Microsoft アカウントの接続を許可 |デバイスの Microsoft アカウントを使った認証をユーザーに許可します。 |
| ユーザー設定の同期を許可 |Windows の設定データとアプリ データのローミングをユーザーに許可します。このポリシーを無効にすると、モバイル デバイスでの同期とバックアップが無効になります |

## <a name="group-policy-settings"></a>グループ ポリシー設定

グループ ポリシーの設定は、Active Directory ドメインに参加している Windows 10 デバイスに適用されます。 以下の表には、同期設定の管理用に見えるものの、実際には Windows 10 の Enterprise State Roaming では正しく機能しない既存の設定も記載されています。こうした設定には "使用しないでください" と記されています。

これらの設定は `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` にあります 

| 名前 | 説明 |
| --- | --- |
| アカウント: Microsoft アカウントをブロックします |このコンピューターにユーザーが新しい Microsoft アカウントを追加できないようにするポリシー設定です。 |
| 同期しない |ユーザーが Windows の設定データとアプリ データをローミングできないようにします。 |
| パーソナル設定を同期しない |"テーマ" グループの同期を無効にします。 |
| ブラウザーの設定を同期しない |"Internet Explorer" グループの同期を無効にします。 |
| パスワードを同期しない |"パスワード" グループの同期を無効にします。 |
| その他の Windows 設定を同期しない |"その他の Windows 設定" グループの同期を無効にします。 |
| デスクトップの個人設定を同期しない |使用しないでください。一切作用しません。 |
| 従量制課金接続で同期しない |携帯電話の 3G など従量制課金接続でのローミングを無効にします。 |
| アプリを同期しない |使用しないでください。一切作用しません。 |
| アプリの設定を同期しない |アプリ データのローミングを無効にします。 |
| スタート設定を同期しない |使用しないでください。一切作用しません。 |

## <a name="next-steps"></a>次のステップ

概要については、「[Enterprise State Roaming の概要](enterprise-state-roaming-overview.md)」を参照してください。
