---
title: Microsoft Threat Modeling Tool のリリース 2020 年 3 月 22 日 - Azure
description: Threat Modeling Tool リリース 7.3.00316.1 のリリース ノートです。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: e182d40d20529b5743fa9105c68108a8ae55d943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516902"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool 更新プログラム リリース 7.3.00316.1 - 2020 年 3 月 22 日

バージョン 7.3.00316.1 の Microsoft Threat Modeling Tool (TMT) が 2020 年 3 月 22 日にリリースされました。変更点は以下のとおりです。

- アクセシビリティの向上
- バグの修正
- 新しい DiagramReader 機能

## <a name="notable-bug-fixes"></a>注目すべきバグの修正

### <a name="exporting-the-threat-list-to-csv"></a>脅威リストの CSV へのエクスポート

CSV へのエクスポート機能では、脅威リストからエクスポートするフィールドの選択に一貫性がありませんでした。 現在は、脅威リストのすべてのフィールドが CSV ファイルにエクスポートされます。 

### <a name="ux-bugs"></a>UX バグ

- プライマリ ワークフロー (作成、オープン、分析) とテンプレート エディター エクスペリエンスのヘルプ メニューで、一貫したメニュー オプションが使用できるようになりました。
- ステンシル ペインの検索バーに標準カーソルが追加され、適切なラベルが追加されました。

## <a name="new-features"></a>新機能

### <a name="diagramreader-feature-has-been-added"></a>DiagramReader 機能が追加されました

モデルが開いている間のメイン メニューに新しい DiagramReader 機能が追加されました。 この機能により、モデルのグラフィック表示がテキストベースのナレーションに変換されます。 

## <a name="system-requirements"></a>システム要件

- サポートされているオペレーティング システム:
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 以降
- 必要な .NET のバージョン:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 以降
- その他の要件:
  - ツールとテンプレートの更新プログラムを受け取るためのインターネット接続

## <a name="documentation-and-feedback"></a>ドキュメントとフィードバック

- Threat Modeling Tool のドキュメントは [docs.microsoft.com](./threat-modeling-tool.md) にあり、[ツールの使用について](./threat-modeling-tool-getting-started.md)の情報が含まれます。

## <a name="next-steps"></a>次のステップ

最新バージョンの [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool) をダウンロードしてください。