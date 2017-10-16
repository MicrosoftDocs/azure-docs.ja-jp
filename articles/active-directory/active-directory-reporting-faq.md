---
title: "Azure Active Directory レポートに関する FAQ | Microsoft Docs"
description: "Azure Active Directory レポートに関する FAQ。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: accf292f70bf0eafdefc00c3feeaf8e346605401
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory レポートに関する FAQ

この記事では、Azure Active Directory レポートに関してよく寄せられる質問 (FAQ) に対する回答を示します。  
詳細については、[Azure Active Directory レポート](active-directory-reporting-azure-portal.md)に関する記事を参照してください。 

**Q: Azure Portal でのアクティビティ ログ (監査およびサインイン) のデータ保持期間はどのぐらいですか。** 

**A:** 無料でご利用のお客様は 7 日間です。Azure AD Premium 1 または Premium 2 ライセンスに切り替えると、データに最大で 30 日間アクセスできるようになります。 保持期間の詳細については、「[Azure Active Directory レポートの保持ポリシー](active-directory-reporting-retention.md)」をご覧ください。

--- 

**Q: 自分のタスクを完了した後、アクティビティ データが表示されるまでどのぐらい時間がかかりますか。**

**A:** 監査アクティビティ ログの待機時間は 15 分から 1 時間です。 サインイン アクティビティ ログの待機時間はほとんどのレコードで 15 分以上で、一部のレコードでは最大 2 時間です。

---

**Q: Azure Portal でアクティビティ ログを確認、および API を通じてデータを取得するにはグローバル管理者である必要がありますか。**

**A:** いいえ。 **セキュリティ閲覧者**、**セキュリティ管理者**、または**グローバル管理者**であれば、Azure Portal でレポート データを確認でき、また API を通じてアクセスすることもできます。

---

**Q: Azure Portal を通じて Office 365 のアクティビティ ログ情報を取得できますか。**

**A:** Office 365 のアクティビティ ログと Azure AD アクティビティ ログではディレクトリ リソースの大部分が共有されていますが、Office 365 のアクティビティ ログをすべて閲覧する場合は、Office 365 管理センターにアクセスして Office 365 アクティビティ ログ情報を取得する必要があります。

---


**Q: Office 365 のアクティビティ ログの情報を取得するには、どの API を使えばよいですか。**

**A:** [API を通じて Office 365 アクティビティ ログ](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview)にアクセスするには、Office 365 Management API を使用します。

---

**Q: Azure Portal からレコードをいくつまでダウンロードすることができますか。**

**A:** レコードは Azure Portal で 12 万個までダウンロードできます。 レコードは*最新の順*に並べ替えられており、既定では最新の 12 万個のレコードを取得します。 

---

**Q: アクティビティ API を通じてクエリできるレコードは何個までですか。**

**A:** レコードは 100 万個まで照会できます (レコードを最新の順に並べ替える top 演算子を使用しない場合)。 "top" 演算子を使用する場合、レコードは 50 万個までクエリできます。 API の使用方法に関するサンプル クエリは[こちら](active-directory-reporting-api-getting-started.md)で確認できます。

---

**Q: Premium ライセンスはどうすれば取得できますか。**

**A:** この質問の回答については、「[Azure Active Directory Premium の概要](active-directory-get-started-premium.md)」を参照してください。

---

**Q: Premium ライセンスを取得した後、どのぐらいの期間でアクティビティ データが表示されるようになりますか。**

**A:** 無料ライセンスでのアクティビティ データが既にある場合、同じデータが表示されます。 データが何もなかった場合は、1 日から 2 日かかります。

---

**Q: Azure AD Premium ライセンスを取得した後に、前月のデータを見ることはできますか。**

**A:**: 最近 Premium バージョン (試用版を含む) に切り替えた場合、初めは 7 日間分までのデータを見ることができます。 データが累積すると、30 日間分まで見られるようになります。

---

**Q: Identity Protection にリスク イベントがありますが、すべてのサインインの中に対応するサインインを確認できません。これは期待される動作ですか?**

**A:** はい、Identity Protection では、対話型または非対話型にかかわらず、すべての認証フローについてリスクを評価します。 それに対して、すべてのサインインのレポートには、対話型のサインインだけが表示されます。

---

**Q: Azure Portal で "リスクのフラグ付きユーザー" レポートはどうすればダウンロードできますか。**

**A:** *リスクのフラグ付きユーザー* レポートをダウンロードするオプションは、近日中に追加されます。

---

**Q: Azure Portal でサインインまたはユーザーにリスクのフラグが付けられた理由はどうすれば調べることができますか。**

**A:** Premium エディションのお客様は、[リスクのフラグ付きユーザー] に含まれるユーザーをクリックするか、[危険なサインイン] をクリックすれば、基になるリスク イベントについて確認できます。 無料および Basic エディションのお客様には、基になるリスク イベント情報なしで、リスクのあるユーザーとサインインが表示されます。

---

**Q: サインインとリスクの高いサインイン レポートでは、IP アドレスはどのように計算されていますか。**

**A:** IP アドレスは、IP アドレスとそのアドレスを持つコンピューターの物理的な配置場所との間に明確な関連性がないような方法で発行されます。 これは、クライアント デバイスの実際の使用場所から遠く離れたところにある中央プールから IP アドレスを発行するモバイル プロバイダーや VPN などの要因によって複雑になります。 これにより、IP アドレスの物理的な場所の変換は、トレース、レジストリ データ、逆引き参照、およびその他の情報に基づくベスト エフォートで対応されています。 

---
