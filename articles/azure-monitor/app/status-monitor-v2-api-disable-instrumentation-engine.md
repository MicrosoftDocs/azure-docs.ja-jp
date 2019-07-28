---
title: 'Azure Status Monitor v2 API リファレンス: インストルメンテーション エンジンを無効にする |Microsoft Docs'
description: Status Monitor v2 API リファレンス。 Disable-InstrumentationEngine。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
services: application-insights
documentationcenter: .net
author: ms-timothymothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: b5ff218ce7bc7593d38f496b22d2a03402a00cdc
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514422"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine-v021-alpha"></a>Status Monitor v2 API:Disable-InstrumentationEngine (v0.2.1-alpha)

この記事では、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーであるコマンドレットについて説明します。

> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 一部の機能は、サポートされていなかったり、制限されていたりする場合があります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="description"></a>説明
いくつかのレジストリ キーを削除することで、インストルメンテーション エンジンを無効にします。
変更を有効にするために IIS を再起動してください。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限の PowerShell セッションが必要です。

## <a name="examples"></a>例

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>parameters 

### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを出力する場合はこのスイッチを使用します。

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>インストルメンテーション エンジンが正常に無効化された場合の出力例

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>次の手順

 Status Monitor v2 の活用:
 - ガイドを使用して、Status Monitor v2 の[トラブルシューティング](status-monitor-v2-troubleshoot.md)を行います。
