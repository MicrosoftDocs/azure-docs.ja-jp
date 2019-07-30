---
title: Azure AD Domain Services - マネージド ドメインの正常性を確認する | Microsoft Docs
description: Azure Portal の正常性ページを使用して、マネージド ドメインの正常性を確認します。
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 6b808126fe4366d3ca3cc19c674b489ec3055665
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234150"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインの正常性を確認する

## <a name="overview-of-the-health-page"></a>正常性ページの概要
Azure Portal の正常性ページを使用して、マネージド ドメインの最新の状態を常に確認できます。 この記事では、正常性ページの要素について説明します。

### <a name="how-to-view-the-health-of-your-managed-domain"></a>マネージド ドメインの正常性を確認する方法
1. Azure Portal で、[Azure AD Domain Services ページ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)に移動します。
2. 正常性を表示するドメインをクリックします。
3. 左側のナビゲーション ウィンドウで、 **[正常性]** をクリックします。

次の図は、正常性ページの例を示しています。![正常性ページの例](./media/active-directory-domain-services-alerts/health-page.png)

>[!NOTE]
> マネージド ドメインの正常性は 1 時間ごとに評価されます。 マネージド ドメインに変更を加えたら、次の評価サイクルで、マネージド ドメインの更新された正常性が表示されるまで待ちます。 右上隅にある "最終評価" タイムスタンプは、マネージド ドメインの正常性が最後に評価された日時を示しています。
>

### <a name="status-of-your-managed-domain"></a>マネージド ドメインの状態
正常性ページの右上のある状態は、マネージド ドメインの全体的な正常性を示しています。 この状態には、ドメイン上の既存のアラートすべてが反映されています。 Azure AD Domain Services の概要ページで、ドメインの状態を確認することもできます。

| Status | アイコン | 説明 |
| --- | :----: | --- |
| 実行中 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | マネージド ドメインはスムーズに実行されています。重大なアラートまたは警告アラートはありません。 このドメインでは、情報アラートが発生している場合があります。 |
| 要注意 (警告) | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | マネージド ドメインに重大なアラートはありませんが、対処する必要がある 1 つ以上の警告アラートがあります。 |
| 要注意 (重大) | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | マネージド ドメインに 1 つ以上の重大なアラートがあります。 警告/情報アラートが発生している場合もあります。 |
| デプロイ中 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | ドメインはデプロイ中です。 |

## <a name="monitors"></a>モニター
モニターは、Azure AD Domain Services が定期的に監視するマネージド ドメインの諸側面です。 モニターを正常な状態に保つには、マネージド ドメインのアクティブなアラートを解決するのが最も効果的です。

Azure AD Domain Services の現在の監視対象を次に示します。
 - バックアップ
 - Azure AD での同期

### <a name="the-backup-monitor"></a>"バックアップ" モニター
マネージド ドメインの定期的なバックアップが実行されているかどうかを監視します。 次の表では、バックアップ モニターの詳細列に表示される情報について説明します。

| 詳細の値 | 説明 |
| --- | --- |
|"バックアップされていません" | 新しく作成されたマネージド ドメインの場合、この状態は正常です。 一般的に、最初のバックアップは、マネージド ドメインがプロビジョニングされてから 24 時間後に作成されます。 新しいマネージド ドメインが作成されていない場合、またはこの状態の継続時間が異常に長い場合は、[サポートにお問い合わせください](contact-us.md)。 |
| 1 ～ 14 日前に最後のバックアップが行われました | バックアップ モニターでは、通常、この値が表示されます。 |
| 最後のバックアップは 14 日より以前に行われました。 | 最後のバックアップからの時間が 2 週間を超える場合は、異常に長すぎます。 重大なアクティブ アラートがあると、マネージド ドメインが定期的にバックアップされない場合があります。 まず、マネージド ドメインのアクティブ アラートを解決します。問題が解決しない場合は、[サポートにお問い合わせください](contact-us.md)。 |


### <a name="the-synchronization-with-azure-ad-monitor"></a>"Azure AD での同期" モニター
Microsoft では、マネージド ドメインがどのくらいの頻度で Azure Active Directory と同期されているかを監視します。 オブジェクト (ユーザーとグループ) 数と、前回の同期から Azure AD で行われた変更数の両方が、同期の所要時間に影響を及ぼす可能性があります。 マネージド ドメインが最後に同期されたのが 3 日以上前の場合は、[サポートにお問い合わせください](contact-us.md)。

## <a name="alerts"></a>アラート
Azure AD Domain Services を実行するために対処する必要がある、マネージド ドメイン上の問題に対してアラートが生成されます。 アラートごとに問題の説明と URL が示されており、この URL で、問題を解決するための手順を確認できます。 すべてのアラートとその解決策を表示するには、[アラートのトラブルシューティング](troubleshoot-alerts.md)に関する記事をご覧ください。

### <a name="alert-severity"></a>アラートの重大度
アラートの重大度は、重大、警告、情報の 3 つのレベルに分類されます。

 * **重大なアラート**は、マネージド ドメインに深刻な影響を与える問題です。 このアラートには直ちに対処する必要があります。Microsoft は、マネージド ドメインに対する監視、管理、修正プログラムの適用、および同期を実行できません。 
 * **警告アラート**は、将来的にマネージド ドメインに影響を与える可能性のある問題を通知します。 このアラートは、マネージド ドメインをセキュリティで保護するための推奨事項を提供します。
 * **情報アラート**は通知であり、ドメインに悪影響を及ぼすことはありません。 情報アラートの目的は、ドメインおよび Azure AD Domain Services の状況に関する情報を提供することです。

## <a name="next-steps"></a>次の手順
- [マネージド ドメインのアラートを解決する](troubleshoot-alerts.md)
- [Azure AD Domain Services の詳細を確認する](overview.md)
- [製品チームに連絡する](contact-us.md)
