---
title: Azure Active Directory Domain Services の正常性を確認する | Microsoft Docs
description: Azure portal を使用して、Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインの正常性を確認し、ステータス メッセージについて理解する方法を学習します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 92cc6157c590d3e05b4002e0f071c08e4ec23a16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620326"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services マネージド ドメインの正常性を確認する

Azure Active Directory Domain Services (Azure AD DS) では、いくつかのバックグラウンド タスクを実行して、マネージド ドメインを正常かつ最新の状態に保ちます。 これらのタスクには、バックアップの取得、セキュリティ更新プログラムの適用、および Azure AD からのデータの同期が含まれます。 Azure AD DS マネージド ドメインに問題がある場合、これらのタスクは正常に完了しない可能性があります。 問題を確認して解決するために、Azure portal を使用して、マネージド ドメインの正常性状態を確認できます。

この記事では、Azure AD DS の正常性状態を表示し、表示されている情報またはアラートを理解する方法を示します。

## <a name="view-the-health-status"></a>正常性状態を表示する

マネージド ドメインの正常性状態は、Azure portal を使用して表示されます。 最後のバックアップ時刻と Azure AD との同期に関する情報が、マネージド ドメインの正常性に関する問題を示すアラートと共に表示されます。 マネージド ドメインの正常性状態を表示するには、次の手順を実行します。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。
1. 目的のマネージド ドメインを選択します (例: *aaddscontoso.com*)。
1. Azure AD DS リソース ウィンドウの左側で、 **[正常性]** を選択します。 次のスクリーンショット例は、正常なマネージド ドメインと、最後のバックアップおよび Azure AD 同期の状態を示しています。

    ![Azure Active Directory Domain Services の状態を示す、Azure portal の正常性ページの概要](./media/check-health/health-page.png)

正常性ページの "*最終評価日時*" タイムスタンプは、マネージド ドメインが最後に確認された日時を示します。 マネージド ドメインの正常性は、1 時間ごとに評価されます。 マネージド ドメインに変更を加えた場合は、次の評価サイクルで更新された正常性状態が表示されるまで待ちます。

右上の状態は、マネージド ドメインの全体的な正常性を示しています。 この状態には、ドメイン上の既存のアラートがすべて反映されます。 次の表で、使用可能な状態インジケーターについて詳しく説明します。

| Status | アイコン | 説明 |
| --- | :----: | --- |
| 実行中 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | マネージド ドメインは正しく実行されており、重大なアラートや警告アラートはありません。 ドメインでは、情報アラートが発生している場合があります。 |
| 要注意 (警告) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | マネージド ドメインに重大なアラートはありませんが、対処する必要がある 1 つ以上の警告アラートがあります。 |
| 要注意 (重大) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | マネージド ドメインには、対処する必要がある重大なアラートが 1 つ以上あります。 警告や情報アラートが発生している場合もあります。 |
| デプロイ中 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | マネージド ドメインはデプロイ中です。 |

## <a name="understand-monitors-and-alerts"></a>モニターとアラートについて

マネージド ドメインの正常性状態では、"*モニター*" と "*アラート*" という 2 種類の情報が示されます。 モニターでは、主要なバックグラウンド タスクが完了した時刻が示されます。 アラートでは、マネージド ドメインの安定性を向上させるための情報や提案が提供されます。

### <a name="monitors"></a>モニター

モニターは、定期的に確認されるマネージド ドメインの領域です。 マネージド ドメインのアクティブなアラートがある場合は、いずれかのモニターによって問題が報告されることがあります。 Azure AD DS には現在、次の領域のモニターがあります。

* バックアップ
* Azure AD での同期

#### <a name="backup-monitor"></a>バックアップ モニター

バックアップ モニターは、マネージド ドメインの定期的な自動バックアップが正常に実行されていることを確認します。 次の表で、使用可能なバックアップ モニターの状態について詳しく説明します。

| 詳細の値 | 説明 |
| --- | --- |
| バックアップされていません | 新しいマネージド ドメインの場合、この状態は正常です。 最初のバックアップは、マネージド ドメインがデプロイされてから 24 時間後に作成されるはずです。 この状態が続く場合は、[Azure サポート リクエストを開きます][azure-support]。 |
| 1 ～ 14 日前に最後のバックアップが行われました | この時間範囲は、バックアップ モニターの予期される状態です。 定期的な自動バックアップは、この期間内に行われるはずです。 |
| 最後のバックアップは 14 日より以前に行われました。 | 期間が 2 週間を超えている場合は、定期的な自動バックアップに問題があることを示しています。 重大なアクティブ アラートがあると、マネージド ドメインがバックアップされない場合があります。 マネージド ドメインのアクティブなアラートをすべて解決します。 その後、バックアップ モニターで最新のバックアップを報告するために状態が更新されない場合は、[Azure サポート リクエストを開きます][azure-support]。 |

#### <a name="synchronization-with-azure-ad-monitor"></a>Azure AD での同期モニター

マネージド ドメインは、Azure Active Directory と定期的に同期されます。 ユーザーとグループ オブジェクトの数、および前回の同期以降に Azure AD ディレクトリに加えられた変更の数は、同期にかかる時間に影響します。 マネージド ドメインが最後に同期されたのが 3 日以上前の場合は、アクティブなアラートを確認して解決します。 アクティブなアラートに対処した後、同期モニターで最新の同期を表示するために状態が更新されない場合は、[Azure サポート リクエストを開きます][azure-support]。

### <a name="alerts"></a>警告

サービスを正しく実行するために対処する必要がある、マネージド ドメインでの問題に対してアラートが生成されます。 アラートごとに問題の説明と、問題を解決するための具体的な手順を概説する URL が示されます。 考えられるアラートとその解決策の詳細については、[アラートのトラブルシューティング](troubleshoot-alerts.md)に関するページを参照してください。

正常性状態のアラートは、次の重大度レベルに分類されます。

 * **重大なアラート** は、マネージド ドメインに深刻な影響を与える問題です。 これらのアラートは直ちに対処する必要があります。 Azure プラットフォームでは、問題が解決されるまで、マネージド ドメインの監視、管理、修正プログラムの適用、および同期を行うことはできません。
 * **警告アラート** では、問題が解決しない場合にマネージド ドメインの操作に影響する可能性がある問題を通知します。 これらのアラートでは、マネージド ドメインをセキュリティで保護するための推奨事項も提供されます。
 * **情報アラート** は、マネージド ドメインに悪影響を与えない通知です。 情報アラートで、マネージド ドメインで何が起こっているかについて洞察できます。

## <a name="next-steps"></a>次のステップ

正常性状態ページに表示されるアラートの詳細については、[マネージド ドメインのアラートの解決][troubleshoot-alerts]に関するページを参照してください

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
