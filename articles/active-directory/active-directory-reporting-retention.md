---
title: "Azure Active Directory レポートの保持ポリシー | Microsoft Docs"
description: "Azure Active Directory でのレポート データの保持ポリシー"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: aa7a69c933abfda3bf4d1ac1a298c4ba684efd7e
ms.contentlocale: ja-jp
ms.lasthandoff: 04/06/2017

---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory レポートの保持ポリシー


このトピックでは、最も一般的な質問に対する回答を、Azure Active Directory のさまざまなアクティビティ レポートのデータ保持と一緒に説明します。 

**Q: アクティビティ データの収集を開始するにはどうすればよいか**

**A:**

| Azure AD のエディション | コレクションの開始 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | サブスクリプションにサインアップしたとき |
| Azure AD Free | [Azure Active Directory ブレード](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)を初めて開いたとき、または [Reporting API](https://aka.ms/aadreports) を初めて使用したとき  |

---
**Q: Azure Portal でアクティビティ データを使用できるようになるのはいつか**

**A:**

- **すぐに** - Azure クラシック ポータルで既にレポートを操作している場合
- **2 時間以内** - Azure クラシック ポータルでレポートを有効にしていない場合

---
**Q: セキュリティ シグナルの収集を開始するにはどうすればよいか**  

**A:** セキュリティ シグナルの場合、収集プロセスは Identity Protection センターを使用することを選択した時点から開始されます。 


---
**Q: 収集されたデータのどのくらい保存されるのか**

**A:**

**アクティビティ レポート**    

| レポート                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| ディレクトリ監査        | 7 日        | 30 日             | 30 日             |
| サインイン アクティビティ       | 7 日        | 30 日             | 30 日             |

**セキュリティ シグナル**

| レポート         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| リスクのあるユーザー  | 7 日        | 30 日             | 90 日間             |
| リスクの高いサインイン | 7 日        | 30 日             | 90 日間             |

---
