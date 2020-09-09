---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050476"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

この記事では、デバイス ツインに必要なプロパティを追加した後、ID レジストリに対してクエリを実行し、適切に更新された報告されるプロパティを持つデバイスをすべて検索するバックエンド サービスを作成します。 そのサービスには、必要なデバイス ツインのプロパティを変更するための**サービス接続**アクセス許可と、ID レジストリに対してクエリを実行するための**レジストリ読み取り**アクセス許可が必要となります。 その 2 つのアクセス許可だけを含んだ既定の共有アクセス ポリシーは存在しないため、共有アクセス ポリシーを独自に作成する必要があります。
