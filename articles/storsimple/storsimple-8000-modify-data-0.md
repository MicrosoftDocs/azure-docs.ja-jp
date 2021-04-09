---
title: StorSimple 8000 シリーズ デバイスの DATA 0 設定を変更する | Microsoft Docs
description: StorSimple 用 Windows PowerShell を使用して、StorSimple デバイスの DATA 0 のネットワーク インターフェイスを再構成する方法について説明します。
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 85d7114f419266124d0d23368b24700af025758a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961041"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>StorSimple 8000 シリーズ デバイスの DATA 0 ネットワーク インターフェイス設定の変更

## <a name="overview"></a>概要

Microsoft Azure StorSimple デバイスには、DATA 0 ～ DATA 5 の 6 つのネットワーク インターフェイスがあります。 DATA 0 インターフェイスは、必ず Windows PowerShell インターフェイスまたはシリアル コンソールを介して構成され、自動的にクラウドに対応します。 Azure ポータルを使用して、DATA 0 ネットワーク インターフェイスを構成することはできません。

DATA 0 インターフェイスは、まず、StorSimple デバイスを初めてデプロイするときにセットアップ ウィザードで構成されます。 デバイスが操作モードのときは、DATA 0 の設定を再構成することが必要になる場合があります。 このチュートリアルでは、StorSimple 用 Windows PowerShell を使って DATA 0 ネットワーク設定を変更する 2 つの方法について説明します。

このチュートリアルを読むと、次のことができるようになります。

* セットアップ ウィザードを使用して DATA 0 ネットワーク設定を変更する
* `Set-HcsNetInterface` コマンドレットを使用して DATA 0 ネットワーク設定を変更する

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>セットアップ ウィザードを使用して DATA 0 ネットワーク設定を変更する
DATA 0 ネットワーク設定を再構成するには、StorSimple デバイスの Windows PowerShell インターフェイスに接続し、セットアップ ウィザードのセッションを起動します。 DATA 0 の設定を変更するには、次の手順を実行します。

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>セットアップ ウィザードを使用して DATA 0 ネットワーク設定を変更するには
1. シリアル コンソール メニューで、オプション 1 を選択して、**フル アクセスでログイン** します。 画面の指示に従って、 **デバイス管理者のパスワード** を入力します。 既定のパスワードは `Password1`です。
2. コマンド プロンプトで、次のコマンドを入力します。
   
    `Invoke-HcsSetupWizard`
3. デバイスの DATA 0 インターフェイスの構成に役立つセットアップ ウィザードが表示されます。 IP アドレス、ゲートウェイ、およびネットマスクの新しい値を指定します。

> [!NOTE]
> 固定コントローラーの IP は、Azure ポータルの StorSimple デバイスの **[ネットワーク設定]** ブレードで再構成する必要があります。 詳細については、 [ネットワーク インターフェイスの変更](storsimple-8000-modify-device-config.md#modify-network-interfaces)をご覧ください。

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Set-HcsNetInterface コマンドレットを使用して DATA 0 ネットワーク設定を変更する
DATA 0 ネットワーク インターフェイスを再構成するためのもう 1 つの方法は、 `Set-HcsNetInterface` コマンドレットを使用することです。 このコマンドレットは、StorSimple デバイスの Windows PowerShell インターフェイスから実行します。 この手順を使用する場合、コントローラーの固定 IP アドレスをここで構成することもできます。 DATA 0 の設定を変更するには、次の手順を実行します。 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Set-HcsNetInterface コマンドレットを使用して DATA 0 ネットワーク設定を変更するには
1. シリアル コンソール メニューで、オプション 1 を選択して、**フル アクセスでログイン** します。 画面の指示に従って、デバイス管理者のパスワードを入力します。 既定のパスワードは `Password1`です。
2. コマンド プロンプトで、次のコマンドを入力します。
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    次の項目について、DATA 0 の値を山かっこ (< >) で囲んで入力します。
   
   * IPv4 アドレス
   * IPv4 ゲートウェイ
   * IPv4 サブネット マスク
   * コント ローラー 0 の固定 IPv4 アドレス
   * コント ローラー 1 の固定 IPv4 アドレス
     
     このコマンドレットの使用方法の詳細については、 [StorSimple 用 Windows PowerShell コマンドレット リファレンス](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630))をご覧ください。

## <a name="next-steps"></a>次のステップ
* DATA 0 以外のネットワーク インターフェイスを構成するには、[Azure ポータルで [ネットワーク設定の構成]](storsimple-8000-modify-device-config.md) を使用できます。 
* ネットワーク インターフェイスを構成するときに問題が発生した場合は、 [デプロイに関する問題のトラブルシューティング](./storsimple-8000-troubleshoot-deployment.md)のページを参照してください。