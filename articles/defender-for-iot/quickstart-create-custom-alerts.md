---
title: カスタム アラートの作成
description: Azure Defender for IoT セキュリティ サービスのカスタム デバイス アラートについて理解し、それらの作成と割り当てができるようにします。
ms.topic: quickstart
ms.date: 09/04/2020
ms.openlocfilehash: 836619fa200983bc91d26db4d063e8545b11a1b5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781043"
---
# <a name="create-custom-alerts"></a>カスタム アラートの作成

カスタムのセキュリティ グループおよびアラートを使用することで、エンド ツー エンドのセキュリティ情報とカテゴリ別のデバイス知識を最大限に活用し、IoT ソリューション全体のセキュリティを確実に向上させます。

## <a name="why-use-custom-alerts"></a>カスタム アラートを使用する理由

お客様の IoT デバイスについて最もよく知っているのはお客様自身です。

予想されるデバイスの動作を完全に理解しているお客様のために、Defender for IoT には、その理解をデバイスの動作ポリシーに変換し、予想される通常の動作からの逸脱に基づいてアラートする機能があります。

## <a name="security-groups"></a>セキュリティ グループ

セキュリティ グループでは、デバイスの論理グループを定義して、それらのセキュリティ状態を一元的に管理できます。

これらのグループは、特定のハードウェアを備えたデバイス、一定の場所にデプロイされたデバイス、または特定のニーズに適したその他のグループを表す場合があります。

セキュリティ グループは、**SecurityGroup** という名前のデバイス ツイン タグ プロパティによって定義されます。 既定では、IoT Hub 上の各 IoT ソリューションに、**default** という名前のセキュリティ グループが 1 つあります。 **SecurityGroup** プロパティの値を変更して、デバイスのセキュリティ グループを変更します。

次に例を示します。

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

セキュリティ グループを使用して、デバイスを論理カテゴリにグループ化します。 グループの作成後、それらを任意のカスタム アラートに割り当てて、最も効果的なエンド ツー エンド IoT セキュリティ ソリューションを実現します。

## <a name="customize-an-alert"></a>アラートのカスタマイズ

1. ご利用の IoT Hub を開き、 **[セキュリティ]** メニューから **[設定]** メニューを選択します。

1. **[カスタム アラート]** を選択します。

1. カスタマイズの適用先にしたいセキュリティ グループを選択します。

1. **[Add a custom alert]\(カスタム アラートの追加\)** を選択します。

1. ドロップダウン リストからカスタムのアラートを選択します。

1. 必須のプロパティを編集して、 **[OK]** を選択します。

1. 必ず **[SAVE]\(保存\)** を選択してください。 新しいアラートは、保存しないと IoT ハブを次に閉じたときに削除されます。

## <a name="alerts-available-for-customization"></a>カスタマイズに使用できるアラート

Defender for IoT には、アラートが多数用意されており、特定のニーズに応じてカスタマイズできます。 アラートの重要度、データ ソース、説明、および各アラートを受信した場合の推奨される修復手順については、[カスタマイズ可能なアラートの表](concept-customizable-security-alerts.md)を確認してください。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、セキュリティ エージェントのデプロイ方法を確認してください。

> [!div class="nextstepaction"]
> [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)
