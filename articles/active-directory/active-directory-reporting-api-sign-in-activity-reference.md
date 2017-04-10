---
title: "Azure Active Directory サインイン アクティビティ レポート API リファレンス | Microsoft Docs"
description: "Azure Active Directory サインイン アクティビティ レポート API のリファレンス"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: dce65678f9fc96d5802a7b705689cc63e6532c84
ms.lasthandoff: 03/24/2017


---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Azure Active Directory サインイン アクティビティ レポート API リファレンス
このトピックは Azure Active Directory Reporting API に関するトピックのコレクションの一部です。  
Azure AD レポートは、コードまたは関連ツールを使用してサインイン アクティビティ レポート データにアクセスできるようにする API を提供します。
このトピックでは、 **サインイン アクティビティ レポート API**に関する参照情報について説明します。

参照:

* [サインイン アクティビティ](active-directory-reporting-azure-portal.md#sign-in-activities) に関する記事
* [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md) 」。

質問、問題点、またはフィードバックについては、 [AAD レポート ヘルプ](mailto:aadreportinghelp@microsoft.com)にお問い合わせください。

## <a name="who-can-access-the-api-data"></a>誰が API データにアクセスできますか。
* セキュリティ管理者またはセキュリティ リーダーの役割のユーザー
* グローバル管理者
* API へのアクセスを承認するすべてのアプリ (アプリの承認は、グローバル管理者のアクセス許可に基づいてのみ設定できます)

## <a name="prerequisites"></a>前提条件
Reporting API を使用してこのレポートにアクセスするには、次が必要です。

* [Azure Active Directory Premium P1 または P2 エディション](active-directory-editions.md)
* 「 [Azure AD Reporting API にアクセスするための前提条件](active-directory-reporting-api-prerequisites.md)」の完了。 

## <a name="accessing-the-api"></a>API へのアクセス
この API にアクセスするには、 [Graph Explorer](https://graphexplorer2.cloudapp.net) またはプログラム (例: PowerShell) を使用します。 PowerShell に AAD Graph REST 呼び出しで使用される OData フィルターの構文を正しく解釈させるには、バックティック (別名: グレーブ アクセント) 文字を使用して、$ 文字を “エスケープ” する必要があります。 バックティック文字は、 [PowerShell のエスケープ文字](https://technet.microsoft.com/library/hh847755.aspx)として機能し、PowerShell に $ 文字をリテラルに解釈させ、PowerShell 変数名 (例: $filter) と混同させないようにすることができます。

このトピックでは、Graph Explorer に焦点を当てます。 PowerShell の例については、 「 [PowerShell スクリプト](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script)」を参照してください。

## <a name="api-endpoint"></a>API エンドポイント
次のベース URI を使用して、この API にアクセスできます。  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



データ量により、この API は返されるレコードが&100; 万に制限されています。 

この呼び出しはバッチでデータを返します。 各バッチには最大 1000 個のレコードがあります。  
レコードの次のバッチを取得するには、Next リンクを使用します。 返されるレコードの最初のセットから [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) 情報を取得します。 skip トークンは結果セットの最後に配置されます。  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>サポートされているフィルター
フィルターの形式で API 呼び出しによって返されるレコードの数を絞り込むことができます。  
サインイン API 関連データについては、次のフィルターがサポートされています。

* **$top = \<返されるレコードの数\>** - 返されるレコードの数を制限します。 これは負荷の高い操作です。 数千のオブジェクトを取得する場合、このフィルターを使用する必要はりません。  
* **$filter = \<フィルター ステートメント\>** - サポートされているフィルター フィールドに基づいて、重要なレコードの種類を指定します。

## <a name="supported-filter-fields-and-operators"></a>サポートされているフィルター フィールドと演算子
重要なレコードの種類を指定するには、次のフィルター フィールドの&1; つまたは組み合わせのいずれかを含めることができるフィルター ステートメントを構築します。

* [signinDateTime](#signindatetime) - 日付または日付範囲を定義します。
* [userId](#userid) - 特定のユーザー ベースのユーザーの ID を定義します。
* [userPrincipalName](#userprincipalname) - 特定のユーザー ベースのユーザーのユーザー プリンシパル名 (UPN) を定義します。
* [appId](#appid) - 特定のアプリ ベースのアプリの ID を定義します。
* [appDisplayName](#appdisplayname) - 特定のアプリ ベースのアプリの表示名を定義します。
* [loginStatus](#loginStatus) - ログインの状態 (成功/失敗) を定義します。

> [!NOTE]
> Graph Explorer を使用する場合は、フィルター フィールドの各文字について大文字と小文字を正しく使用する必要があります。
> 
> 

返されるデータのスコープを絞り込むには、サポートされているフィルターとフィルター フィールドの組み合わせを構築します。 たとえば、次のステートメントは、2016 年 7 月 1 日～ 2016 年 7 月 6日 の間で上位 10 個のレコードを返します。

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**サポートされている演算子**: eq、ge、le、gt、lt

**例**:

特定の日付を使用する

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



日付の範囲を使用する    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**注**:

datetime パラメーターは UTC 形式にする必要があります。 

- - -
### <a name="userid"></a>userId
**サポートされている演算子**: eq

**例**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**注**:

userId の値は文字列の値です。

- - -
### <a name="userprincipalname"></a>userPrincipalName
**サポートされている演算子**: eq

**例**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**注**:

userPrincipalName の値は文字列の値です。

- - -
### <a name="appid"></a>appId
**サポートされている演算子**: eq

**例**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**注**:

appId の値は文字列の値です。

- - -
### <a name="appdisplayname"></a>appDisplayName
**サポートされている演算子**: eq

**例**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**注**:

appDisplayName の値は文字列の値です。

- - -
### <a name="loginstatus"></a>loginStatus
**サポートされている演算子**: eq

**例**:

    $filter=loginStatus+eq+'1'  


**注**:

loginStatus には 2 つのオプション (0 - 成功、1 - 失敗) があります。

- - -
## <a name="next-steps"></a>次のステップ
* フィルター処理されたサインイン アクティビティの例を参照しますか。 「 [Azure Active Directory サインイン アクティビティ レポート API のサンプル](active-directory-reporting-api-sign-in-activity-samples.md)」を確認してください。
* Azure AD Reporting API の詳細を確認しますか。 「 [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md)」を参照してください。


