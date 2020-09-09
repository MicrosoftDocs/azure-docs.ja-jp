---
title: Azure AD ギャラリー アプリを構成する管理者の資格情報の保存に関する問題
description: 既に Azure AD アプリケーション ギャラリーに一覧表示されているアプリケーションにユーザー プロビジョニングを構成するときの一般的な問題をトラブルシューティングする方法について説明します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/21/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 4f47954f3f4943846cab2dd9a38fd310abce3469
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782247"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Azure Active Directory ギャラリー アプリケーションへのユーザー プロビジョニングを構成している間の管理者の資格情報の保存に関する問題 

Azure Portal を使ってエンタープライズ アプリケーションの[自動ユーザー プロビジョニング](user-provisioning.md)を構成しているときに、次のような状況が発生する可能性があります。

* アプリケーションに入力した**管理者資格情報**は有効で、 **[テスト接続]** ボタンは機能します。 しかし、資格情報を保存することはできず、Azure Portal は一般的なエラー メッセージを返します。

SAML ベースのシングル サインオンも同じアプリケーションに対して構成されている場合、エラーの最も可能性の高い原因は、証明書と資格情報に対して Azure AD 内部で設定されているアプリケーションごとストレージ上限を超えたことです。

現在、アプリケーションの単一のインスタンスに関連付けられているすべての証明書、シークレット トークン、資格情報、および関連構成データの Azure AD での最大ストレージ容量は 1024 バイトです (Azure AD のサービス プリンシパル レコードとも呼ばれるます)。

SAML ベースのシングル サインオンが構成されている場合、SAML トークンの署名に使われる証明書がここに格納され、通常、領域の 50% 以上を消費します。

ユーザー プロビジョニングのセットアップ時に入力されるシークレット トークン、URI、通知メール アドレス、ユーザー名、およびパスワードにより、ストレージ制限を超える可能性があります。

## <a name="how-to-work-around-this-issue"></a>この問題を回避する方法 

現在、この問題を回避できる方法が 2 つあります。

1. **シングル サインオン用とユーザー プロビジョニング用に、2 つのギャラリー アプリケーション インスタンスを使う** - ギャラリー アプリケーション [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) を例にすると、ギャラリーから LinkedIn Elevate を追加して、シングル サインオン用に構成することができます。 プロビジョニング用には、Azure AD アプリ ギャラリーから LinkedIn Elevate の別のインスタンスを追加し、名前を "LinkedIn Elevate (Provisioning)" に設定します。 この 2 番目のインスタンスについては、[プロビジョニング](../saas-apps/linkedinelevate-provisioning-tutorial.md)は構成しますが、シングル サインオンは構成しません。 この回避策を使うときは、同じユーザーとグループを両方のアプリケーションに[割り当てる](../manage-apps/assign-user-or-group-access-portal.md)必要があります。 

2. **格納される構成データの量を減らす** - [プロビジョニング] タブの [[管理者資格情報]](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) セクションに入力したすべてのデータは、SAML 証明書と同じ場所に格納されます。 このすべてのデータの長さを短くすることはできない可能性がありますが、一部の省略可能な構成フィールド ( **[通知用電子メール]** など) は削除できます。

## <a name="next-steps"></a>次のステップ
[SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の構成](user-provisioning.md)
