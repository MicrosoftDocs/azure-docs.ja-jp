---
title: Microsoft Threat Modeling Tool のリリース 2020 年 2 月 11 日 - Azure
description: Threat Modeling Tool のリリース ノートです
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: da4e61d6c89e62c3598570b30ce749390915ca1b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259340"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool 更新プログラム リリース 7.3.00206.1 - 2020 年 2 月 11 日

バージョン 7.3.00206.1 の Microsoft Threat Modeling Tool (TMT) が 2020 年 2 月 11 日にリリースされました。変更点は以下のとおりです。

- バグの修正

## <a name="notable-bug-fixes"></a>注目すべきバグの修正

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>予想される範囲外の優先順位の値に関連するエラー

一部のお客様から、"Threat Modeling Tool 2016" またはカスタム テンプレートで作成されたファイルを開くときに、次のエラー メッセージが表示されるという報告を受けています。

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

この問題は今回のリリースで解決されました。

## <a name="system-requirements"></a>システム要件

- サポートされるオペレーティング システム
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 以降
- 必要な .NET のバージョン
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 以降
- その他の要件
  - ツールとテンプレートの更新プログラムを受け取るには、インターネット接続が必要です。

## <a name="documentation-and-feedback"></a>ドキュメントとフィードバック

- Threat Modeling Tool のドキュメントは [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) にあり、[ツールの使用について](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)の情報が含まれます。

## <a name="next-steps"></a>次のステップ

最新バージョンの [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool) をダウンロードしてください。
