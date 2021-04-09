---
title: StorSimple デバイスのモードを変更する | Microsoft Docs
description: StorSimple デバイスのモードについて、また StorSimple 用 Windows PowerShell を使用してデバイスのモードを変更する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: c7b0ea489c1d70ab86d677aad666ea6728fa76b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017085"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>StorSimple デバイスのデバイス モードの変更

この記事では、StorSimple デバイスのさまざまな動作モードについて簡単に説明します。 StorSimple デバイスの動作モードには、通常モード、メンテナンス モード、回復モードの 3 つがあります。

この記事を読むと、次のことについて理解できます。

* StorSimple デバイスのモードとは
* StorSimple デバイスの現在のモードを確認する方法
* 通常モードからメンテナンス モードおよび *その逆の方向*

上記の管理タスクは、StorSimple デバイスの Windows PowerShell インターフェイスでのみ実行できます。

## <a name="about-storsimple-device-modes"></a>StorSimple デバイスのモードについて

StorSimple デバイスは、通常モード、メンテナンス モード、回復モードで動作できます。 ここでは、これらの各モードについて簡単に説明します。

### <a name="normal-mode"></a>通常モード

このモードは、完全に構成された StorSimple デバイスの通常の動作モードとして定義されます。 既定では、デバイスは通常モードになっています。

### <a name="maintenance-mode"></a>メンテナンス モード

StorSimple デバイスをメンテナンス モードに切り替えることが必要になる場合もあります。 このモードでは、デバイスのメンテナンスを実行し、中断を伴う更新プログラムをインストールすることができます。たとえば、ディスク ファームウェアに関連する更新プログラムなどです。

システムをメンテナンス モードに切り替えるには、StorSimple 用 Windows PowerShell のみを使用できます。 このモードでは、すべての I/O 要求が一時停止します。 非揮発性ランダム アクセス メモリ (NVRAM) などのサービスやクラスター化サービスも停止します。 このモードの開始時または終了時に、両方のコントローラーが再起動します。 メンテナンス モードを終了すると、すべてのサービスが再開され、正常な状態になります。 これには数分かかることがあります。

> [!NOTE]
> **メンテナンス モードは、適切に動作しているデバイスでのみサポートされます。コントローラーのいずれかまたは両方が機能していないデバイスでは、サポートされていません。**


### <a name="recovery-mode"></a>回復モード

回復モードは、"ネットワーク サポート付きの Windows セーフ モード" と説明することができます。 回復モードでは、Microsoft サポート チームが連絡を受けてシステムに対して診断を実行できます。 回復モードの主な目標は、システム ログを取得することです。

システムが回復モードになった場合は、Microsoft サポートに対処法をお問い合わせください。 詳細については、「 [Microsoft サポートに問い合わせる](storsimple-8000-contact-microsoft-support.md)」を参照してください。

> [!NOTE]
> **デバイスを回復モードに切り替えることはできません。デバイスが不適切な状態になると、回復モードでは、Microsoft サポート担当者が調査できる状態にすることを試みます。**

## <a name="determine-storsimple-device-mode"></a>StorSimple デバイスのモードの確認

#### <a name="to-determine-the-current-device-mode"></a>デバイスの現在のモードを確認するには

1. 「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)」の手順に従って、デバイスのシリアル コンソールにログオンします。
2. デバイスのシリアル コンソール メニューでバナー メッセージを確認します。 このメッセージでは、デバイスがメンテナンス モードと回復モードのどちらであるかを明示的に示します。 システムのモードに関連する具体的な情報がメッセージに含まれていない場合、デバイスは通常モードで動作しています。

## <a name="change-the-storsimple-device-mode"></a>StorSimple デバイスのモードを変更する

StorSimple デバイスを (通常モードから) メンテナンス モードにすると、メンテナンスを実行したり、メンテナンス モードの更新プログラムをインストールしたりできます。 メンテナンス モードを開始または終了するには、次の手順を実行します。

> [!IMPORTANT]
> メンテナンス モードに入る前に、Azure Portal で対象デバイスの **[デバイスの設定] > [ハードウェアの正常性]** にアクセスして、両方のデバイス コントローラーが正常な状態であることを確かめます。 一方または両方のコントローラーが正常な状態でない場合、次の手順で Microsoft サポートにお問い合わせください。 詳細については、「 [Microsoft サポートに問い合わせる](storsimple-8000-contact-microsoft-support.md)」を参照してください。
 

#### <a name="to-enter-maintenance-mode"></a>メンテナンス モードを開始するには

1. 「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)」の手順に従って、デバイスのシリアル コンソールにログオンします。
2. シリアル コンソール メニューで、オプション 1 を選択して、**フル アクセスでログイン** します。 画面の指示に従って、 **デバイス管理者のパスワード** を入力します。 既定のパスワードは `Password1`になります。
3. コマンド プロンプトに、次のコマンドを入力します。 
   
    `Enter-HcsMaintenanceMode`
4. メンテナンス モードにすると、すべての I/O 要求が中断されAzure Portal への接続が切断されるという警告メッセージが表示され、確認が求められます。 メンテナンス モードを開始するには、「 **Y** 」と入力します。
5. 両方のコントローラーが再起動します。 再起動が完了すると、デバイスがメンテナンス モードであることがシリアル コンソールのバナーに示されます。 サンプル出力を次に示します。

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>メンテナンス モードを終了するには

1. デバイスのシリアル コンソールにログオンします。 バナー メッセージを調べて、デバイスがメンテナンス モードになっていることを確認します。
2. コマンド プロンプトで、次のコマンドを入力します。
   
    `Exit-HcsMaintenanceMode`
3. 警告メッセージと確認メッセージが表示されます。 メンテナンス モードを終了するには、「 **Y** 」と入力します。
4. 両方のコントローラーが再起動します。 再起動が完了すると、デバイスが通常モードであることがシリアル コンソールのバナーに示されます。 サンプル出力を次に示します。

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>次のステップ

StorSimple デバイスに [通常モードとメンテナンス モードの更新プログラムを適用](storsimple-update-device.md) する方法について説明します。

