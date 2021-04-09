---
title: Microsoft Threat Modeling Tool のリリース 2020 年 7 月 29 日 - Azure
description: Threat Modeling Tool リリース 7.3.00729.1 のリリース ノートです。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516375"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool 更新プログラム リリース 7.3.00729.1 - 2020 年 7 月 29 日

バージョン 7.3.00729.1 の Microsoft Threat Modeling Tool (TMT) が 2020 年 7 月 29 日にリリースされました。変更点は以下のとおりです。

- バグの修正
 
## <a name="known-issues"></a>既知の問題

### <a name="errors-related-to-tmt7application-file-deserialization"></a>TMT7.application ファイルの逆シリアル化に関連するエラー

#### <a name="issue"></a>問題

一部のお客様から、Threat Modeling Tool をダウンロードするときに、次のエラー メッセージが表示されるという報告を受けています。

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

このエラーは、一部のブラウザーで ClickOnce インストールがネイティブにサポートされていないために発生します。 そのような場合は、ClickOnce アプリケーション ファイルがユーザーのハード ドライブにダウンロードされます。

#### <a name="workaround"></a>回避策

このエラーは、TMT7.application ファイルをダブルクリックして Threat Modeling Tool を起動させた場合に引き続き表示されます。 ただし、このエラーをバイパスした後、ツールは正常に機能します。 TMT7.application ファイルをダブルクリックして Threat Modeling Tool を起動するのではなく、インストール中に Windows メニューで作成されたショートカットを使用して Threat Modeling Tool を開始する必要があります。

## <a name="system-requirements"></a>システム要件

- サポートされるオペレーティング システム
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 以降
- 必要な .NET のバージョン
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 以降
- その他の要件
  - ツールとテンプレートの更新プログラムを受け取るには、インターネット接続が必要です。

## <a name="documentation-and-feedback"></a>ドキュメントとフィードバック

- Threat Modeling Tool のドキュメントは [docs.microsoft.com](./threat-modeling-tool.md) にあり、[ツールの使用について](./threat-modeling-tool-getting-started.md)の情報が含まれます。

## <a name="next-steps"></a>次のステップ

最新バージョンの [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool) をダウンロードしてください。