---
title: Threat Modeling Tool のリリース - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool のリリース ノートです
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: c96b924294286be57de90dae7e6534b5ed9306ea
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57874574"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool 更新プログラム リリース 7.1.60126.1 - 1/29/2019

バージョン 7.1.60126.1 の Microsoft Threat Modeling Tool が 2019 年 1 月 29 日にリリースされました。変更点は以下のとおりです。

- .NET の最低限必要なバージョンが [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) に上がりました。
- .NET の依存関係のため、Windows の最低限必要なバージョンが [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) に上がりました。
- ツールの [オプション] メニューにモデル検証の切り替え機能が追加されました。
- 脅威のプロパティ内のいくつかのリンクが更新されました。
- ツールのホーム画面の UX にいくつかの小さな変更が加えられました。
- Threat Modeling Tool は、ホスト オペレーティング システムの TLS 設定を継承するようになり、TLS 1.2 以降を必要とする環境でサポートされるようになりました。

## <a name="feature-changes"></a>機能の変更

### <a name="model-validation-option"></a>モデルの検証オプション

お客様からのフィードバックに基づいて、モデルの検証を有効または無効にするオプションがツールに追加されました。 以前は、テンプレートで 2 つのオブジェクト間に単一の一方向データ フローが使用されていると、メッセージ フレームに "ObjectsName には少なくとも 1 つの 'Any' が必要です。" のようなエラー メッセージが表示されることがありました。 モデルの検証を無効にすると、これらの警告がビューに表示されなくなります。

モデルの検証のオンとオフを切り替えるオプションは、[ファイル]-> [設定] -> [オプション] メニューにあります。 この設定の既定値は [無効] です。

![モデルの検証オプション](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>システム要件

- サポートされているオペレーティング システム
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 以降
- 必要な .NET のバージョン
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 以降
- その他の要件
  - ツールとテンプレートの更新プログラムを受け取るには、インターネット接続が必要です。

## <a name="known-issues"></a>既知の問題

### <a name="unsupported-systems"></a>サポートされていないシステム

#### <a name="issue"></a>問題

Windows 10 Enterprise LTSB (バージョン 1507) など、.NET 4.7.1 以降をインストールできない Windows 10 システムのユーザーは、アップグレード後にツールを開くことができません。 これらの古いバージョンの Windows は、Threat Modeling Tool のサポート対象プラットフォームではなくなりました。最新の更新プログラムをインストールしないでください。

#### <a name="workaround"></a>対処法

最新の更新プログラムをインストールした Windows 10 Enterprise LTSB (バージョン 1507) のユーザーは、[アプリと機能] のアンインストール ダイアログを使用して、Threat Modeling Tool を以前のバージョンに戻すことができます。

## <a name="documentation-and-feedback"></a>ドキュメントとフィードバック

- Threat Modeling Tool のドキュメントは [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) にあり、[ツールの使用について](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)の情報が含まれます。

## <a name="next-steps"></a>次の手順

最新バージョンの [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool) をダウンロードしてください。
