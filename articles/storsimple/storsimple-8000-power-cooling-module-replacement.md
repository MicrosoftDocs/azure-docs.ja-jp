---
title: StorSimple 8000 シリーズ デバイスの PCM を交換する | Microsoft Docs
description: StorSimple デバイスの電源冷却モジュール (PCM) を取り外して交換する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 045cec85174a88d1d608a4adc679461008852768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514590"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>StorSimple デバイスの電源冷却モジュールを交換する
## <a name="overview"></a>概要
Microsoft Azure StorSimple デバイスの電源冷却モジュール (PCM) は、主エンクロージャと EBOD エンクロージャを通して制御される電源と冷却ファンで構成されます。 各エンクロージャで認定されている PCM のモデルは 1 つだけあります。 主エンクロージャでは 764 W PCM が、EBOD エンクロージャでは 580 W PCM が認定されています。 主エンクロージャと EBOD エンクロージャの PCM は異なりますが、交換手順は同じです。

このチュートリアルでは、次の方法について説明します。

* PCM を取り外す
* 交換用 PCM を取り付ける

> [!IMPORTANT]
> PCM を取り外して交換する前に、「 [StorSimple ハードウェア コンポーネントの交換](storsimple-8000-hardware-component-replacement.md)」の安全に関する情報を再確認してください。


## <a name="before-you-replace-a-pcm"></a>PCM を交換する前に
PCM を交換する前に、次の重要な問題に注意してください。

* PCM の電源に障害が発生した場合、問題のあるモジュールは取り付けたままにしますが、電源コードは取り外します。 ファンの電力はエンクロージャから供給され、冷却は適切に続行されます。 ファンに障害が発生した場合は、PCM をただちに交換する必要があります。
* PCM を取り外す前に、メイン スイッチをオフにする (存在する場合) か電源コードを物理的に抜くことで、PCM の電源を切ります。 これにより、電源のシャット ダウンが間もなく発生することがシステムに警告されます。
* システムの稼働を継続するために、障害が発生している PCM を交換する前に他の PCM が機能していることを確認します。 障害が発生している PCM は、完全に動作する PCM に早急に交換する必要があります。
* PCM モジュールの交換は数分で完了できますが、過熱状態を防ぐために障害が発生した PCM の取り外しは 10 分以内に完了する必要があります。
* 工場から出荷された交換用 764 W PCM モジュールには、バックアップ バッテリ モジュールが含まれていません。 障害が発生している PCM からバッテリを取り外し、交換用モジュールにそのバッテリを装着してから、交換を行う必要があります。 詳細については、 [バックアップ バッテリ モジュールを取り外して装着する](storsimple-8000-battery-replacement.md)方法を参照してください。

## <a name="remove-a-pcm"></a>PCM を取り外す
電源冷却モジュール (PCM) を Microsoft Azure StorSimple デバイスから取り外す準備ができたら、次の手順に従います。

> [!NOTE]
> PCM を取り外す前に、適切な交換用 PCM (主エンクロージャでは 764 W、EBOD エンクロージャでは 580 W) を用意していることを確認します。

#### <a name="to-remove-a-pcm"></a>PCM を取り外すには
1. Azure クラシック ポータルで、**[設定]、[監視]、[ハードウェアの正常性]** の順にクリックします。 **[共有コンポーネント]** で PCM コンポーネントの状態を調べて、障害が発生している PCM を特定します。
   
   * PCM 0 の電源で障害が発生している場合は、 **PCM 0 の電源** の状態が赤色になります。
   * PCM 1 の電源で障害が発生している場合は、 **PCM 1 の電源** の状態が赤色になります。
   * PCM 1 のファンに障害が発生した場合は、**PCM 0 の冷却装置 0** または **PCM 0 冷却装置 1** の状態が赤色になります。
