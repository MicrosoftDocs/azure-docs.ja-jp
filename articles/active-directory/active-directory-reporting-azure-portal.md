---
title: "Azure Active Directory レポート | Microsoft Docs"
description: "Azure Active Directory レポートの概要を紹介します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 738c8f4a56586b87f03973ec258b0a3023affa60
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="azure-active-directory-reporting"></a>Azure Active Directory レポート

Azure Active Directory レポートを通じて、自分が管理する環境がどのような状態にあるのか、洞察を得ることができます。  
レポートから得たデータによって次のことが可能となります。

- 管理下にあるユーザーがアプリとサービスをどのように利用しているかを明らかにする。
- 自分が管理している環境の正常性に影響する潜在的リスクを検出する。
- ユーザーの作業を妨げている問題をトラブルシューティングする。  

このレポート機能のアーキテクチャは、大きく次の 2 つの要素から成ります。

- セキュリティ レポート
- アクティビティ レポート

![レポート](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>セキュリティ レポート

Azure Active Directory のセキュリティ レポートは、組織の ID 保護に役立てることができます。  
Azure Active Directory には、次の 2 種類のセキュリティ レポートがあります。

- **リスクのフラグ付きユーザー** - [リスクのフラグ付きユーザー セキュリティ レポート](active-directory-reporting-security-user-at-risk.md)では、セキュリティが侵害されている可能性のあるユーザー アカウントの概要を把握することができます。

- **危険なサインイン** - [危険なサインイン セキュリティ レポート](active-directory-reporting-security-risky-sign-ins.md)は、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 

**セキュリティ レポートにアクセスするために必要な Azure AD ライセンス**  
"リスクのフラグ付きユーザー" レポートと "危険なサインイン" レポートは、Azure Active Directory の全エディションで利用できます。  
ただしエディションによってレポートの粒度が異なります。 

- リスクのフラグ付きユーザーと危険なサインインは、**Azure Active Directory の Free エディションと Basic エディション**でも一覧表示できます。 

- **Azure Active Directory Premium 1** エディションではこのモデルが拡張され、各レポートについて検出された、基になるリスク イベントの一部を調べることができます。 

- **Azure Active Directory Premium 2** エディションでは、基になるリスク イベントについて最も詳しい情報が得られます。また、構成されているリスク レベルに対して自動的に対応するセキュリティ ポリシーを構成することができます。


## <a name="activity-reports"></a>アクティビティ レポート

Azure Active Directory には、次の 2 種類のアクティビティ レポートがあります。

- **監査ログ** - テナント内で実行されたすべてのタスクの履歴は、[監査ログ アクティビティ レポート](active-directory-reporting-activity-audit-logs.md)で把握できます。

- **サインイン** -  監査ログ レポートによって報告されたタスクをだれが実行したかは、[サインイン アクティビティ レポート](active-directory-reporting-activity-sign-ins.md)で明らかにすることができます。



**監査ログ レポート**から得られるシステム アクティビティの記録は、コンプライアンスに利用することができます。
特に、レポートから得たデータによって、日常的に発生する次のようなシナリオに対処することができます。

- テナント内のだれかが管理者グループにアクセスした。 だれがアクセス権を与えたのか。 

- 最近自分が配布準備をしたアプリの状況を知るために、特定のアプリにサインインしている一連のユーザーを把握したい。

- テナント内でパスワード リセットが何回実行されたかを知りたい。


**監査ログ レポートにアクセスするために必要な Azure AD ライセンス**  
監査ログ レポートは、自分がライセンスを所有している機能に関して利用できます。 特定の機能のライセンスがある場合、その機能の監査ログ情報にもアクセスすることができます。

詳細については、「[Azure Active Directory の機能と働き](https://www.microsoft.com/cloud-platform/azure-active-directory-features)」の「**Free、Basic、および Premium エディションの一般公開されている機能の比較**」を参照してください。   



**サインイン アクティビティ レポート**によって、次のような疑問に対する答えを見つけることができます。

- ユーザーのサインインにどのようなパターンがあるか。
- 1 週間で何人のユーザーがユーザー サインインを行ったか。
- これらのサインインはどのような状態か。


**サインイン アクティビティ レポートにアクセスするために必要な Azure AD ライセンス**  
サインイン アクティビティ レポートにアクセスするためには、ご利用のテナントに Azure AD Premium ライセンスが関連付けられている必要があります。


## <a name="programmatic-access"></a>プログラムによるアクセス

Azure Active Directory レポートでは、ユーザー インターフェイスからだけでなく、レポート データに[プログラムからアクセス](active-directory-reporting-api-getting-started-azure-portal.md)することができます。 これらのレポートのデータは、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに非常に役立ちます。 Azure AD レポート API は、一連の REST ベースの API を使用してプログラムによってデータにアクセスできるようにします。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。 


## <a name="next-steps"></a>次のステップ

Azure Active Directory における各種レポートの詳細については、次のページを参照してください。

- [リスクのフラグ付きユーザー レポート](active-directory-reporting-security-user-at-risk.md)
- [リスクの高いサインイン レポート](active-directory-reporting-security-risky-sign-ins.md)
- [監査ログ レポート](active-directory-reporting-activity-audit-logs.md)
- [サインイン ログ レポート](active-directory-reporting-activity-sign-ins.md)

レポート API を使ったレポート データへのアクセスについて詳しくは、次のページを参照してください。 

- [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started-azure-portal.md)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png
