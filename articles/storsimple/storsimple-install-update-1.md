---
title: "StorSimple デバイスへの Update 1.2 のインストール | Microsoft Docs"
description: "StorSimple 8000 シリーズのデバイスに StorSimple 8000 シリーズの Update 1.2 をインストールする方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 7a513923-eb77-4078-b0ab-f8e90183796a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 56af9046fff24229298997b169aaf329e1692cc2
ms.openlocfilehash: 80ff35cc47dfc38089f4c392ef4c90baf9ccc03e
ms.lasthandoff: 02/28/2017


---
# <a name="install-update-12-on-your-storsimple-8000-series-device"></a>StorSimple 8000 シリーズ デバイスに Update 1.2 をインストールする
## <a name="overview"></a>概要
このチュートリアルでは、Update 1 より前のソフトウェア バージョンを実行している StorSimple デバイスに Update 1.2 をインストールする方法について説明します。 このチュートリアルではまた、ゲートウェイが StorSimple デバイスの DATA 0 以外のネットワーク インターフェイスで構成されている場合にこの更新プログラムに必要な追加の手順についても説明します。

Update 1.2 には、デバイス ソフトウェアの更新プログラム、LSI ドライバーの更新プログラム、ディスク ファームウェアの更新プログラムが含まれます。 デバイス ソフトウェアと LSI ドライバーの更新プログラムは中断を伴わない更新プログラムであるため、Azure クラシック ポータルを使用して適用できます。 ディスク ファームウェアの更新プログラムは中断を伴う更新プログラムであるため、デバイスの Windows PowerShell インターフェイスでのみ適用できます。

デバイスが実行しているバージョンに応じて、Update 1.2 を適用するかどうかを判断します。 デバイスのソフトウェア バージョンは、デバイスの**ダッシュボード**の **[概要]** セクションに移動することで確認できます。

</br>

| 実行しているソフトウェア バージョン | ポータルの動作 |
| --- | --- |
| リリース - GA |リリース バージョン (GA) を実行している場合は、この更新プログラムを適用しないでください。 デバイスを更新するには、 [Microsoft サポートにお問い合わせください](storsimple-contact-microsoft-support.md) 。 |
| Update 0.1 |ポータルによって Update 1.2 が適用されます。 |
| Update 0.2 |ポータルによって Update 1.2 が適用されます。 |
| Update 0.3 |ポータルによって Update 1.2 が適用されます。 |
| Update 1 |この更新プログラムは使用できません。 |
| Update 1.1 |この更新プログラムは使用できません。 |

</br>

> [!IMPORTANT]
> * 更新プログラムは段階的に公開されるため、Update 1.2 が即座に表示されない場合があります。 これらの更新プログラムは間もなく利用可能になるため、数日後にもう一度更新プログラムの有無を確認してください。
> * この更新プログラムには、ハードウェアの状態とネットワーク接続の点からデバイスの正常性を判断するための、手動と自動の一連の事前チェックが含まれます。 これらの事前チェックは、Azure クラシック ポータルから更新プログラムを適用する場合にのみ実行されます。
> * ソフトウェアとドライバーの更新プログラムのインストールには Azure クラシック ポータルを使用することをお勧めします。 ポータルで更新前のゲートウェイのチェックに失敗した場合のみ、(更新プログラムをインストールする) デバイスの Windows PowerShell インターフェイスに移動してください。 更新プログラムのインストールには、(Windows の更新プログラムを含めて) 5 ～ 10 時間がかかる場合があります。 メンテナンス モードの更新プログラムは、デバイスの Windows PowerShell インターフェイスからインストールする必要があります。 メンテナンス モードの更新プログラムは中断を伴う更新プログラムであるため、デバイスにダウンタイムが発生します。
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Azure クラシック ポータルを使用して Update 1.2 をインストールする
デバイスを [Update 1.2](storsimple-update1-release-notes.md)に更新するには、次の手順を実行します。 この手順は、ゲートウェイがデバイスの DATA 0 のネットワーク インターフェイスで構成されている場合にのみ使用します。

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. デバイスで **StorSimple 8000 Series Update 1.2 (6.3.9600.17584)**が実行されていることを確認します。 **[最終更新日]** も変更されています。 メンテナンス モードの更新プログラムも利用できることが示されます (このメッセージは、更新プログラムをインストールした後、最大 24 時間表示され続ける可能性があります)。
   
   メンテナンス モードの更新プログラムは、デバイスのダウンタイムを発生させる更新プログラムであり、デバイスの Windows PowerShell インターフェイス経由でのみ適用できます。
   
   ![Maintenance page](./media/storsimple-install-update-1/InstallUpdate12_10M.png "Maintenance page")