2. 主エンクロージャの背面で障害が発生した PCM を探します。 8600 モデルを稼働させている場合は、フロント パネルの LED ディスプレイに表示されるシステム ユニット識別番号を見て、主エンクロージャを識別します。 主エンクロージャに表示される既定のユニット ID は **00** であるのに対し、EBOD エンクロージャに表示される既定のユニット ID は **01** です。 次の図と表で、LED ディスプレイのフロント パネルについて説明します。
   
    ![フロント OPS パネルのシステム ID](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **図 1** デバイスのフロント パネル  
   
   | Label | 説明 |
   |:--- |:--- |
   | 1 |ミュート ボタン |
   | 2 |システム電源 |
   | 3 |モジュール障害 |
   | 4 |論理的な障害 |
   | 5 |ユニット ID ディスプレイ |
3. 障害が発生している PCM を識別するために、主エンクロージャの背面にあるモニタリング インジケーター LED も使用できます。 次の図と表で、LED を使用して障害のある PCM を見つける方法を理解してください。 たとえば、**ファンの障害** に対応する LED が点灯している場合は、ファンが障害状態となっています。 同様に、**AC 障害** に対応する LED が点灯している場合は、電源装置が障害状態です。 
   
    ![デバイスの PCM モニタリング インジケーター LED のバックプレーン](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **図 2** PCM 背面のインジケーター LED
   
   | Label | 説明 |
   |:--- |:--- |
   | 1 |AC 電源障害 |
   | 2 |ファン障害 |
   | 3 |バッテリ障害 |
   | 4 |PCM OK |
   | 5 |DC 電源障害 |
   | 6 |バッテリ正常 |
4. 次の StorSimple デバイスの背面図を参照して、障害が発生している PCM モジュールを見つけてください。 PCM 0 は左側に、PCM 1 は右側に配置されています。 次の表で、モジュールについて説明します。
   
     ![デバイスの主エンクロージャ モジュールのバックプレーン](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **図 3** デバイスの背面とプラグイン モジュール 
   
   | Label | 説明 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |コントローラー 0 |
   | 4 |コントローラー 1 |
5. 障害が発生している PCM の電源を切り、電源ケーブルを抜きます。 これで、PCM を取り外すことができます。
6. ラッチと PCM ハンドルの側面を親指と人差し指でつまみ、強く押してハンドルを開きます。
   
    ![PCM ハンドルを開く](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **図 4** PCM ハンドルを開く
7. ハンドルを握って PCM を取り外します。
   
    ![デバイスの PCM を取り外す](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **図 5** PCM を取り外す

## <a name="install-a-replacement-pcm"></a>交換用 PCM を取り付ける
StorSimple デバイスに PCM を取り付けるには次の手順に従います。 交換用 PCM (764 W PCM のみが該当) を取り付ける前に、バックアップ バッテリ モジュールを装着したことを確認します。 詳細については、 [バックアップ バッテリ モジュールを取り外して装着する](storsimple-8000-battery-replacement.md)方法を参照してください。

#### <a name="to-install-a-pcm"></a>PCM を取り付けるには
1. 交換用 PCM がエンクロージャに適合した PCM であることを確認します。 主エンクロージャには 764 W PCM が、EBOD エンクロージャには 580 W PCM が必要です。 主エンクロージャで 580 W PCM を、EBOD エンクロージャで 764 W PCM を使用しようとしないでください。 次の図に、この情報をPCM に貼付されたラベルで特定する場所を示します。
   
    ![デバイスの PCM ラベル](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **図 6** PCM ラベル
2. エンクロージャが破損していないことを確認します。特にコネクタに注意します。 
   
   > [!NOTE]
   > **コネクタ ピンが曲がっている場合は、モジュールを取り付けないでください。**
   > 
   > 
3. PCM ハンドルが開いた状態で、モジュールをエンクロージャに押し込みます。
   
    ![デバイスの PCM を取り付ける](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **図 7** PCM を取り付ける
4. PCM ハンドルを手動で閉じます。 ハンドルのラッチがかかると、カチッと音がします。
   
   > [!NOTE]
   > コネクタ ピンがかみ合ったことを確認するために、ラッチを解放せずにハンドルをゆっくりと引くことができます。 PCM が引き出されたら、コネクタがかみ合う前にラッチを閉じたことを意味します。
   
5. 電源ケーブルを電源と PCM に接続します。
6. 張力のがしベールを固定します。
7. PCM の電源を入れます。
8. 正しく交換されたことを確かめるために、StorSimple デバイス マネージャー サービスの Azure Portal から対象デバイスに移動し、**[設定]、[監視]、[ハードウェアの正常性]** の順に移動します。 **[共有コンポーネント]** で、PCM の状態が緑色になっているはずです。
   
   > [!NOTE]
   > 置換用 PCM が完全に初期化されるまで数分かかる場合があります。

## <a name="next-steps"></a>次のステップ
「 [StorSimple ハードウェア コンポーネントの交換](storsimple-8000-hardware-component-replacement.md)」の説明を参照してください。

