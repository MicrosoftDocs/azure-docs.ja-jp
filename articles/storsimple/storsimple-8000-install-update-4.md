---
title: StorSimple 8000 シリーズ デバイスへの Update 4 のインストール | Microsoft Docs
description: StorSimple 8000 シリーズのデバイスに StorSimple 8000 シリーズの Update 4 をインストールする方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860510"
---
# <a name="install-update-4-on-your-storsimple-device"></a>StorSimple デバイスへの Update 4 のインストール

## <a name="overview"></a>概要

このチュートリアルでは、Update 4 より前のソフトウェア バージョンを実行している StorSimple デバイスに、Azure Portal 経由で Update 4 をインストールする方法と、修正プログラムを使用してインストールする方法について説明します。 修正プログラムによる方法は、ゲートウェイが StorSimple デバイスの DATA 0 以外のネットワーク インターフェイスで構成されており、Update 1 より前のソフトウェア バージョンから更新する場合に使用します。

Update 4 には、デバイス ソフトウェア、USM ファームウェア、LSI ドライバーおよびファームウェア、Storport と Spaceport、OS セキュリティ更新プログラム、その他多くの OS 更新プログラムが含まれています。  デバイス ソフトウェア、USM ファームウェア、Spaceport、Storport、その他の OS 更新プログラムは、中断なしの更新プログラムです。 Azure Portal または修正プログラムによる方法を使用して、中断なしまたは通常の更新プログラムを適用できます。 ディスク ファームウェアの更新プログラムは中断を伴う更新プログラムであるため、デバイスの Windows PowerShell インターフェイスを使用して修正プログラムによる方法を介してのみ適用できます。

> [!IMPORTANT]
> * インストールの前に、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための手動と自動の一連の事前チェックが行われます。 これらの事前チェックは、Azure Portal から更新プログラムを適用する場合にのみ実行されます。
> * ソフトウェアとその他の通常の更新プログラムのインストールには Azure Portal を使用することをお勧めします。 ポータルで更新前のゲートウェイのチェックに失敗した場合のみ、(更新プログラムをインストールする) デバイスの Windows PowerShell インターフェイスに移動してください。 更新するバージョンによっては、更新プログラムのインストールに 4 時間 (またはそれ以上) かかる場合があります。 メンテナンス モードの更新プログラムも、デバイスの Windows PowerShell インターフェイスからインストールする必要があります。 メンテナンス モードの更新プログラムは中断を伴う更新プログラムであるため、デバイスにダウンタイムが発生します。
> * オプションの StorSimple Snapshot Manager を実行している場合は、デバイスを更新する前に Snapshot Manager のバージョンを Update 4 にアップグレードしたことを確認します。


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Azure Portal を使用して Update 4 をインストールする
デバイスを [Update 4](storsimple-update4-release-notes.md)に更新するには、次の手順を実行します。

> [!NOTE]
> Microsoft はデバイスから追加の診断情報を取得します。 その結果、Microsoft の運用チームが問題のあるデバイスを識別したときに、デバイスから情報を収集して問題を診断する能力が向上します。 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

デバイスで **StorSimple 8000 Series Update 4 (6.3.9600.17820)** が実行されていることを確認します。 **[最終更新日]** も変更されています。

* メンテナンス モードの更新プログラムを利用できることが示されます (このメッセージは、更新プログラムをインストールした後、最大 24 時間表示され続ける可能性があります)。 メンテナンス モードの更新プログラムは、デバイスのダウンタイムを発生させる更新プログラムであり、デバイスの Windows PowerShell インターフェイス経由でのみ適用できます。

* 「[修正プログラムをダウンロードするには](#to-download-hotfixes)」で示された手順を使用して KB4011837 を検索してメンテナンス モードの更新プログラムをダウンロードします。ディスク ファームウェアの更新プログラムがインストールされます (他の更新プログラムが既にインストールされている必要があります)。 メンテナンス モードの更新プログラムをインストールするには、「 [メンテナンス モードの修正プログラムをインストールして確認するには](#to-install-and-verify-maintenance-mode-hotfixes) 」に記載されている手順に従います。

## <a name="install-update-4-as-a-hotfix"></a>Update 4 を修正プログラムとしてインストールする
Update 4 のインストールにお勧めの方法は、Azure Portal を使用する方法です。

この手順は、Azure Portal から更新プログラムをインストールしようとしたときにゲートウェイのチェックに失敗した場合に使用してください。 このチェックは、DATA 0 以外のネットワーク インターフェイスに割り当てられているゲートウェイがある場合に、デバイスが Update 1 より前のソフトウェア バージョンを実行していると、失敗します。

修正プログラムによる方法でアップグレードできるソフトウェアのバージョンは、次のとおりです。

* Update 0.1、0.2、0.3
* Update 1、1.1、1.2
* Update 2、2.1、2.2
* Update 3、3.1


修正プログラムを使用する方法には、次の 3 つの手順が含まれます。

1. Microsoft Update カタログから修正プログラムをダウンロードします。
2. 通常モードの修正プログラムをインストールして確認します。
3. メンテナンス モードの修正プログラムをインストールして確認します。

#### <a name="download-updates-for-your-device"></a>デバイスの更新プログラムをダウンロードする

指定の順序と推奨されるフォルダーで、次の修正プログラムをダウンロードしてインストールする必要があります。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |インストールするフォルダー|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |ソフトウェア更新 |通常 <br></br>中断なし |～ 25 分 |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |LSI ドライバーおよびファームウェアの更新プログラム <br> USM ファームウェアの更新プログラム (バージョン 3.38) |通常 <br></br>中断なし |～ 3 時間 <br> (2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398、KB3108381 <br> KB3205400、KB3142030 <br> KB3197873、KB3197873 <br> KB3192392、KB3153704 <br> KB3174644、KB3139914  |OS のセキュリティ更新プログラム <br> Windows Server 2012 R2 のダウンロード |通常 <br></br>中断なし |- |SecondOrderUpdate|
| 2C. |KB3210083、KB3103616 <br> KB3146621、KB3121261 <br> KB3123538 |OS の更新プログラム パッケージ <br> Windows Server 2012 R2 のダウンロード |通常 <br></br>中断なし |- |SecondOrderUpdate|

前の表で示したすべての更新プログラムのほか、ディスク ファームウェアの更新プログラムのインストールが必要になることもあります。 `Get-HcsFirmwareVersion` コマンドレットを実行すると、ディスク ファームウェアの更新プログラムが必要かどうかを確認できます。 `XMGJ`、`XGEG`、`KZ50`、`F6C2`、`VR08`、`N002`、`0106` の各ファームウェア バージョンを実行している場合は、これらの更新プログラムをインストールする必要はありません。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 | インストールするフォルダー|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |ディスク ファームウェア |メンテナンス <br></br>中断あり |～ 30 分 | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * この手順は、Update 4 を適用するために 1 回だけ実行する必要があります。 以降の更新プログラムは、Azure Portal を使用して適用できます。
> * Update 3 または 3.1 から更新する場合、インストール時間は合計で 4 時間ほどです。
> * この手順を使用して更新プログラムを適用する前に、両方のデバイス コント ローラーがオンラインであり、すべてのハードウェア コンポーネントが正常な状態であることを確認します。

この修正プログラムをダウンロードしてインポートするには、次のステップを実行します。

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>次の手順
詳しくは、[Update 4 リリース](storsimple-update4-release-notes.md)に関するページをご覧ください。

