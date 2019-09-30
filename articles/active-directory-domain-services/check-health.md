---
title: Azure Active Directory Domain Services の正常性を確認する | Microsoft Docs
description: Azure portal を使用して、Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインの正常性を確認し、ステータス メッセージについて理解する方法を学習します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: iainfou
ms.openlocfilehash: 50b142acb457d16abeb24f22d56b653a38aca76d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898262"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services マネージド ドメインの正常性を確認する

Azure Active Directory Domain Services (Azure AD DS) では、いくつかのバックグラウンド タスクを実行して、マネージド ドメインを正常かつ最新の状態に保ちます。 これらのタスクには、バックアップの取得、セキュリティ更新プログラムの適用、および Azure AD からのデータの同期が含まれます。 Azure AD DS マネージド ドメインに問題がある場合、これらのタスクは正常に実行されない可能性があります。 問題を確認して解決するために、Azure portal を使用して、Azure AD DS マネージド ドメインの正常性状態を確認できます。

この記事では、Azure AD DS の正常性状態を表示し、表示されている情報またはアラートを理解する方法を示します。

## <a name="view-the-health-status"></a>正常性状態を表示する

Azure AD DS マネージド ドメインの正常性状態は、Azure portal を使用して表示されます。 最後のバックアップ時刻と Azure AD との同期に関する情報が、マネージド ドメインの正常性に関する問題を示すアラートと共に表示されます。 Azure AD DS マネージド ドメインの正常性状態を表示するには、次の手順を完了します。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。
1. Azure AD DS マネージド ドメイン (*contoso.com* など) を選択します。
1. Azure AD DS リソース ウィンドウの左側で、 **[正常性]** を選択します。 次のスクリーンショット例は、正常な Azure AD DS マネージド ドメインと、最後のバックアップおよび Azure AD 同期の状態を示しています。

    ![Azure Active Directory Domain Services の状態を示す、Azure portal の正常性ページの概要](./media/check-health/health-page.png)

正常性ページの "*最終評価日時*" タイムスタンプは、Azure AD DS マネージド ドメインが最後に確認された日時を示します。 Azure AD DS マネージド ドメインの正常性は、1 時間ごとに評価されます。 Azure AD DS マネージド ドメインに変更を加えた場合は、次の評価サイクルで更新された正常性状態が表示されるまで待ちます。

右上の状態は、Azure AD DS マネージド ドメインの全体的な正常性を示しています。 この状態には、ドメイン上の既存のアラートがすべて反映されます。 次の表で、使用可能な状態インジケーターについて詳しく説明します。

| Status | アイコン | 説明 |
| --- | :----: | --- |
| 実行中 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Azure AD DS マネージド ドメインは正しく実行されており、重大なアラートや警告アラートはありません。 ドメインでは、情報アラートが発生している場合があります。 |
| 要注意 (警告) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Azure AD DS マネージド ドメインに重大なアラートはありませんが、対処する必要がある 1 つ以上の警告アラートがあります。 |
| 要注意 (重大) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Azure AD DS マネージド ドメインには、対処する必要がある重大なアラートが 1 つ以上あります。 警告や情報アラートが発生している場合もあります。 |
| デプロイ中 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | Azure AD DS ドメインがデプロイされています。 |

## <a name="understand-monitors-and-alerts"></a>モニターとアラートについて

Azure AD DS マネージド ドメインの正常性状態では、モニターとアラートという 2 種類の情報が示されます。 モニターでは、主要なバックグラウンド タスクが完了した時刻が示されます。 アラートでは、マネージド ドメインの安定性を向上させるための情報や提案が提供されます。

### <a name="monitors"></a>モニター

モニターは、定期的に確認される Azure AD DS マネージド ドメインの領域です。 Azure AD DS マネージド ドメインのアクティブなアラートがある場合は、いずれかのモニターによって問題が報告されることがあります。 Azure AD Domain Services では現在、以下の領域が監視されます。

* バックアップ
* Azure AD での同期

#### <a name="backup-monitor"></a>バックアップ モニター

バックアップ モニターでは、Azure AD DS マネージド ドメインの定期的な自動バックアップが正常に実行されていることを確認します。 次の表で、使用可能なバックアップ モニターの状態について詳しく説明します。

| 詳細の値 | 説明 |
| --- | --- |
| バックアップされていません | 新しい Azure AD DS マネージド ドメインの場合、この状態は正常です。 最初のバックアップは、Azure AD DS マネージド ドメインがデプロイされてから 24 時間後に作成されるはずです。 この状態が続く場合は、[Azure サポート リクエストを開きます][azure-support]。 |
| 1 ～ 14 日前に最後のバックアップが行われました | この時間範囲は、バックアップ モニターの予期される状態です。 定期的な自動バックアップは、この期間内に行われるはずです。 |
| 最後のバックアップは 14 日より以前に行われました。 | 期間が 2 週間を超えている場合は、定期的な自動バックアップに問題があることを示しています。 重大なアクティブ アラートがあると、Azure AD DS マネージド ドメインがバックアップされない場合があります。 Azure AD DS マネージド ドメインのアクティブなアラートをすべて解決します。 その後、バックアップ モニターで最新のバックアップを報告するために状態が更新されない場合は、[Azure サポート リクエストを開きます][azure-support]。 |

#### <a name="synchronization-with-azure-ad-monitor"></a>Azure AD での同期モニター

Azure AD DS マネージド ドメインは、Azure Active Directory と定期的に同期されます。 ユーザーとグループ オブジェクトの数、および前回の同期以降に Azure AD ディレクトリに加えられた変更の数は、同期にかかる時間に影響します。 Azure AD DS マネージド ドメインが最後に同期されたのが 3 日以上前の場合は、アクティブなアラートを確認して解決します。 その後、バックアップ モニターで最新の同期を表示するために状態が更新されない場合は、[Azure サポート リクエストを開きます][azure-support]。

### <a name="alerts"></a>アラート

サービスを正しく実行するために対処する必要がある、Azure AD DS マネージド ドメインでの問題に対してアラートが生成されます。 アラートごとに問題の説明と、問題を解決するための具体的な手順を概説する URL が示されます。 考えられるアラートとその解決策の詳細については、[アラートのトラブルシューティング](troubleshoot-alerts.md)に関するページを参照してください。

正常性状態のアラートは、次の重大度レベルに分類されます。

 * **重大なアラート**は、Azure AD DS マネージド ドメインに深刻な影響を与える問題です。 これらのアラートは直ちに対処する必要があります。 Azure プラットフォームでは、問題が解決されるまで、マネージド ドメインの監視、管理、修正プログラムの適用、および同期を行うことはできません。
 * **警告アラート**では、問題が解決しない場合に Azure AD DS マネージド ドメインの操作に影響する可能性がある問題を通知します。 これらのアラートでは、マネージド ドメインをセキュリティで保護するための推奨事項も提供されます。
 * **情報アラート**は、Azure AD DS マネージド ドメインに悪影響を与えない通知です。 情報アラートで、マネージド ドメインで何が起こっているかについて洞察できます。

## <a name="next-steps"></a>次の手順

正常性状態ページに表示されるアラートの詳細については、[マネージド ドメインのアラートの解決][troubleshoot-alerts]に関するページを参照してください

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
