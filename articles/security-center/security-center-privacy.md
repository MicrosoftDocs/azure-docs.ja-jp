---
title: Azure Security Center でのユーザー データの管理 | Microsoft Docs
description: Azure Security Center でユーザー データを管理する方法について説明します。 ユーザー データの管理には、データへのアクセス、削除、またはエクスポート機能が含まれます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 5b5c78ffec736f29a481aa95426ff663199613b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595651"
---
# <a name="manage-user-data-in-azure-security-center"></a>Azure Security Center でのユーザー データの管理
この記事では、Azure Security Center でユーザー データを管理する方法について説明します。 ユーザー データの管理には、データへのアクセス、削除、またはエクスポート機能が含まれます。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Azure Security Center のユーザーは、ツール内の顧客データにアクセスできます。 アカウント管理者ロールの詳細については、「[Azure ロールベースのアクセス制御の組み込みロール](../role-based-access-control/built-in-roles.md)」を参照して閲覧者、所有者、共同作成者ロールを参照してください。 「[Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)」に関する記事を参照してください。

## <a name="searching-for-and-identifying-personal-data"></a>個人データの検索および特定
Security Center のユーザーは、Azure portal から自分の個人データを表示できます。 Security Center では、メール アドレスや電話番号などのセキュリティ連絡先の詳細情報のみを保存します。 詳細については、「[Azure Security Center でセキュリティ連絡先の詳細情報を指定する](security-center-provide-security-contact-details.md)」を参照してください。

Azure portal で、ユーザーは、Security Center の Just In Time VM アクセス機能を使用して、許可されている IP 構成を表示できます。 詳細については、「[Just-In-Time を使用した仮想マシン アクセスの管理](security-center-just-in-time.md)」を参照してください。

Azure portal で、ユーザーは、IP アドレスや攻撃者の詳細など、Security Center から提供されるセキュリティ アラートを表示できます。 詳細については、「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」を参照してください。

## <a name="classifying-personal-data"></a>個人データの分類
Security Center のセキュリティ連絡先機能にある個人データを分類する必要はありません。 保存されるデータは、1 つまたは複数のメール アドレスと電話番号です。 [連絡先データ](security-center-provide-security-contact-details.md)は、Security Center によって検証されます。

Security Center の [Just In Time](security-center-just-in-time.md) 機能で保存された IP アドレスとポート番号を分類する必要はありません。

管理者のロールを割り当てられたユーザーのみが、Security Center で[アラートを表示](security-center-managing-and-responding-alerts.md)して、個人データを分類できます。

## <a name="securing-and-controlling-access-to-personal-data"></a>個人データのセキュリティ保護とアクセス制御
閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Security Center のユーザーは、[セキュリティ連絡先データ](security-center-provide-security-contact-details.md)にアクセスできます。

閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Security Center のユーザーは、自分の [Just In Time](security-center-just-in-time.md) ポリシーにアクセスすることができます。

閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Security Center のユーザーは、自分の[アラート](security-center-managing-and-responding-alerts.md)を表示できます。

## <a name="updating-personal-data"></a>個人データの更新
所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Security Center のユーザーは、Azure portal を介して[セキュリティ連絡先データ](security-center-provide-security-contact-details.md)を更新できます。

所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Security Center のユーザーは、自分の [Just In Time ポリシー](security-center-just-in-time.md)を更新することができます。

アカウント管理者は、アラート インシデントを編集できません。 [アラート インシデント](security-center-managing-and-responding-alerts.md)はセキュリティ データと見なされ、読み取り専用です。

## <a name="deleting-personal-data"></a>個人データの削除
所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Security Center のユーザーは、Azure portal を介して[セキュリティ連絡先データ](security-center-provide-security-contact-details.md)を削除できます。

所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Security Center のユーザーは、Azure portal を介して [Just In Time ポリシー](security-center-just-in-time.md)を削除できます。

Security Center のユーザーは、アラート インシデントを削除できません。 セキュリティ上の理由により、[アラート インシデント](security-center-managing-and-responding-alerts.md)は読み取り専用データと見なされます。

## <a name="exporting-personal-data"></a>個人データのエクスポート
閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Security Center のユーザーは、次の方法で[セキュリティ連絡先データ](security-center-provide-security-contact-details.md)をエクスポートできます。

- Azure portal からコピーする
- Azure REST API 呼び出しの GET HTTP: を実行する
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

アカウント管理者のロールが割り当てられている Security Center のユーザーは、IP アドレスを含む [Just In Time ポリシー](security-center-just-in-time.md)をエクスポートできます。

- Azure portal からコピーする
- Azure REST API 呼び出しの GET HTTP: を実行する
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

アカウント管理者は、次の方法でアラートの詳細をエクスポートできます。

- Azure portal からコピーする
- Azure REST API 呼び出しの GET HTTP: を実行する
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

詳細については、[セキュリティ アラートの取得 (GET コレクション)](/previous-versions/azure/reference/mt704050(v=azure.100)) に関するページを参照してください。

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>同意のないプロファイルまたはマーケティングのための個人データの使用の制限
Security Center のユーザーは、自分の[セキュリティ連絡先データ](security-center-provide-security-contact-details.md)を削除することによって、オプトアウトすることを選択できます。

[Just In Time データ](security-center-just-in-time.md)は、識別不可能なデータと見なされ、30 日間保持されます。

[アラート データ](security-center-managing-and-responding-alerts.md)は、セキュリティ データと見なされ、2 年間保持されます。

## <a name="auditing-and-reporting"></a>監査とレポート
セキュリティ連絡先、Just In Time、およびアラートの更新の監査ログは、[Azure の活動ログ](../azure-monitor/essentials/platform-logs-overview.md)で管理されます。