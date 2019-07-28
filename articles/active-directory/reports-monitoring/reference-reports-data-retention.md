---
title: Azure AD にレポート データが保存される期間 | Microsoft Docs
description: Azure でさまざまな種類のレポート データが保存される期間について説明します。
services: active-directory
documentationcenter: ''
author: markusvi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41fa12c9d79d14a6602d995ed93b5d1a23be8a4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65781054"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Azure AD にレポート データが保存される期間

この記事では、Azure Active Directory のさまざまなアクティビティ レポートのデータ保持ポリシーについて説明します。 

### <a name="when-does-azure-ad-start-collecting-data"></a>Azure AD でデータ収集が開始されるタイミング

| Azure AD のエディション | コレクションの開始 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | サブスクリプションにサインアップしたとき |
| Azure AD Free <br /> Azure AD Basic | [Azure Active Directory ブレード](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)を初めて開いたとき、または [Reporting API](https://aka.ms/aadreports) を初めて使用したとき  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Azure portal でアクティビティ データを使用できるようになるタイミング

- **すぐに** - Azure Portal で既にレポートを操作している場合。
- **2 時間以内** - Azure Portal でレポートを有効にしていない場合。

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Premium ライセンスの取得後、アクティビティ データが表示されるまでにかかる時間

無料ライセンスでのアクティビティ データが既にある場合、アップグレード時にはすぐにそのデータを表示できます。 データが何もない場合は、Premium ライセンスへのアップグレード後、レポートにデータが表示されるまで 1 日から 2 日かかります。

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Azure AD Premium ライセンスの取得後、前月のデータを確認可能か

最近 Premium バージョン (試用版を含む) に切り替えた場合、初めは最大 7 日分のデータを確認できます。 データが蓄積されると、過去 30 日間のデータを確認できます。

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Azure AD でセキュリティ シグナル データが収集されるタイミング  

セキュリティ シグナルの場合、収集プロセスは **Identity Protection センター**を使用することを選択した時点から開始されます。 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Azure AD にデータが保存される期間

**アクティビティ レポート**    

| レポート                 | Azure AD Free | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| 監査ログ             | 7 日        |  7 日        | 30 日             | 30 日             |
| サインイン               | 該当なし           |  該当なし           | 30 日             | 30 日             |
| Azure MFA の使用状況        | 30 日       |  30 日       | 30 日             | 30 日             |

監査およびサインイン アクティビティ データは、Azure Monitor を使用して Azure ストレージ アカウントにルーティングすることで、上記で説明した既定の保有期間よりも長く保持することができます。 詳細については、「[Azure AD のログを Azure ストレージ アカウントにアーカイブする](quickstart-azure-monitor-route-logs-to-storage-account.md)」をご覧ください。

**セキュリティ シグナル**

| レポート         | Azure AD Free | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| リスクのあるユーザー  | 7 日        | 7 日         | 30 日             | 90 日間             |
| リスクの高いサインイン | 7 日        | 7 日         |  30 日            | 90 日間             |

---
