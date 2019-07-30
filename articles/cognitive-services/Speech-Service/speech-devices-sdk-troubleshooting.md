---
title: Speech Devices SDK のトラブルシューティング - Speech Services
titleSuffix: Azure Cognitive Services
description: この記事では、Speech Devices SDK を使用しているときに発生する可能性がある問題の解決に役立つ情報を提供します。
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606293"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Speech Devices SDK のトラブルシューティング

この記事では、Speech Devices SDK を使用しているときに発生する可能性がある問題の解決に役立つ情報を提供します。

## <a name="certificate-failures"></a>証明書エラー

Speech Services を使用している場合に証明書エラーが発生した場合は、デバイスの日付と時刻が正しいことを確認します。

1. **[設定]** に移動します。 **[システム]** で、 **[日付と時刻]** を選択します。

    ![[設定] で、[日付と時刻] を選択する](media/speech-devices-sdk/qsg-12.png)

1. **[Automatic date & time]** (自動の日付と時刻) オプションは選択したままにします。 **[タイム ゾーンの選択]** で、現在のタイム ゾーンを選択します。

    ![日付とタイム ゾーンのオプションを選択する](media/speech-devices-sdk/qsg-13.png)

    開発キットの時刻が PC の時刻と一致している場合、開発キットはインターネットに接続されています。

## <a name="next-steps"></a>次の手順

* [リリース ノートを確認します](devices-sdk-release-notes.md)
