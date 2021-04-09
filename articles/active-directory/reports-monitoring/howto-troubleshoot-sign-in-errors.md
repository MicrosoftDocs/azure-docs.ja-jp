---
title: サインイン エラー レポートでのトラブルシューティング方法 | Microsoft Docs
description: Azure Portal で Azure Active Directory レポートを使用してサインイン エラーをトラブルシューティングする方法について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de9f5a21f38f741dcf65d285446d9482716d4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85608128"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>方法: Azure Active Directory レポートを使用してサインイン エラーをトラブルシューティングする

Azure Active Directory (Azure AD) の[サインイン レポート](concept-sign-ins.md)では、次のように組織内のアプリケーションへのアクセスの管理に関する質問への回答を見つけることができます。

- ユーザーのサインインにどのようなパターンがあるか。
- 1 週間で何人のユーザーがユーザー サインインを行ったか。
- これらのサインインはどのような状態か。


さらに、サインイン レポートは、組織内のユーザーのサインイン エラーのトラブルシューティングに役立つこともあります。 このガイドでは、サインイン レポートでサインイン エラーを特定する方法について学習し、エラーの根本的な原因を理解するために使用します。

## <a name="prerequisites"></a>前提条件

必要なもの:

* Premium (P1/P2) ライセンスがある Azure AD テナント。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。
* テナントの **グローバル管理者**、**セキュリティ管理者**、**セキュリティ閲覧者**、または **レポート閲覧者** のロールに含まれているユーザー。 また、すべてのユーザーは自分のサインインにアクセスできます。 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>サインイン レポートを使用したサインイン エラーのトラブルシューティング

1. [Azure portal](https://portal.azure.com) に移動し、自分のディレクトリを選択します。
2. **[Azure Active Directory]** を選択し、**[監視]** セクションの **[サインイン]** を選択します。 
3. ユーザー名またはオブジェクト識別子、アプリケーション名または日付のいずれかで指定されたフィルターを使って、エラーを絞り込みます。 さらに、**[状態]** ドロップダウンから **[失敗]** を選択して、失敗したサインインのみを表示します。 

    ![結果をフィルター](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. 調査する必要がある失敗したサインインを特定します。 そのサインインを選択し、追加の詳細ウィンドウを開いて、失敗したサインインに関する詳細情報を確認します。 **[サインインのエラー コード]** と **[エラーの理由]** をメモします。 

    ![レコードの選択](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. 詳細ウィンドウの **[トラブルシューティングおよびサポート]** タブでもこの情報を見つけることができます。

    ![トラブルシューティングとサポート](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. 失敗の理由ではエラーについて説明します。 たとえば、上記のシナリオでの失敗の理由は、**ユーザー名またはパスワードが無効、またはオンプレミスのユーザー名またはパスワードが無効** です。 この修正プログラムは、単に適切なユーザー名とパスワードでもう一度サインインするためのものです。

7. [サインインのエラー コードの参照](reference-sign-ins-error-codes.md)でエラー コード (この例では **50126**) を検索することで、修復に関するアイデアなどの追加情報を取得できます。 

8. 他のすべてが失敗した場合、または推奨されるアクションを行っても問題が解決しない場合は、**[トラブルシューティングおよびサポート]** タブの手順に従って、[サポート チケットをオープン](../fundamentals/active-directory-troubleshooting-support-howto.md)にしてください。 

## <a name="next-steps"></a>次のステップ

* [サインインのエラー コードの参照](reference-sign-ins-error-codes.md)
* [サインイン レポートの概要](concept-sign-ins.md)
