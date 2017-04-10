---
title: "StorSimple デバイスへの Update 3 のインストール | Microsoft Docs"
description: "StorSimple 8000 シリーズのデバイスに StorSimple 8000 シリーズの Update 3 をインストールする方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c6c4634d-4f3a-4bc4-b307-a22bf18664e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f48f9dff289c556aea1912ecf7b6415abb902f33
ms.openlocfilehash: 72b004a6c2604e0fc20b71b4b69217622f8f9ea0
ms.lasthandoff: 02/28/2017


---
# <a name="install-update-3-on-your-storsimple-8000-series-device"></a>StorSimple 8000 シリーズ デバイスに Update 3 をインストールする

## <a name="overview"></a>概要

このチュートリアルでは、Update 3 より前のソフトウェア バージョンを実行している StorSimple デバイスに、Azure クラシック ポータル経由で Update 3 をインストールする方法と、修正プログラムを使用してインストールする方法について説明します。 修正プログラムによる方法は、ゲートウェイが StorSimple デバイスの DATA 0 以外のネットワーク インターフェイスで構成されており、Update 1 より前のソフトウェア バージョンから更新する場合に使用します。

Update 3 には、デバイス ソフトウェア、LSI ドライバーとファームウェア、Storport および Spaceport 更新プログラムが含まれています。 Update 2 またはそれ以前のバージョンから更新する場合は、iSCSI と WMI、場合によってはディスク ファームウェアの更新プログラムも適用する必要があります。 デバイス ソフトウェア、WMI、iSCSI、LSI ドライバー、Spaceport、Storport の修正プログラムは中断を伴わない更新プログラムであるため、Azure クラシック ポータルを使用して適用できます。 ディスク ファームウェアの更新プログラムは中断を伴う更新プログラムであるため、デバイスの Windows PowerShell インターフェイスでのみ適用できます。 

> [!IMPORTANT]
> * インストールの前に、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための手動と自動の一連の事前チェックが行われます。 これらの事前チェックは、Azure クラシック ポータルから更新プログラムを適用する場合にのみ実行されます。
> * ソフトウェアとドライバーの更新プログラムのインストールには Azure クラシック ポータルを使用することをお勧めします。 ポータルで更新前のゲートウェイのチェックに失敗した場合のみ、(更新プログラムをインストールする) デバイスの Windows PowerShell インターフェイスに移動してください。 更新するバージョンによっては、更新プログラムのインストールに 1.5 ～ 2.5 時間かかる場合があります。 メンテナンス モードの更新プログラムは、デバイスの Windows PowerShell インターフェイスからインストールする必要があります。 メンテナンス モードの更新プログラムは中断を伴う更新プログラムであるため、デバイスにダウンタイムが発生します。
> * オプションの StorSimple Snapshot Manager を実行している場合は、デバイスを更新する前に Snapshot Manager のバージョンを Update 2 にアップグレードしたことを確認します。
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Azure クラシック ポータルを使用して Update 3 をインストールする
デバイスを [Update 3](storsimple-update3-release-notes.md)に更新するには、次の手順を実行します。

> [!NOTE]
> Update 2 以降 (Update 2.1 を含む) を適用する場合、Microsoft はデバイスから追加の診断情報を取得することができます。 その結果、Microsoft の運用チームが問題のあるデバイスを識別したときに、デバイスから情報を収集して問題を診断する能力が向上します。 Update 2 以降を受け入れると、このプロアクティブ サポートの提供に同意することになります。
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

デバイスで **StorSimple 8000 Series Update 3 (6.3.9600.17759)**が実行されていることを確認します。 **[最終更新日]** も変更されています。 
   - Update 2 より前のバージョンから更新する場合、メンテナンス モードの更新プログラムも利用できることが示されます (このメッセージは、更新プログラムをインストールした後、最大 24 時間表示され続ける可能性があります)。
     メンテナンス モードの更新プログラムは、デバイスのダウンタイムを発生させる更新プログラムであり、デバイスの Windows PowerShell インターフェイス経由でのみ適用できます。 Update 1.2 を実行している場合は、ディスクのファームウェアが既に最新になっている可能性があります。その場合、メンテナンス モードの更新プログラムをインストールする必要はありません。
   - Update 2 以降から更新する場合、デバイスは最新の状態です。 次の手順は省略できます。

