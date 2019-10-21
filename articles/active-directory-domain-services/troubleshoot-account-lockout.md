---
title: Azure AD Domain Services のアカウント ロックアウト問題を解決する | Microsoft Docs
description: Azure Active Directory Domain Services でユーザー アカウントのロックアウトを引き起こす一般的な問題を解決する方法について説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 29789f299f266c86d719d56cfbf8e262907f7264
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827090"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services マネージド ドメインでアカウントがロックアウトされる問題を解決する

悪意のあるサインインが繰り返し試行されることを防ぐ目的で、Azure AD DS では、定義されているしきい値を超えるとアカウントがロックアウトされます。 このアカウント ロックアウトは、サインイン攻撃がなくても偶発的に起こることがあります。 たとえば、ユーザーが間違ったパスワードを繰り返し入力したり、サービスで古いパスワードの使用が試行されたりしたとき、アカウントはロックアウトされます。

このトラブルシューティング記事では、アカウント ロックアウトが発生する理由、ロックアウト動作を設定する方法、ロックアウト問題を解決する目的でセキュリティ監査を確認する方法について説明します。

## <a name="what-is-an-account-lockout"></a>アカウント ロックアウトとは何ですか。

サインイン失敗に対して定義されているしきい値に到達すると、Azure AD DS のユーザー アカウントはロックアウトされます。 サインインが総当たり式に繰り返し試行される場合、自動化されたデジタル攻撃の可能性があります。このアカウント ロックアウト動作はユーザーをそのような攻撃から守るように設計されています。

**既定では、2 分間で 5 回パスワードの入力に失敗すると、アカウントは 30 分間ロックアウトされます。**

アカウント ロックアウトの既定のしきい値は、細かい設定が可能なパスワード ポリシーを利用して構成されます。 特定の一連の要件がある場合、アカウント ロックアウトの既定のしきい値をオーバーライドできます。 ただし、アカウントのロックアウト数を減らす目的でしきい値の上限を上げることはお勧めしません。 まず、アカウント ロックアウト動作の原因を解決してください。

### <a name="fine-grained-password-policy"></a>細かい設定が可能なパスワード ポリシー

細かい設定が可能なパスワード ポリシー (FGPP) を使用すると、パスワードおよびアカウント ロックアウトのポリシーに対して、ドメイン内の異なるユーザーに特定の制限を適用できます。 FGPP は、Azure AD DS で作成されたユーザーにのみ影響します。 Azure AD から Azure AD DS マネージド ドメインに同期されたクラウド ユーザーとドメイン ユーザーは、パスワード ポリシーの影響を受けません。

ポリシーは、Azure AD DS マネージド ドメイン内のグループの関連付けを通じて配布されます。すべての変更が、次回のユーザーのサインイン時に適用されます。 ポリシーを変更しても、既にロックアウトされているユーザー アカウントのロックが解除されることはありません。

細かい設定が可能なパスワード ポリシーの詳細については、[パスワードとアカウント ロックアウト ポリシーの構成][configure-fgpp]に関するページを参照してください。

## <a name="common-account-lockout-reasons"></a>アカウント ロックアウトの一般的な理由

アカウントがロックアウトされる最も一般的な理由には、悪意のある意図や要因を除いて、次のようなシナリオが含まれます。

* **ユーザーが自分自身をロックアウトした。**
    * 最近パスワードを変更した後、以前のパスワードを使用しませんでしたか。 ユーザーが古いパスワードをうっかり使用すると、2 分間で 5 回失敗という既定のポリシーがアカウント ロックアウトの原因となることがあります。
* **古いパスワードが指定されたアプリケーションまたはサービスが存在する。**
    * アカウントがアプリケーションまたはサービスによって使用されている場合、そのようなリソースで、古いパスワードによるサインインが繰り返し試されることがあります。 この動作により、アカウントのロックアウトが引き起こされることがあります。
    * 複数の異なるアプリケーションまたはサービスをまたぐアカウントの使用は最小限に留め、資格情報の使用場所を記録してください。 アカウントのパスワードが変更された場合、関連付けられているアプリケーションまたはサービスを適宜更新してください。
* **別の環境でパスワードが変更されたが、新しいパスワードがまだ同期されていない。**
    * オンプレミス AD DS 環境など、Azure AD DS の外部でアカウント パスワードが変更された場合、そのパスワード変更が Azure AD 経由で Azure AD DS まで同期するまで数分かかることがあります。
    * そのパスワード同期プロセスが完了する前に Azure AD DS 経由でリソースにサインインしようとすると、アカウントがロックアウトされます。

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>セキュリティ監査によるアカウント ロックアウトのトラブルシューティング

アカウント ロックアウトがいつ発生し、どこでロックアウトが発生するかトラブルシューティングするには、[Azure AD DS 向けセキュリティ監査 (現在のところ、プレビュー段階) を有効にします][security-audit-events]。 監査イベントのキャプチャは、この機能を有効にした時点からに限られます。 理想的には、トラブルシューティングするアカウント ロックアウト問題が発生する*前に*セキュリティ監査を有効にしてください。 ユーザー アカウントでロックアウト問題が繰り返される場合、それが次に発生するときのための準備としてセキュリティ監査を有効にできます。

セキュリティ監査を有効にすると、次のサンプル クエリから、*アカウント ロックアウト イベント* コード *4740* をレビューする方法が示されます。

直近 7 日間のすべてのアカウント ロックアウト イベントを表示します。

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

*driley* という名前のアカウントを対象に、直近 7 日間のすべてのアカウント ロックアウト イベントを表示します。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

2019 年 6 月 26 日午前 9 時 から 2019 年 7 月 1 日午前 0 時までのすべてのアカウント ロックアウト イベントを表示します。

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>次の手順

細かい設定が可能なパスワード ポリシーでアカウント ロックアウトのしきい値を調整する方法については、[パスワードとアカウント ロックアウト ポリシーの構成][configure-fgpp]に関するページを参照してください。

VM を Azure AD DS マネージド ドメインに参加させる際の問題が解決しない場合は、[ヘルプを参照し、Azure Active Directory のサポート チケットを開いてください][azure-ad-support]。

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
