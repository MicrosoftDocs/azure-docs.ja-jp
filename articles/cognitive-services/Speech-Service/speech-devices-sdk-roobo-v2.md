---
title: Speech Devices SDK Roobo Smart Audio Dev Kit v2 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK Roobo Smart Audio Dev Kit v2 の使用を開始するための前提条件と手順です。
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "80371575"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>デバイス: Roobo Smart Audio Dev Kit v2

この記事では、Roobo Smart Audio Dev Kit2 のデバイス固有の情報を提供します。

## <a name="set-up-the-development-kit"></a>開発キットをセットアップする

1. 開発キットには、2 つのマイクロ USB コネクタがあります。 左側のコネクタは開発キットに電源を供給するためのものです。下の画像では Power と強調表示されています。 右側のコネクタは開発キットを制御するためのものです。画像では Debug とマークされています。 
    ![開発キットの接続](media/speech-devices-sdk/roobo-v2-connections.png)
1. マイクロ USB ケーブルを使用して電源ポートを PC または電源アダプターに接続して、開発キットに電源を供給します。 トップ ボードの下で緑色の電源インジケーターが点灯します。
1. 開発キットを制御するために、2 本目のマイクロ USB ケーブルを使用してデバッグ ポートをコンピューターに接続します。 信頼性の高い通信を確保するためには、高品質のケーブルを使用することが不可欠になります。
1. 上の画像に示すように、環状のマイクがまっすぐ天井を向くように開発キットを正しく配置します


## <a name="development-information"></a>開発情報

開発の詳細については、[Roobo の開発ガイド](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)を参照してください。

## <a name="audio-recordplay"></a>オーディオの録音と再生

DDK2 のオーディオ操作は、次の方法で実行できます。
* ALSA オープンソース ライブラリとそのアプリケーションを使用します。
* アプリケーション開発を行うには、Appmainprog インターフェイスを使用します。 DDK2 のオーディオ関連のソフトウェア フレームワークでは、標準の ALSA フレームワークが使用されるため、libasound を使用して 直接ソフトウェアを開発できます。 そのため、ALSA の arecord と aplay を直接使用して、オーディオの録音と再生を行うことができます。
