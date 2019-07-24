---
title: Azure App Configuration のポイントインタイム スナップショット | Microsoft Docs
description: Azure App Configuration におけるポイントインタイム スナップショットの動作の概要
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 6238f96c9e8df0431e42caa5e5846af3fc60e681
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484017"
---
# <a name="point-in-time-snapshot"></a>ポイントインタイム スナップショット

Azure App Configuration は、新しいキーと値のペアが作成されたときとその後変更されたときの正確な時間を記録しています。 これらのレコードは、キー/値の変化の完全なタイムラインを形成します。 アプリ構成ストアは、あらゆるキー/値の履歴を再構築して、過去から現在に至るまでの任意の時点の値を再現することができます。 この機能により、過去にさかのぼって以前のキー/値を取得することができます。 たとえば、以前の構成を復旧してアプリケーションをロールバックするために、直近のデプロイより前の昨日の構成設定を取得できます。

## <a name="key-value-retrieval"></a>キー/値の取得

過去のキー/値を取得するには、そのスナップショットが作成された日時を REST API 呼び出しの HTTP ヘッダーに指定します。 例: 

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

現在、App Configuration では 7 日分の変更履歴が保持されます。

## <a name="next-steps"></a>次の手順

* [クイック スタート:ASP.NET Web アプリを作成する](quickstart-aspnet-core-app.md)  
