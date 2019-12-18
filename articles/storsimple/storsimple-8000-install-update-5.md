---
title: StorSimple 8000 シリーズ デバイスへの Update 5 のインストール | Microsoft Docs
description: StorSimple 8000 シリーズのデバイスに StorSimple 8000 シリーズの Update 5 をインストールする方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: d86e77ef0148c0fac3dfa31153364de153b094ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126751"
---
# <a name="install-update-5-on-your-storsimple-device"></a>StorSimple デバイスへの Update 5 のインストール

## <a name="overview"></a>概要

このチュートリアルでは、Update 5 より前のソフトウェア バージョンを実行している StorSimple デバイスに、Azure Portal 経由で Update 5 をインストールする方法と、修正プログラムを使用してインストールする方法について説明します。 修正プログラムによる方法は、Update 3 より前のバージョンを実行しているデバイスに Update 5 をインストールする場合に使用します。 修正プログラムによる方法は、ゲートウェイが StorSimple デバイスの DATA 0 以外のネットワーク インターフェイスで構成されており、Update 1 より前のソフトウェア バージョンから更新する場合にも使用されます。

Update 5 には、デバイス ソフトウェア、Storport と Spaceport、OS セキュリティ更新プログラム、OS 更新プログラム、ディスク ファームウェア更新プログラムが含まれています。  デバイス ソフトウェア、Spaceport、Storport、セキュリティ、その他の OS 更新プログラムは、中断を伴わない更新プログラムです。 Azure Portal または修正プログラムによる方法を使用して、中断なしまたは通常の更新プログラムを適用できます。 ディスク ファームウェアの更新プログラムは中断を伴うため、デバイスの Windows PowerShell インターフェイスを使用して、デバイスが修正プログラムによるメンテナンス モードである場合に適用できます。

> [!IMPORTANT]
> * Update 5 は必須の更新プログラムであり、すぐにインストールする必要があります。 詳細については、[Update 5 リリース ノート](storsimple-update5-release-notes.md)に関する記事を参照してください。
> * インストールの前に、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための手動と自動の一連の事前チェックが行われます。 これらの事前チェックは、Azure Portal から更新プログラムを適用する場合にのみ実行されます。
> * Update 3 より前のバージョンを実行しているデバイスを更新する場合は、修正プログラムによる方法を使用して更新プログラムをインストールすることを強くお勧めします。 何らかの問題が発生した場合、[サポート チケットを記録](storsimple-8000-contact-microsoft-support.md)してください。
> * ソフトウェアとその他の通常の更新プログラムのインストールには Azure Portal を使用することをお勧めします。 ポータルで更新前のゲートウェイのチェックに失敗した場合のみ、(更新プログラムをインストールする) デバイスの Windows PowerShell インターフェイスに移動してください。 更新するバージョンによっては、更新プログラムのインストールに 4 時間 (またはそれ以上) かかる場合があります。 メンテナンス モードの更新プログラムは、デバイスの Windows PowerShell インターフェイスからインストールする必要があります。 メンテナンス モードの更新プログラムは中断を伴う更新プログラムであるため、デバイスにダウンタイムが発生します。
> * オプションの StorSimple Snapshot Manager を実行している場合は、デバイスを更新する前に Snapshot Manager のバージョンを Update 5 にアップグレードしたことを確認します。


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Azure Portal を使用して Update 5 をインストールする
デバイスを [Update 5](storsimple-update5-release-notes.md) に更新するには、次の手順を実行します。

> [!NOTE]
> Microsoft はデバイスから追加の診断情報を取得します。 その結果、Microsoft の運用チームが問題のあるデバイスを識別したときに、デバイスから情報を収集して問題を診断する能力が向上します。

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

デバイスで **StorSimple 8000 Series Update 5 (6.3.9600.17845)** が実行されていることを確認します。 **[最終更新日]** が変更されています。

