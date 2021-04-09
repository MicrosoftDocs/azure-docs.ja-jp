---
title: Microsoft Azure StorSimple 8000 シリーズ デバイスのバッテリ交換
description: StorSimple デバイスのバックアップ バッテリ モジュールの取り外し、交換、メンテナンスの方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023773"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>StorSimple デバイスのバックアップ バッテリ モジュールを交換する

## <a name="overview"></a>概要
Microsoft Azure StorSimple デバイスの主エンクロージャの電源および冷却モジュール (PCM) には、予備のバッテリ パックがあります。 このパックは、主エンクロージャの AC 電源が失われた場合に StorSimple デバイスがデータを保存できるように、電源を提供します。 このバッテリ パックは、*バックアップ バッテリ モジュール* と呼びます。 バックアップ バッテリ モジュールは、StorSimple デバイスの主エンクロージャに対してのみ存在します (EBOD エンクロージャにはバックアップ バッテリ モジュールは含まれません)。

このチュートリアルでは、次の方法について説明します。

* バックアップ バッテリ モジュールを取り外す
* 新しいバックアップ バッテリ モジュールを取り付ける
* バックアップ バッテリ モジュールを保守する

> [!IMPORTANT]
> バックアップ バッテリ モジュールを取り外して交換する前に、「 [StorSimple ハードウェア コンポーネントの交換](storsimple-8000-hardware-component-replacement.md)」の安全に関する情報を再確認してください。


## <a name="remove-the-backup-battery-module"></a>バックアップ バッテリ モジュールを取り外す
StorSimple デバイスのバックアップ バッテリ モジュールは、現場交換可能ユニットです。 PCM に取り付けるまで、バッテリ モジュールは元のパッケージに格納しておく必要があります。 バックアップ バッテリーを取り外すには、次の手順を実行します。

#### <a name="to-remove-the-backup-battery-module"></a>バックアップ バッテリ モジュールを取り外すには
1. Azure Portal で StorSimple デバイス マネージャー サービス ブレードに移動します。 **[デバイス]** に移動し、デバイスの一覧から目的のデバイスを選択します。 **[監視]**  >  **[ハードウェアの正常性]** に移動します。 [**共有コンポーネント**] の下にバッテリの状態が表示されます。
2. バッテリが故障している PCM を特定します。 図 1 では、StorSimple デバイスの背面を示します。
   
    ![デバイスの主エンクロージャ モジュールのバックプレーン](./media/storsimple-battery-replacement/IC740994.png)
   
    **図 1** PCM およびコントローラー モジュールが示されているプライマリ デバイスの背面
   
   | Label | 説明 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |コントローラー 0 |
   | 4 |コントローラー 1 |
   
    図 2 の番号 3 で示されいてるように、 **バッテリ障害** に対応する PCM 0 のモニタリング インジケーター LED が点灯している必要があります。
   
    ![デバイスの PCM モニタリング インジケーター LED のバックプレーン](./media/storsimple-battery-replacement/IC740992.png)
   
    **図 2** モニタリング インジケーター LED が示されている PCM の背面
   
   | Label | 説明 |
   |:--- |:--- |
   | 1 |AC 電源障害 |
   | 2 |ファン障害 |
   | 3 |バッテリ障害 |
   | 4 |PCM OK |
   | 5 |DC 電源障害 |
   | 6 |バッテリ正常 |
3. バッテリが故障した PCM を取り外すには、「 [PCM を取り外す](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm)」の手順に従います。
4. PCM を取り外したら、次の図に示すように、バッテリ モジュールのハンドルを上へ回転して、引っ張ってバッテリを取り外します。
   
    ![PCM からのバッテリの取り外し](./media/storsimple-battery-replacement/IC741019.png)
   
    **図 3** PCM からのバッテリーの取り外し
5. モジュールを現場交換可能ユニットのパッケージに置きます。
6. 適切なサービスと処理のために不良ユニットをマイクロソフトに返します。

## <a name="install-a-new-backup-battery-module"></a>新しいバックアップ バッテリ モジュールを取り付ける
次の手順に従って、交換用バッテリ モジュールを StorSimple デバイスの主エンクロージャの PCM に取り付けます。

#### <a name="to-install-the-battery-module"></a>バッテリ モジュールを取り付けるには
1. バックアップ バッテリ モジュールを正しい向きで PCM 内に配置します。
2. バッテリ モジュールのハンドルを押し下げてコネクタに完全に装着します。
3. 「 [StorSimple デバイスの電源および冷却モジュールを交換する](storsimple-8000-power-cooling-module-replacement.md)」のガイドラインに従って主エンクロージャの PCM を交換します。
4. 交換が完了したら、対象デバイスに移動し、Azure Portal で **[監視]**  >  **[ハードウェアの正常性]** に移動します。 バッテリーの状態を確認して、正常に取り付けられていることを確認します。 緑色の状態は、バッテリーが正常な状態であることを示しています。

## <a name="maintain-the-backup-battery-module"></a>バックアップ バッテリ モジュールを保守する
StorSimple デバイスでは、停電中にバックアップ バッテリ モジュールがコントローラーに電力を供給します。 これにより、StorSimple デバイスはシャットダウンする前に適切な方法で重要なデータを保存できます。 PCM 内の 2 つの完全に充電されたバッテリにより、システムは 2 つの連続する停電に対処できます。

Azure Portal の **[監視]** ブレードの **[ハードウェアの正常性]** では、バッテリの故障または寿命が近いことが示されます。 バッテリの状態は [**共有コンポーネント**] の [**PCM 0 のバッテリ**] または [**PCM 1 のバッテリ**] によって示されます。 このブレードでは、寿命が近い場合は **[低下]** と表示され、寿命に達した場合は **[失敗]** と表示されます。

> [!NOTE]
> バッテリは単に充電が必要な場合にも **[失敗]** と表示されることがあります。


**[低下]** 状態が表示されたら、次の一連の措置をお勧めします。

* システムで最近停電が発生したか、またはバッテリのメンテナンスが定期的に行われている可能性があります。 続行する前に、12 時間はシステムを観察します。
  
  * コントローラーと PCM を実行したまま AC 電源に 12 時間連続して接続した後、状態がまだ **[低下]** の場合は、バッテリを交換する必要があります。 交換用バックアップ バッテリ モジュールについては [マイクロソフトのサポートに問い合わせ](storsimple-8000-contact-microsoft-support.md) てください。
  * 12 時間後に状態が [OK] になった場合は、バッテリは動作しており、保守充電のみが必要です。
* 関連する AC 電源の停電がなく、PCM をオンにして AC 電源に接続されている場合は、バッテリを交換する必要があります。 [Contact Microsoft Support](storsimple-8000-contact-microsoft-support.md) てください。

> [!IMPORTANT]
> 国または地域の規定に従って、故障したバッテリを廃棄します。

## <a name="next-steps"></a>次のステップ
「 [StorSimple ハードウェア コンポーネントの交換](storsimple-8000-hardware-component-replacement.md)」の説明を参照してください。

