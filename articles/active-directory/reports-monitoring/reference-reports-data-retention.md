---
title: Azure AD にレポート データが保存される期間 | Microsoft Docs
description: Azure でさまざまな種類のレポート データが保存される期間について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e240e8ec53ce007be1a858af7b6e41273ca8831
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97093640"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Azure AD にレポート データが保存される期間


この記事では、Azure Active Directory のさまざまなアクティビティ レポートのデータ保持ポリシーについて説明します。 

### <a name="when-does-azure-ad-start-collecting-data"></a>Azure AD でデータ収集が開始されるタイミング

| Azure AD のエディション | コレクションの開始 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | サブスクリプションにサインアップしたとき |
| Azure AD Free| [Azure Active Directory ブレード](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)を初めて開いたとき、または [Reporting API](./overview-reports.md) を初めて使用したとき  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Azure portal でアクティビティ データを使用できるようになるタイミング

- **すぐに** - Azure Portal で既にレポートを操作している場合。
- **2 時間以内** - Azure Portal でレポートを有効にしていない場合。

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Premium ライセンスの取得後、アクティビティ データが表示されるまでにかかる時間

無料ライセンスでのアクティビティ データが既にある場合、アップグレード時にはすぐにそのデータを表示できます。 データが何もない場合は、Premium ライセンスへのアップグレード後、レポートにデータが表示されるまでに最大 3 日かかります。

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Azure AD でセキュリティ シグナル データが収集されるタイミング  

セキュリティ シグナルの場合、収集プロセスは **Identity Protection センター** を使用することを選択した時点から開始されます。 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Azure AD にデータが保存される期間

**アクティビティ レポート**    

| レポート                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 監査ログ             | 7 日        | 30 日             | 30 日             |
| サインイン               | 7 日        | 30 日             | 30 日             |
| Azure AD MFA の使用状況        | 30 日       | 30 日             | 30 日             |

監査およびサインイン アクティビティ データは、Azure Monitor を使用して Azure ストレージ アカウントにルーティングすることで、上記で説明した既定の保有期間よりも長く保持することができます。 詳細については、「[Azure AD のログを Azure ストレージ アカウントにアーカイブする](quickstart-azure-monitor-route-logs-to-storage-account.md)」をご覧ください。

**セキュリティ シグナル**

| レポート         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| リスクのあるユーザー  | 7 日        | 30 日             | 90 日間             |
| リスクの高いサインイン | 7 日        | 30 日             | 90 日間             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Azure AD Premium ライセンスの取得後、前月のデータを確認可能か

**いいえ**、できません。 Azure では、無料版のアクティビティ データが最大 7 日間保存されます。 つまり、無料版から Premium バージョンに切り替えたときは、最大 7 日間のデータしか表示できなくなります。

---
