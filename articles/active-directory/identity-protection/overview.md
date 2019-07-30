---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Azure AD Identity Protection を使用して、侵害された ID またはデバイスを攻撃者が悪用する能力を制限する方法、および以前に疑われた、または侵害を確認された ID またはデバイスを保護する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99a542d3208d5871d88c966fffc65cf16e0fbeee
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335399"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection とは

Azure Active Directory [Identity](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection を使用して、組織は、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成できます。

## <a name="get-started"></a>作業開始

Microsoft は 10 年以上にわたってクラウド ベースの ID を保護してきました。 Azure Active Directory Identity Protection を使用すると、お客様の環境で、Microsoft が使用する保護システムと同じものを使って、ID をセキュリティで保護できます。

ほとんどのセキュリティ侵害は、攻撃者がユーザーの ID を盗むことにより環境にアクセスできるようになると発生します。 長年にわたって、攻撃者はサード パーティのセキュリティ違反をより巧妙に利用し、高度なフィッシング攻撃を使っています。 低い特権のユーザー アカウントであってもアクセス権を得た攻撃者はすぐに、かつ比較的簡単に、横方向に移動し、重要な企業リソースにアクセスしてしまいます。

このため、次が必要です。

- 権限レベルにかかわらず、すべての ID を保護する
- 侵害された ID が悪用されるのを事前に防止する

侵害された ID を検出するのは簡単な作業ではありません。 Azure Active Directory は、アダプティブ機械学習アルゴリズムとヒューリスティックを使用して、ID が侵害された可能性があることを示す、異常と疑わしいインシデントを検出します。 Identity Protection は、このデータを使用してレポートとアラートを生成し、これにより、ユーザーは検出された問題を評価し、適切な修復または軽減のアクションを実行することができます。

Azure Active Directory Identity Protection は単なる監視とレポート作成のツールではありません。 指定したリスク レベルに達したときに、検出された問題が自動的に対処されるようにリスク ベースのポリシーを構成することで、組織の ID を保護できます。 こうしたポリシーと、Azure Active Directory および [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS) によって提供される他の条件付きアクセス コントロールにより、パスワードのリセットや多要素認証の適用などのアダプティブ修復アクションを自動的にブロックまたは開始できます。

### <a name="identity-protection-capabilities"></a>Identity Protection の機能

**脆弱性とリスクの高いアカウントの検出:**  

- 脆弱性を目立たせることにより全体的なセキュリティ対策を向上させるためのカスタム推奨事項を提供します
- サイン インのリスク レベルを計算します
- ユーザーのリスク レベルを計算します

**リスク イベントの調査:**

- リスク イベントの通知を送信します
- 関連情報とコンテキスト情報を使用してリスク イベントを調査します
- 調査を追跡するための基本的なワークフローを提供します
- パスワード リセットなどの修復アクションへの簡単なアクセスを提供します

**リスクに基づく条件付きアクセス ポリシー:**

- サインインのブロックまたは多要素認証チャレンジの要求によりリスクの高いサインインを抑制するポリシー
- リスクの高いユーザー アカウントをブロックまたはセキュリティ保護するためのポリシー
- 多要素認証用に登録するようユーザーに要求するポリシー

## <a name="identity-protection-roles"></a>Identity Protection のロール

Identity Protection 実装の管理アクティビティの負荷を分散するため、いくつかのロールを割り当てることができます。 Azure AD Identity Protection は、3 つのディレクトリ ロールをサポートします。

| Role | できること | できないこと |
| :-- | --- | --- |
| 全体管理者 | Identity Protection へのフル アクセス、Identity Protection の配布準備| |
| セキュリティ管理者 | Identity Protection へのフル アクセス | Identity Protection の配布準備、ユーザーのパスワードのリセット |
| セキュリティ閲覧者 | Identity Protection への読み取り専用アクセス | Identity Protection の配布準備、ユーザーの修復、ポリシーの構成、パスワードのリセット |

詳細については、「[Azure Active Directory での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

## <a name="detection"></a>検出

### <a name="vulnerabilities"></a>脆弱性

Azure Active Directory Identity Protection は、構成を分析し、ユーザーの ID に影響する可能性がある脆弱性を検出します。 詳細については、「[Azure Active Directory Identity Protection で検出される脆弱性](vulnerabilities.md)」を参照してください。

### <a name="risk-events"></a>リスク イベント

Azure Active Directory は、アダプティブ機械学習アルゴリズムとヒューリスティックを使用して、ユーザーの ID に関連する疑わしいアクションを検出します。 疑わしいアクションが検出されると、アクションごとにレコードが作成されます。 こうしたレコードは、リスク イベントとも呼ばれます。  
詳細については、「[Azure Active Directory risk events (Azure Active Directory リスク イベント)](../active-directory-identity-protection-risk-events.md)」を参照してください。

## <a name="investigation"></a>調査

Identity Protection を使用するときは、通常、Identity Protection ダッシュボードから開始します。

![修復](./media/overview/1000.png "Remediation")

ダッシュボードからは次のものにアクセスできます。

- **リスクのフラグ付きユーザー**、**リスク イベント**、**脆弱性**などのレポート
- **セキュリティ ポリシー**、**通知**、**多要素認証の登録**の構成などの設定

通常、これが調査の起点になります。調査は、修復または軽減の手順が必要であるかどうかを決定し、ID が侵害を受けているかどうか、およびどのように侵害されたかを理解し、侵害された ID がどのように使用されたかを理解するために、アクティビティ、ログ、およびリスク イベントに関連するその他の関連情報を確認するプロセスです。

調査アクティビティを、Azure Active Directory Protection が電子メールごとに送信する [通知](notifications.md) と関連付けることができます。

## <a name="policies"></a>ポリシー

応答の自動化の実装に、Azure Active Directory Identity Protection では、次の 3 つのポリシーを用意しています。

- [Multi-Factor Authentication 登録ポリシー](howto-mfa-policy.md)
- [ユーザー リスク ポリシー](howto-user-risk-policy.md)
- [サインインのリスク ポリシー](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>次の手順

- [Channel 9:Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview) (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)
- [Azure Active Directory Identity Protection の有効化](enable.md)
