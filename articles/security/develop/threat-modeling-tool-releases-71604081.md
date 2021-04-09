---
title: Microsoft Threat Modeling Tool のリリース 2019 年 4 月 9 日
titleSuffix: Azure
description: Threat Modeling Tool リリース 7.1.60408.1 のリリース ノートです。
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 69dd2c6cdba41779849b4eb6b889cde9b1d6e5c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913570"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool 更新プログラム リリース 7.1.60408.1 - 4/9/2019

バージョン 7.1.60408.1 の Microsoft Threat Modeling Tool (TMT) が 2019 年 4 月 9 日にリリースされました。変更点は以下のとおりです。

- Azure Key Vault と Azure Traffic Manager の新しいステンシル
- TMT バージョン番号をホーム画面上に表示
- サポート リンクの更新
- バグの修正

## <a name="feature-changes"></a>機能の変更

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure Key Vault と Azure Traffic Manager の新しいステンシル

![Azure Key Vault と Azure Traffic Manager のアイコンを示しているスクリーンショット。](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Azure のステンシル セットに Azure Key Vault と Azure Traffic Manager の新しいステンシルと脅威が追加されました。 Azure のステンシル セットに基づくモデルを開くと、ユーザーはモデルに関連付けられたテンプレートを更新するように求められます。 Azure のステンシル セットに基づくモデルの更新は、"ファイル" メニューの "テンプレートの適用" コマンドを使用し、最新の Azure Cloud Services.tb7 ファイルを再適用して手動で開始することもできます。

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT バージョン番号をホーム画面上に表示

クライアント バージョンの Threat Modeling Tool がアプリケーションのホーム画面に表示され、簡単にアクセスできるようになりました。

![Microsoft Threat Modeling Tool とクライアントのバージョン番号を示しているスクリーンショット。](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>サポート リンクの更新

ツール内のすべてのサポート リンクが更新され、ユーザーが MSDN フォーラムではなく [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) に転送されるようになりました。

## <a name="system-requirements"></a>システム要件

- サポートされるオペレーティング システム
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 以降
- 必要な .NET のバージョン
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 以降
- その他の要件
  - ツールとテンプレートの更新プログラムを受け取るには、インターネット接続が必要です。

## <a name="documentation-and-feedback"></a>ドキュメントとフィードバック

- Threat Modeling Tool のドキュメントは [docs.microsoft.com](threat-modeling-tool.md) にあり、[ツールの使用について](threat-modeling-tool-getting-started.md)の情報が含まれます。

## <a name="next-steps"></a>次のステップ

最新バージョンの [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool) をダウンロードしてください。