メンテナンス モードの更新プログラムを利用できることが示されます (このメッセージは、更新プログラムをインストールした後、最大 24 時間表示され続ける可能性があります)。 メンテナンス モードの更新プログラムをインストールする手順については、次のセクションで詳しく説明します。

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Update 5 を修正プログラムとしてインストールする

修正プログラムによる方法でアップグレードできるソフトウェアのバージョンは、次のとおりです。

* Update 0.1、0.2、0.3
* Update 1、1.1、1.2
* Update 2、2.1、2.2
* Update 3、3.1
* Update 4

> [!NOTE] 
> Update 3 以降のバージョンから更新する場合は、Azure Portal を使用して Update 5 をインストールする方法をお勧めします。 Update 3 より前のバージョンを実行しているデバイスを更新する場合は、この手順を使用します。 この手順は、Azure Portal から更新プログラムをインストールしようとしたときにゲートウェイのチェックに失敗した場合にも使用できます。 このチェックは、DATA 0 以外のネットワーク インターフェイスに割り当てられているゲートウェイがある場合に、デバイスが Update 1 より前のソフトウェア バージョンを実行していると、失敗します。

修正プログラムを使用する方法には、次の 3 つの手順が含まれます。

1. Microsoft Update カタログから修正プログラムをダウンロードします。
2. 通常モードの修正プログラムをインストールして確認します。
3. メンテナンス モードの修正プログラムをインストールして確認します。

#### <a name="download-updates-for-your-device"></a>デバイスの更新プログラムをダウンロードする

指定の順序と推奨されるフォルダーで、次の修正プログラムをダウンロードしてインストールする必要があります。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |インストールするフォルダー|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |ソフトウェア更新<br> _HcsSoftwareUpdate.exe_ と _CisMSDAgent.exe_ の両方をダウンロード |通常 <br></br>中断なし |～ 25 分 |FirstOrderUpdate|

Update 4 を実行するデバイスから更新する場合は、OS の累積的な更新プログラムを、2 番目の注文の更新プログラムとしてインストールするだけです。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |インストールするフォルダー|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS の累積的な更新プログラム パッケージ <br> Windows Server 2012 R2 バージョンのダウンロード |通常 <br></br>中断なし |- |SecondOrderUpdate|

Update 3 以前のバージョンを実行するデバイスからインストールする場合は、累積な更新プログラムだけでなく、次の更新プログラムをインストールします。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |インストールするフォルダー|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI ドライバーおよびファームウェアの更新プログラム <br> USM ファームウェアの更新プログラム (バージョン 3.38) |通常 <br></br>中断なし |～ 3 時間 <br> (2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS のセキュリティ更新プログラム <br> Windows Server 2012 R2 バージョンのダウンロード |通常 <br></br>中断なし |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS の更新プログラム パッケージ <br> Windows Server 2012 R2 バージョンのダウンロード |通常 <br></br>中断なし |- |SecondOrderUpdate|


前の表で示したすべての更新プログラムのほか、ディスク ファームウェアの更新プログラムのインストールが必要になることもあります。 `Get-HcsFirmwareVersion` コマンドレットを実行すると、ディスク ファームウェアの更新プログラムが必要かどうかを確認できます。 `XMGJ`、`XGEG`、`KZ50`、`F6C2`、`VR08`、`N003`、`0107` の各ファームウェア バージョンを実行している場合は、これらの更新プログラムをインストールする必要はありません。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 | インストールするフォルダー|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |ディスク ファームウェア |メンテナンス <br></br>中断あり |～ 30 分 | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Update 4 から更新する場合、インストール時間は合計で 4 時間ほどです。
> * この手順を使用して更新プログラムを適用する前に、両方のデバイス コント ローラーがオンラインであり、すべてのハードウェア コンポーネントが正常な状態であることを確認します。

この修正プログラムをダウンロードしてインポートするには、次のステップを実行します。

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>次の手順
詳しくは、[Update 5 リリース](storsimple-update5-release-notes.md)に関するページをご覧ください。

