
---
title: "グループ ポリシーと MDM の設定 | Microsoft Docs"
description: "会社所有のデバイスで使用すべきグループ ポリシーとモバイル デバイス管理 (MDM) 設定に関する情報を提供します。 これらのポリシーはユーザーのデバイス全体に適用されます。"
services: active-directory
keywords: "Enterprise State Roaming 用のグループ ポリシーと MDM 設定とは, Enterprise State Roaming, Windows クラウド"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6bdec7bf35bb4a233200def36c0e697baaf15fec
ms.openlocfilehash: 5ca48cf3cc4b2f4d86463e36c73de47e386cff25
ms.lasthandoff: 01/05/2017


---
# <a name="group-policy-and-mdm-settings"></a>グループ ポリシーと MDM の設定
ここで取り上げるグループ ポリシーとモバイル デバイス管理 (MDM) の設定は、会社所有のデバイスに使用を限定してください。これらのポリシーはユーザーのデバイス全体に適用されます。 MDM ポリシーを適用して設定の同期を無効にすると、ユーザー所有のパーソナル デバイスの使用に悪影響が生じます。 加えて、そのデバイスに存在する他のユーザー アカウントにもポリシーの影響が波及します。

会社の管理下にない個人用デバイスのローミングを管理する必要がある場合は、グループ ポリシーや MDM ではなく Azure Portal を使用して、ローミングを有効または無効にしてください。
利用可能なポリシー設定を以下の表で説明します。

## <a name="mdm-settings"></a>MDM の設定
MDM のポリシー設定は、Windows 10 と Windows 10 Mobile の両方に適用されます。  Windows 10 Mobile のサポートは、ユーザーの OneDrive アカウントによる Microsoft アカウント ベースのローミングに対してのみ存在します。  Azure AD ベースの同期用にサポートされているデバイスの詳細については、「デバイスとエンドポイント」 のセクションをご覧ください。

| 名前 | Description |
| --- | --- |
| Microsoft アカウントの接続を許可 |デバイスの Microsoft アカウントを使った認証をユーザーに許可します。 |
| ユーザー設定の同期を許可 |Windows の設定データとアプリ データのローミングをユーザーに許可します。このポリシーを無効にすると、モバイル デバイスでの同期とバックアップが無効になります |

## <a name="group-policy-settings"></a>グループ ポリシーの設定
グループ ポリシーの設定は、Active Directory ドメインに参加している Windows 10 デバイスに適用されます。 以下の表には、同期設定の管理用に見えるものの、実際には Windows 10 の Enterprise State Roaming では正しく機能しない既存の設定も記載されています。こうした設定には "使用しないでください" と記されています。

| 名前 | Description |
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

## <a name="related-topics"></a>関連トピック
* [Enterprise State Roaming の概要](active-directory-windows-enterprise-state-roaming-overview.md)
* [Azure Active Directory の Enterprise State Roaming を有効にする](active-directory-windows-enterprise-state-roaming-enable.md)
* [設定とデータのローミングに関する FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Windows 10 ローミング設定リファレンス](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [トラブルシューティング](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


