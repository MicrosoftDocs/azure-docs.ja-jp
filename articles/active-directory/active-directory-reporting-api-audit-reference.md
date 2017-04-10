---
title: "Azure Active Directory 監査 API リファレンス | Microsoft Docs"
description: "Azure Active Directory 監査 API の概要について説明します。"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/16/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: b1de516d907826d3e6ede0783649f6101b381852
ms.openlocfilehash: 261cce0b8424f73df4c7ca86784a14e95a8336f1


---
# <a name="azure-active-directory-audit-api-reference"></a>Azure Active Directory 監査 API リファレンス
このトピックは Azure Active Directory Reporting API に関するトピックのコレクションの一部です。  
Azure AD レポートは、コードまたは関連ツールを使用して監査データにアクセスできるようにする API を提供します。
このトピックでは、 **監査 API**に関する参照情報について説明します。

参照:

* [監査ログ](active-directory-reporting-azure-portal.md#audit-logs)に関する記事
* [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md) 」。

質問、問題点、またはフィードバックについては、 [AAD レポート ヘルプ](mailto:aadreportinghelp@microsoft.com)にお問い合わせください。

## <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。
* セキュリティ管理者またはセキュリティ リーダーの役割のユーザー
* グローバル管理者
* API へのアクセスを承認するすべてのアプリ (アプリの承認は、グローバル管理者のアクセス許可に基づいてのみ設定できます)

## <a name="prerequisites"></a>前提条件
Reporting API を使用してこのレポートにアクセスするには、次が必要です。

* [Azure Active Directory Free 以上のエディション](active-directory-editions.md)
* 「 [Azure AD Reporting API にアクセスするための前提条件](active-directory-reporting-api-prerequisites.md)」の完了。 

## <a name="accessing-the-api"></a>API へのアクセス
この API にアクセスするには、 [Graph Explorer](https://graphexplorer2.cloudapp.net) またはプログラム (例: PowerShell) を使用します。 PowerShell に AAD Graph REST 呼び出しで使用される OData フィルターの構文を正しく解釈させるには、バックティック (別名: グレーブ アクセント) 文字を使用して、$ 文字を “エスケープ” する必要があります。 バックティック文字は、 [PowerShell のエスケープ文字](https://technet.microsoft.com/library/hh847755.aspx)として機能し、PowerShell に $ 文字をリテラルに解釈させ、PowerShell 変数名 (例: $filter) と混同させないようにすることができます。

このトピックでは、Graph Explorer に焦点を当てます。 PowerShell の例については、 「 [PowerShell スクリプト](active-directory-reporting-api-audit-samples.md#powershell-script)」を参照してください。

## <a name="api-endpoint"></a>API エンドポイント
次の URI を使用して、この API にアクセスできます。  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Azure AD 監査 API によって (OData 改ページ調整を使用) 返されるレコードの数に制限はありません。
レポート データの保持制限については、 [レポートの保持ポリシー](active-directory-reporting-retention.md)を参照してください。

この呼び出しはバッチでデータを返します。 各バッチには最大 1000 個のレコードがあります。  
レコードの次のバッチを取得するには、Next リンクを使用します。 返されるレコードの最初のセットから skiptoken 情報を取得します。 skip トークンは結果セットの最後に配置されます。  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>サポートされているフィルター
フィルターの形式で API 呼び出しによって返されるレコードの数を絞り込むことができます。  
サインイン API 関連データについては、次のフィルターがサポートされています。

* **$top = \<返されるレコードの数\>** - 返されるレコードの数を制限します。 これは負荷の高い操作です。 数千のオブジェクトを取得する場合、このフィルターを使用する必要はりません。     
* **$filter = \<フィルター ステートメント\>** - サポートされているフィルター フィールドに基づいて、重要なレコードの種類を指定します。

## <a name="supported-filter-fields-and-operators"></a>サポートされているフィルター フィールドと演算子
重要なレコードの種類を指定するには、次のフィルター フィールドの 1 つまたは組み合わせのいずれかを含めることができるフィルター ステートメントを構築します。

* [activityDate](#activitydate) - 日付または日付範囲を定義します。
* [activityType](#activitytype) - アクティビティの種類を定義します。
* [activity](#activity) - 文字列としてアクティビティを定義します。  
* [actor/name](#actorname) - アクターの名前の形式でアクターを定義します。
* [actor/objectid](#actorobjectid) - アクターの ID の形式でアクターを定義します。   
* [actor/upn](#actorupn) - アクターのユーザー プリンシパル名 (UPN) の形式でアクターを定義します。 
* [target/name](#targetname) - アクターの名前の形式でターゲットを定義します。
* [target/name](#targetobjectid) - ターゲットの ID の形式でターゲットを定義します。  
* [target/upn](#targetupn) - アクターのユーザー プリンシパル名 (UPN) の形式でアクターを定義します。   

- - -
### <a name="activitydate"></a>activityDate
**サポートされている演算子**: eq、ge、le、gt、lt

**例**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**注**:

datetime は UTC 形式にする必要があります。

- - -
### <a name="activitytype"></a>activityType
**サポートされている演算子**: eq

**例**:

    $filter=activityType eq 'User'    

**注**:

大文字と小文字の区別

- - -
### <a name="activity"></a>activity
**サポートされている演算子**: eq、contains、startsWith

**例**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**注**:

大文字と小文字の区別

- - -
### <a name="actorname"></a>actor/name
**サポートされている演算子**: eq、contains、startsWith

**例**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**注**:

大文字と小文字は区別されない

- - -
### <a name="actorobjectid"></a>actor/objectid
**サポートされている演算子**: eq

**例**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

- - -
### <a name="targetname"></a>target/name
**サポートされている演算子**: eq、contains、startsWith

**例**:

    $filter=targets/any(t: t/name eq 'some name')    

**注**:

大文字と小文字は区別されない

- - -
### <a name="targetupn"></a>target/upn
**サポートされている演算子**: eq、startsWith

**例**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**注**:

* 大文字と小文字は区別されない
* Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity を照会する場合は、完全な名前空間を追加する必要があります。

- - -
### <a name="targetobjectid"></a>target/name
**サポートされている演算子**: eq

**例**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>actor/upn
**サポートされている演算子**: eq、startsWith

**例**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**注**:

* 大文字と小文字は区別されない 
* Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity を照会する場合は、完全な名前空間を追加する必要があります。

- - -
## <a name="next-steps"></a>次のステップ
* フィルター処理されたシステム アクティビティの例を参照しますか。 [Azure Active Directory 監査 API のサンプル](active-directory-reporting-api-audit-samples.md)に関する記事を確認してください。
* Azure AD Reporting API の詳細を確認しますか。 「 [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md)」を参照してください。




<!--HONumber=Dec16_HO5-->