2. 「[修正プログラムをダウンロードするには](#to-download-hotfixes)」で示された手順を使用して KB3063416 を検索してメンテナンス モードの更新プログラムをダウンロードします。ディスク ファームウェアの更新プログラムがインストールされます (他の更新プログラムが既にインストールされている必要があります)。
3. メンテナンス モードの更新プログラムをインストールするには、「 [メンテナンス モードの修正プログラムをインストールして確認するには](#to-install-and-verify-maintenance-mode-hotfixes) 」に記載されている手順に従います。
4. Azure クラシック ポータルで **[メンテナンス]** ページに移動し、ページの下部にある **[更新プログラムのスキャン]** をクリックして Windows 更新プログラムを確認し、**[更新プログラムのインストール]** をクリックします。 すべての更新プログラムが正常にインストールされたら、作業は終了です。

## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>ゲートウェイが DATA 0 以外のネットワーク インターフェイスで構成されているデバイスに Update 1.2 をインストールする
Azure クラシック ポータルから更新プログラムをインストールしようとしたときにゲートウェイのチェックに失敗した場合にのみ、この手順を使用してください。 このチェックは、DATA 0 以外のネットワーク インターフェイスに割り当てられているゲートウェイがある場合に、デバイスが Update 1 より前のソフトウェア バージョンを実行していると、失敗します。 デバイスのゲートウェイが DATA 0 以外のネットワーク インターフェイスで設定されていない場合、Azure クラシック ポータルから直接デバイスを更新できます。 「 [を Azure クラシック ポータル使用して Update 1.2 をインストールする](#install-update-1.2-via-the-azure-classic-portal)」を参照してください。

この方法でアップグレードできるソフトウェアのバージョンは、Update 0.1、Update 0.2、Update 0.3 です。

> [!IMPORTANT]
> * デバイスがリリース (GA) バージョンを実行している場合、この更新プログラムの使用については、 [Microsoft サポート](storsimple-contact-microsoft-support.md) にお問い合わせください。
> * この手順は、Update 1.2 を適用するために 1 回だけ実行する必要があります。 以降の更新プログラムは、Azure クラシック ポータルを使用して適用できます。
> 
> 

デバイスが Update 1 よりも前のソフトウェアを実行していて、ゲートウェイが DATA 0 以外のネットワーク インターフェイスで設定されている場合、次の 2 つの方法で Update 1.2 を適用できます。

* **方法 1**: デバイスの Windows PowerShell インターフェイスから、`Start-HcsHotfix` コマンドレットを使用して更新プログラムをダウンロードして適用します。 これが推奨される方法です。 **デバイスが Update 1.0 または Update 1.1 を実行している場合は、この方法で Update 1.2 を適用しないでください。**
* **方法 2**: ゲートウェイ構成を削除し、Azure クラシック ポータルから直接更新プログラムをインストールします。

個々の詳細な手順は、次のセクションに記載されています。

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>方法 1: StorSimple 用 Windows PowerShell を使用して Update 1.2 を修正プログラムとして適用する
Update 0.1、0.2、0.3 を実行している場合、および Azure クラシック ポータルから更新プログラムをインストールしようとしたときにゲートウェイのチェックが失敗した場合のみ、この手順を使用してください。 リリース (GA) ソフトウェアを実行している場合、デバイスの更新については、 [Microsoft サポート](storsimple-contact-microsoft-support.md) にお問い合わせください。

修正プログラムとして Update 1.2 をインストールするには、次の修正プログラムをダウンロードしてインストールする必要があります。

| 順序 | KB | 説明 | 更新の種類 |
| --- | --- | --- | --- |
| 1 |KB3063418 |ソフトウェア更新 |通常 |
| 2 |KB3043005 |LSI SAS コントローラーの更新 |通常 |
| 3 |KB3063416 |ディスク ファームウェア |メンテナンス |

この手順を使用して更新プログラムを適用する前に、次のことを確認します。

* 両方のデバイス コントローラーがオンラインである。

Update 1.2 を適用するには、次の手順を実行します。 **この更新プログラムは、完了するまでに約 2 時間かかる場合があります (ソフトウェアに約 30 分、ドライバーに約 30 分、ディスク ファームウェアに約 45 分)。**

[!INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]

## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>方法 2: ゲートウェイの構成を削除した後で、Azure クラシック ポータルを使用して Update 1.2 を適用する
この手順は、Update 1 よりも前のソフトウェア バージョンを実行していて、ゲートウェイが DATA 0 以外のネットワーク インターフェイスで設定されている StorSimple デバイスのみに当てはまります。 更新プログラムを適用する前に、ゲートウェイの設定をクリアする必要があります。

更新の完了に数時間かかる場合があります。 ホストが別のサブネットにある場合、iSCSI インターフェイスでゲートウェイの構成を削除すると、ダウンタイムが発生する場合があります。 ダウンタイムを抑えるために、DATA 0 を iSCSI トラフィック用に構成することをお勧めします。

ゲートウェイとのネットワーク インターフェイスを無効にして更新プログラムを適用するには、次の手順を実行します。

[!INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>次のステップ
詳細については、 [Update 1.2 リリース](storsimple-update1-release-notes.md)に関するページを参照してください。


