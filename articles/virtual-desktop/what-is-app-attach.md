---
title: Windows Virtual Desktop の MSIX アプリのアタッチの概要 - Azure
description: MSIX アプリのアタッチとは この記事をご覧ください。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88556170"
---
# <a name="what-is-msix-app-attach"></a>MSIX アプリのアタッチとは

MSIX は、すべての Windows アプリのパッケージ化エクスペリエンスを向上させることを目的とした多くの機能を提供する新しいパッケージ形式です。 MSIX の詳細については、[MSIX の概要](/windows/msix/overview)に関する記事を参照してください。

MSIX アプリのアタッチは、物理マシンと仮想マシンの両方に MSIX アプリケーションを配信する方法です。 ただし、MSIX アプリのアタッチは、通常の MSIX とは異なります。これは、特に Windows Virtual Desktop に対して行われるためです。 この記事では、MSIX アプリのアタッチとその効果について説明します。

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Windows Virtual Desktop でのアプリケーションの配信オプション

Windows Virtual Desktop でアプリを配信するには、次のいずれかの方法を使用します。

- アプリをマスター イメージに配置する
- SCCM や Intune などのツールを中央管理に使用する
- 動的なアプリのプロビジョニング (AppV、VMWare AppVolumes、または Citrix AppLayering)
- Microsoft とサードパーティのツールを使用してカスタム ツールまたはスクリプトを作成する

## <a name="what-does-msix-app-attach-do"></a>MSIX アプリのアタッチの効果

Windows Virtual Desktop のデプロイでは、MSIX アプリのアタッチにより次のことが可能になります。

- [MSIX コンテナー](/windows/msix/msix-container)を使用して、ユーザー データ、OS、アプリを分離する。
- アプリケーションを動的に配信するときに再パッケージ化する必要がなくなる。
- ユーザーがサインインするまでにかかる時間を短縮する。
- インフラストラクチャの要件とコストを削減する。

MSIX アプリのアタッチは、VDI または SBC の外部で適用される必要があります。

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>従来のアプリのレイヤー化と MSIX アプリのアタッチの比較

次の表は、MSIX アプリのアタッチとアプリのレイヤー化の主な機能を比較したものです。

| 機能 | 従来のアプリのレイヤー化  | MSIX アプリのアタッチ  |
|-----|-----------------------------|--------------------|
| Format               | アプリのレイヤー化テクノロジごとに独自の形式が必要になります。 | ネイティブ MSIX パッケージ形式で動作します。        |
| 再パッケージ化のオーバーヘッド | 独自の形式では、更新ごとにシーケンス処理と再パッケージ化が必要です。         | MSIX として発行されたアプリには、再パッケージ化は必要ありません。 ただし、MSIX パッケージが使用できない場合は、再パッケージ化のオーバーヘッドが発生します。 |
| エコシステム            | 該当なし (例: ベンダーが App-V を出荷していない)  | MSIX は、主要な ISV パートナーや Office などのインハウス アプリが採用している、Microsoft のメインストリーム テクノロジです。 MSIX は、仮想デスクトップと物理 Windows コンピューターの両方で使用できます。 |
| インフラストラクチャ       | 追加のインフラストラクチャが必要 (サーバー、クライアントなど) | ストレージのみ   |
| 管理       | メンテナンスと更新が必要   | 簡略化されたアプリの更新 |
| ユーザー エクスペリエンス      | ユーザーのサインイン時間に影響します。 境界は、OS の状態、アプリの状態、ユーザー データの間に存在します。  | 配信されたアプリは、ローカルにインストールされたアプリケーションと見分けがつきません。 |

## <a name="next-steps"></a>次のステップ

MSIX アプリのアタッチの詳細については、[用語集](app-attach-glossary.md)と[よくあるご質問](app-attach-faq.md)に関する記事を参照してください。 それ以外の場合、[アプリのアタッチの設定](app-attach.md)を開始してください。
