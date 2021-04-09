---
title: Microsoft Threat Modeling Tool のリリース 2019 年 10 月 16 日 - Azure
description: Threat Modeling Tool リリース 7.1.61015.1 のリリース ノートです。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 761ad45496e799a555b60480ff575b9d8f30e984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516987"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool 更新プログラム リリース 7.1.61015.1 - 2019 年 10 月 16 日

バージョン 7.1.61015.1 の Microsoft Threat Modeling Tool (TMT) が 2019 年 10 月 16 日にリリースされました。変更点は以下のとおりです。

- アクセシビリティの向上
- バグの修正
- Azure Logic Apps と Azure Data Explorer 用の新しいステンシル

## <a name="notable-bug-fixes"></a>注目すべきバグの修正

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>"Threat Modeling Tool 2016" で作成されたファイルとの下位互換性の向上

"Threat Modeling Tool 2016" で作成された脅威モデル ファイルを開く、または表示する動作に関連するいくつかのバグが修正されました。

## <a name="feature-enhancements"></a>機能強化

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Azure Logic Apps と Azure Data Explorer 用の新しいステンシル

Azure Logic Apps と Azure Data Explorer 用の新しいステンシルが、関連する脅威と軽減策と共に Azure ステンシルに追加されました。

![Azure Logic Apps と Azure Data Explorer のステンシル](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>既知の問題

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>予想される範囲外の優先順位の値に関連するエラー

一部のお客様から、"Threat Modeling Tool 2016" またはカスタム テンプレートで作成されたファイルを開くときに、次のエラー メッセージが表示されるという報告を受けています。

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

この問題は調査中です

## <a name="system-requirements"></a>システム要件

- サポートされるオペレーティング システム
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 以降
- 必要な .NET のバージョン
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 以降
- その他の要件
  - ツールとテンプレートの更新プログラムを受け取るには、インターネット接続が必要です。

## <a name="documentation-and-feedback"></a>ドキュメントとフィードバック

- Threat Modeling Tool のドキュメントは [docs.microsoft.com](./threat-modeling-tool.md) にあり、[ツールの使用について](./threat-modeling-tool-getting-started.md)の情報が含まれます。

## <a name="next-steps"></a>次のステップ

最新バージョンの [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool) をダウンロードしてください。