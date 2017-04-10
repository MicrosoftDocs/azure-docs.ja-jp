---
title: "StorSimple デバイスへの Update 2 のインストール | Microsoft Docs"
description: "StorSimple 8000 シリーズのデバイスに StorSimple 8000 シリーズの Update 2 をインストールする方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 8c8981df-75d9-4d19-b137-d6c6ba39dcfb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e788439608b7122f2bca6b99b832baa5258e472d


---
# <a name="install-update-2-on-your-storsimple-device"></a>StorSimple デバイスへの Update 2 のインストール
## <a name="overview"></a>Overview
このチュートリアルでは、Update 2 より前のソフトウェア バージョンを実行している StorSimple デバイスに、Azure クラシック ポータル経由で Update 2 をインストールする方法について説明します。 このチュートリアルでは、ゲートウェイが StorSimple デバイスの DATA 0 以外のネットワーク インターフェイスで構成されているときに、Update 1 より前のソフトウェア バージョンから更新する場合に、この更新プログラムで必要な手順についても説明します。

Update 2 には、デバイス ソフトウェアの更新プログラム、LSI ドライバーの更新プログラム、およびディスク ファームウェアの更新プログラムが含まれます。 デバイス ソフトウェアと LSI の更新プログラムは中断を伴わない更新プログラムであるため、Azure クラシック ポータルを使用して適用できます。 ディスク ファームウェアの更新プログラムは中断を伴う更新プログラムであるため、デバイスの Windows PowerShell インターフェイスでのみ適用できます。

> [!IMPORTANT]
> * 更新プログラムは段階的に公開されるため、Update 2 が即座に表示されない場合があります。 これらの更新プログラムは間もなく利用可能になるため、数日後にもう一度更新プログラムの有無を確認してください。
> * インストールの前に、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための手動と自動の一連の事前チェックが行われます。 これらの事前チェックは、Azure クラシック ポータルから更新プログラムを適用する場合にのみ実行されます。
> * ソフトウェアとドライバーの更新プログラムのインストールには Azure クラシック ポータルを使用することをお勧めします。 ポータルで更新前のゲートウェイのチェックに失敗した場合のみ、(更新プログラムをインストールする) デバイスの Windows PowerShell インターフェイスに移動してください。 更新プログラムのインストールには、(Windows の更新プログラムを含めて) 4 ～ 7 時間かかる場合があります。 メンテナンス モードの更新プログラムは、デバイスの Windows PowerShell インターフェイスからインストールする必要があります。 メンテナンス モードの更新プログラムは中断を伴う更新プログラムであるため、デバイスにダウンタイムが発生します。
> * オプションの StorSimple Snapshot Manager を実行している場合は、デバイスを更新する前に Snapshot Manager のバージョンを Update 2 にアップグレードしたことを確認します。
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Azure クラシック ポータルを使用して Update 2 をインストールする
デバイスを [Update 2](storsimple-update2-release-notes.md)に更新するには、次の手順を実行します。

> [!NOTE]
> Update 2 にすると、Microsoft はデバイスから追加の診断情報を取得できるようになります。 その結果、Microsoft の運用チームが問題のあるデバイスを識別したときに、デバイスから情報を収集して問題を診断する能力が向上します。 Update 2 を受け入れると、このプロアクティブ サポートの提供に同意することになります。
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. デバイスで **StorSimple 8000 Series Update 2 (6.3.9600.17673)**が実行されていることを確認します。 **[最終更新日]** も変更されています。 メンテナンス モードの更新プログラムも利用できることが示されます (このメッセージは、更新プログラムをインストールした後、最大 24 時間表示され続ける可能性があります)。
   
   メンテナンス モードの更新プログラムは、デバイスのダウンタイムを発生させる更新プログラムであり、デバイスの Windows PowerShell インターフェイス経由でのみ適用できます。 Update 1.2 を実行している場合は、ディスクのファームウェアが既に最新になっている可能性があります。その場合、メンテナンス モードの更新プログラムをインストールする必要はありません。
2. 「[修正プログラムをダウンロードするには](#to-download-hotfixes)」で示された手順を使用して KB3121899 を検索してメンテナンス モードの更新プログラムをダウンロードします。ディスク ファームウェアの更新プログラムがインストールされます (他の更新プログラムが既にインストールされている必要があります)。
3. メンテナンス モードの更新プログラムをインストールするには、「 [メンテナンス モードの修正プログラムをインストールして確認するには](#to-install-and-verify-maintenance-mode-hotfixes) 」に記載されている手順に従います。

## <a name="install-update-2-as-a-hotfix"></a>Update 2 を修正プログラムとしてインストールする
この手順は、Azure クラシック ポータルから更新プログラムをインストールしようとしたときにゲートウェイのチェックに失敗した場合に使用してください。 このチェックは、DATA 0 以外のネットワーク インターフェイスに割り当てられているゲートウェイがある場合に、デバイスが Update 1 より前のソフトウェア バージョンを実行していると、失敗します。

修正プログラムを使用してアップグレードできるソフトウェアのバージョンは、Update 0.1、Update 0.2、Update 0.3、Update 1、Update 1.1、および Update 1.2 です。 修正プログラムを使用する方法には、次の 3 つの手順が含まれます。

* Microsoft Update カタログから修正プログラムをダウンロードします。
* 通常モードの修正プログラムをインストールして確認します。
* メンテナンス モードの修正プログラムをインストールして確認します。

修正プログラムとして Update 2 をインストールするには、次の修正プログラムをダウンロードしてインストールする必要があります。

| 順序 | KB | 説明 | 更新の種類 |
| --- | --- | --- | --- |
| 1 |KB3121901 |ソフトウェア更新 |通常 |
| 2 |KB3121900 |LSI ドライバー |通常 |
| 3 |KB3080728 |Storport 修正プログラム  </br>  Windows Server 2012 R2 |通常 |
| 4 |KB3090322 |Spaceport 修正プログラム  </br>  Windows Server 2012 R2 |通常 |
| 5 |KB3121899 |ディスク ファームウェア |メンテナンス |

> [!IMPORTANT]
> * デバイスがリリース (GA) バージョンを実行している場合、この更新プログラムの使用については、 [Microsoft サポート](storsimple-contact-microsoft-support.md) にお問い合わせください。
> * この手順は、Update 2 を適用するために 1 回だけ実行する必要があります。 以降の更新プログラムは、Azure クラシック ポータルを使用して適用できます。
> * 各修正プログラムのインストールは、完了するまで約 20 分かかる可能性があります。 インストール時間の合計は 2 時間近くになります。
> * この手順を使用して更新プログラムを適用する前に、両方のデバイス コント ローラーがオンラインであり、すべてのハードウェア コンポーネントが正常な状態であることを確認します。
> 
> 

この更新プログラムを修正プログラムとして適用するには、次の手順を実行します。

[!INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>次のステップ
詳細については、 [Update 2 リリース](storsimple-update2-release-notes.md)に関するページを参照してください。




<!--HONumber=Nov16_HO3-->


