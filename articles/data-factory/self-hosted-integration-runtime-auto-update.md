---
title: セルフホステッド統合ランタイムの自動更新と期限切れ通知
description: セルフホステッド統合ランタイムの自動更新と期限切れ通知について説明します。
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376260"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>セルフホステッド統合ランタイムの自動更新と期限切れ通知

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、セルフホステッド統合ランタイムを最新バージョンに自動更新する方法と、ADF でセルフホステッド統合ランタイムのバージョンが管理される方法について説明します。

## <a name="self-hosted-integration-runtime-auto-update"></a>セルフホステッド統合ランタイムの自動更新
一般に、ローカル コンピューターまたは Azure VM にセルフホステッド統合ランタイムをインストールする場合、セルフホステッド統合ランタイムのバージョンを管理するためのオプションには自動更新と手動での管理の 2 つがあります。 通常、ADF では毎月、新機能のリリース、バグ修正、または機能強化を含むセルフホステッド統合ランタイムの 2 つの新しいバージョンをリリースします。 そのため、最新の機能と機能強化を入手するために、最新バージョンに更新することをお勧めします。

最も便利な方法は、セルフホステッド統合ランタイムを作成または編集するときに自動更新を有効にすることです。 それにより、最新バージョンに自動的に更新されるようになります。 また、希望する最も適した時間スロットに更新をスケジュールすることもできます。

![自動更新を有効にする](media/create-self-hosted-integration-runtime/shir-auto-update.png)

セルフホステッド統合ランタイム クライアントで最終更新の日付と時刻を確認できます。

![更新時刻の確認のスクリーンショット](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> セルフホステッド統合ランタイムの安定性を確保するために、2 つのバージョンをリリースしても、それが自動的に更新されるのは毎月 1 回だけです。 そのため、自動更新されたバージョンが、実際の最新バージョンの以前のバージョンであることに気付く場合があります。 最新バージョンを取得する場合は、[ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)に移動できます。

## <a name="self-hosted-integration-runtime-expire-notification"></a>セルフホステッド統合ランタイムの期限切れ通知
セルフホステッド統合ランタイムのバージョンを手動で制御する場合は、自動更新の設定を無効にして、手動でインストールすることができます。 セルフホステッド統合ランタイムの各バージョンは 1 年で有効期限が切れます。 有効期限切れのメッセージは、有効期限の **90 日** 前に ADF ポータルとセルフホステッド統合ランタイム クライアントに表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory の統合ランタイムの概念](./concepts-integration-runtime.md)を確認します。

- [Azure portal 上でセルフホステッド統合ランタイムを作成する](./create-self-hosted-integration-runtime.md)方法を確認します。
