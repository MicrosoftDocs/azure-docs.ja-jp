---
title: "Azure Active Directory レポートの保持ポリシー | Microsoft Docs"
description: "Azure Active Directory でのレポート データの保持ポリシー"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 47594d1d435ee92d57150a01a99765bab0e87846
ms.openlocfilehash: 2ac62c2c04975167b243b49bf2c536900e77df73
ms.lasthandoff: 02/03/2017


---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory レポートの保持ポリシー
*このドキュメントは、[Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)の一部です。*


このトピックでは、最も一般的な質問に対する回答を、Azure Active Directory のさまざまなアクティビティ レポートのデータ保持と一緒に説明します。 

アクティビティ データの収集を開始するにはどうすればよいか

| Azure AD のエディション | コレクションの開始 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | サブスクリプションにサインアップしたとき |
| Azure AD Free | [Azure Active Directory ブレード](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)を初めて開いたとき、または [Reporting API](https://aka.ms/aadreports) を初めて使用したとき  |


Azure Portal でアクティビティ データを使用できるようになるのはいつか

- **すぐに** - Azure クラシック ポータルで既にレポートを操作している場合
- **2 時間以内** - Azure クラシック ポータルでレポートを有効にしていない場合

セキュリティ シグナルの収集を開始するにはどうすればよいか  
セキュリティ シグナルの場合、収集プロセスは Identity Protection センターを使用することを選択した時点から開始されます。 

収集されたデータのどのくらい保存されるのか

**アクティビティ レポート**    

| レポート | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| ディレクトリ監査 | 7 日 | 30 日 | 30 日 |
| サインイン アクティビティ |    7 日 | 30 日 | 30 日 |

**セキュリティ シグナル**

| レポート | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--    | :--           | :--                | :--                |
| リスクの高いサインイン | 7 日 | 30 日 | 90 日間 |



