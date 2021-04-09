---
title: 脅威インテリジェンス データを更新する
description: 脅威インテリジェンス データ パッケージは、新しい Defender for IoT バージョンごとに、またはリリース間で必要に応じて提供されます。
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 386d59d33c4f9695b8fc5243dab345321cff4a5f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784052"
---
# <a name="threat-intelligence-research-and-packages"></a>脅威インテリジェンスの調査とパッケージ

Microsoft のセキュリティ チームは、独自の ICS 脅威インテリジェンスおよび脆弱性調査を実施しています。 これらのチームには、MSTIC (Microsoft Threat Intelligence Center)、DART (Microsoft Detection and Response Team)、DCU (Digital Crimes Unit)、Section 52 (ICS 固有のゼロデイ、リバースエンジニアリングのマルウェア、キャンペーン、および敵対者を追跡する IoT/OT/ICS ドメインの専門家) が含まれています。

チームは、Microsoft の次の分野に対してセキュリティの検出、分析、および対応を提供します。

- クラウド インフラストラクチャとサービス。
- 従来の製品とデバイス。
- 社内リソース。

セキュリティ チームは、次の利点を得ています。

- 既知のおよび関連する脅威からの保護。
- トリアージと優先度付けに役立つ分析情報。
- 影響を受ける前に、脅威の完全なコンテキストに対する理解。
- 関連性、正確性、実用性がより高いデータ。

このインテリジェンスにより、Microsoft プラットフォーム分析を強化するためのコンテキスト情報が追加され、インシデント対応および侵害を調査するために会社の管理サービスがサポートされます。 脅威インテリジェンス パッケージには、署名 (マルウェアの署名を含む)、CVE、およびその他のセキュリティ コンテンツが含まれています。

パッケージは、Azure Defender for IoT ポータルの **[更新プログラム]** ページからダウンロードできます。

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Azure Defender for IoT ポータルから更新プログラムをダウンロードします。":::

## <a name="update-threat-intelligence-data"></a>脅威インテリジェンス データを更新する

脅威インテリジェンス パッケージは、新しい Defender for IoT バージョン更新プログラムごとに、またはリリース間で必要に応じて提供されます。

Defender for IoT ポータルからダウンロードしたパッケージは、個々のセンサーに手動でアップロードできます。 オンプレミスの管理コンソールでセンサーを管理している場合は、脅威インテリジェンス パッケージを管理コンソールにダウンロードし、複数のセンサーに同時にプッシュすることができます。

1 つのセンサーでパッケージを更新するには:

1. Azure Defender for IoT の **[更新プログラム]** ページに移動します。

2. **脅威インテリジェンス** パッケージをダウンロードして保存します。

3. センサー コンソールにサインインします。

4. サイド メニューで、 **[システム設定]** を選択します。

5. **[脅威インテリジェンス データ]** を選択し、 **[更新]** を選択します。

6. 新しいパッケージをアップロードします。

複数のセンサーのパッケージを同時に更新するには:

1. Azure Defender for IoT の **[更新プログラム]** ページに移動します。

2. **脅威インテリジェンス** パッケージをダウンロードして保存します。

3. 管理コンソールにサインインします。

4. サイド メニューで、 **[システム設定]** を選択します。

5. **[センサー エンジン構成]** セクションで、更新されたパッケージを受け取るセンサーを選択します。  

6. **[脅威インテリジェンス データの選択]** セクションで、プラス記号 ( **+** ) を選択します。

7. パッケージをアップロードします。

## <a name="see-also"></a>関連項目

[バージョンの更新](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
