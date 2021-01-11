---
title: 'リファレンス: Data Science Virtual Machine イメージの提供終了'
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine に影響する提供終了の詳細
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001622"
---
# <a name="reference-retirements-of-dsvm-images"></a>リファレンス: DSVM イメージの提供終了

以下では、イメージの提供終了 (非推奨化) と、Azure Data Science Virtual Machine 上で予定されている提供終了に対処するための推奨事項について説明します。

## <a name="why-we-retire-dsvm-images"></a>DSVM イメージを提供終了する理由

現在 Data Science Virtual Machine には、次の 2 つのエディションがあります。

* Ubuntu
* Windows Server

これらのエディションの DSVM イメージは、Ubuntu 18.04 LTS など、特定のオペレーティング システムのバージョンに基づいて構築されています。 時間が経過すると、オペレーティング システムの "_バージョン_" のメンテナンス期間は終了することになり、また、データ サイエンス ツールによって古いバージョンのオペレーティング システムはサポートされなくなります。 最新のオペレーティング システムとデータ サイエンス ツールで DSVM イメージを最新の状態に保つために、Microsoft はより新しいバージョンのオペレーティング システムに基づいて新しい DSVM イメージをリリースします。

## <a name="how-we-retire-dsvm-images"></a>DSVM イメージを提供終了する方法

Microsoft は_提供終了に関するお知らせ_を発行し、予定されている DSVM イメージの提供終了について既存の DSVM ユーザーに (電子メールで) お知らせします。 提供終了に関するお知らせには、"_提供終了日_" (この日付を過ぎるとイメージが利用できなくなります) と、軽減策の推奨事項 (より新しい DSVM イメージへのアップグレード方法など) が詳述されます。

"_お知らせ_" の日に、マーケットプレース内のイメージを非表示にします。これにより、次のようになります。

1. 提供終了となる DSVM イメージを新しいユーザーが誤ってプロビジョニングすることが防止されます。
2. 既存のユーザーは提供終了日まで ARM デプロイを使用できます。

非表示にされたイメージには、_提供終了日_までオペレーティング システムの修正プログラムが届けられますが、データ サイエンス ツールおよびフレームワークに対する更新プログラムは届け __られなくなります__。 "_提供終了日_" には、イメージは ARM デプロイで利用できなくなります。

ご利用のサブスクリプションにプロビジョニングされている DSVM イメージは、提供終了日以降も引き続き動作します。 ただし、最新のオペレーティング システムとデータ サイエンス ツールを使用できるように、最新の DSVM イメージにアップグレードすることをお勧めします。

## <a name="impact"></a>影響

ご利用のサブスクリプション内の既存のプロビジョニングされた DSVM イメージは、提供終了日以降も引き続き動作します。 ただし、Azure portal または ARM テンプレートを使用してユーザーが自分の DSVM イメージをより新しいバージョンにアップグレードすることをお勧めします。

> [!WARNING]
> Virtual Machine Scale Sets を使用してプロビジョニングされているインベントリから削除された DSVM イメージは、提供終了日が過ぎると、スケールアップできなくなります。
>
> 新しい DSVM イメージの詳細で更新されていない ARM テンプレートは、提供終了日が過ぎると、デプロイできなくなります。