「[修正プログラムをダウンロードするには](#to-download-hotfixes)」で示された手順を使用して KB3121899 を検索してメンテナンス モードの更新プログラムをダウンロードします。ディスク ファームウェアの更新プログラムがインストールされます (他の更新プログラムが既にインストールされている必要があります)。 メンテナンス モードの更新プログラムをインストールするには、「 [メンテナンス モードの修正プログラムをインストールして確認するには](#to-install-and-verify-maintenance-mode-hotfixes) 」に記載されている手順に従います。 

## <a name="install-update-3-as-a-hotfix"></a>Update 3 を修正プログラムとしてインストールする
この手順は、Azure クラシック ポータルから更新プログラムをインストールしようとしたときにゲートウェイのチェックに失敗した場合に使用してください。 このチェックは、DATA 0 以外のネットワーク インターフェイスに割り当てられているゲートウェイがある場合に、デバイスが Update 1 より前のソフトウェア バージョンを実行していると、失敗します。

修正プログラムによる方法でアップグレードできるソフトウェアのバージョンは、次のとおりです。

* Update 0.1、0.2、0.3
* Update 1、1.1、1.2
* Update 2、2.1、2.2 

> [!IMPORTANT]
> * デバイスがリリース (GA) バージョンを実行している場合、この更新プログラムの使用については、 [Microsoft サポート](storsimple-contact-microsoft-support.md) にお問い合わせください。
> 
> 

修正プログラムを使用する方法には、次の&3; つの手順が含まれます。

1. Microsoft Update カタログから修正プログラムをダウンロードします。
2. 通常モードの修正プログラムをインストールして確認します。
3. メンテナンス モードの修正プログラムをインストールして確認します (Update 2 より前のソフトウェアから更新する場合のみ)。

#### <a name="download-updates-for-your-device"></a>デバイスの更新プログラムをダウンロードする
**デバイスが Update 2.1 または 2.2 を実行している場合は**、指定の順序で次の修正プログラムをダウンロードしてインストールする必要があります。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |
| --- | --- | --- | --- | --- |
| 1. |KB3186843 |ソフトウェア更新 &#42; |通常  <br></br>中断なし |～ 45 分 |
| 2. |KB3186859 |LSI ドライバーおよびファームウェア |通常  <br></br>中断なし |～ 20 分 |
| 3. |KB3121261 |Storport および Spaceport 修正プログラム  </br> Windows Server 2012 R2 |通常  <br></br>中断なし |～ 20 分 |

&#42; *ソフトウェア更新プログラムは、デバイスのソフトウェア更新プログラム (`all-hcsmdssoftwareupdate` で始まる) と Cis と Mds のエージェント (`all-cismdsagentupdatebundle` で始まる) の&2; つのバイナリ ファイルで構成されます。Cis と Mds のエージェントをインストールする前に、デバイスのソフトウェア更新プログラムをインストールする必要があります。また、Cis と Mds のエージェント更新プログラムを適用したら (残りの更新プログラムを適用する前に)、`Restart-HcsController` コマンドレットを使用してアクティブなコントローラーを再起動する必要があります。*" 

**デバイスが更新プログラム 0.1、0.2、0.3、1.0、1.1、1.2、または 2.0 を実行している場合**は、前の表に示したソフトウェア、LSI ドライバー、ファームウェアの更新プログラムのほか、次の修正プログラムを、指定の順序でダウンロードしてインストールする必要があります。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |
| --- | --- | --- | --- | --- |
| 4. |KB3146621 |iSCSI パッケージ |通常  <br></br>中断なし |～ 20 分 |
| 5. |KB3103616 |WMI パッケージ |通常  <br></br>中断なし |～ 12 分 |

<br></br>

**デバイスがバージョン 0.2、0.3、1.0、1.1、および 1.2 を実行している場合**は、前の表で示したすべての更新プログラムのほか、ディスク ファームウェアの更新プログラムのインストールが必要になることもあります。 `Get-HcsFirmwareVersion` コマンドレットを実行すると、ディスク ファームウェアの更新プログラムが必要かどうかを確認できます。 `XMGG`、`XGEG`、`KZ50`、`F6C2`、`VR08` の各ファームウェア バージョンを実行している場合は、これらの更新プログラムをインストールする必要はありません。

| 順序 | KB | 説明 | 更新の種類 | インストール時間 |
| --- | --- | --- | --- | --- |
| 6. |KB3121899 |ディスク ファームウェア |メンテナンス  <br></br>中断あり |～ 30 分 |

<br></br>

> [!IMPORTANT]
> * この手順は、Update 3 を適用するために 1 回だけ実行する必要があります。 以降の更新プログラムは、Azure クラシック ポータルを使用して適用できます。
> * Update 2.2 から更新する場合、インストール時間は合計で 1.1 時間ほどです。
> * この手順を使用して更新プログラムを適用する前に、両方のデバイス コント ローラーがオンラインであり、すべてのハードウェア コンポーネントが正常な状態であることを確認します。
> 
> 

この修正プログラムをダウンロードしてインポートするには、次のステップを実行します。

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>次のステップ
詳細については、 [Update 3 リリース](storsimple-update3-release-notes.md)に関するページをご覧ください。


