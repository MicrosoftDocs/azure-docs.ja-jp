---
title: Media Services API V3 への移行の利点
description: この記事では、Media Services V2 から V3 に移行する利点について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: b6d51e05598f60de0e9c8fb85472b7c14bba990e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598390"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>ステップ 1 - Media Services API V3 に移行する利点を理解する

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-1.svg)

新機能、機能性、パフォーマンスの最適化は、現在の V3 API でのみ利用可能であるため、Azure Media Services V3 API のバージョン 2020-05-01 の使用を今すぐ開始して、利点を得ることをお勧めします。

ポータル、最新の SDK、最新の CLI、REST API の API バージョンを、正しいバージョン文字列に変更できます。

Media Services の V3 は大幅に改善されました。  

## <a name="benefits-of-media-services-v3"></a>Media Services v3 のメリット

| **V3 の機能** | **特長** |
| --- | --- |
| **Azure Portal** | |
| Azure portal の更新 | Azure portal が更新され、V3 API エンティティの管理が含まれるようになりました。 これにより、お客様は portal を使用して、ライブ ストリーミングの開始、V3 変換ジョブの送信、コンテンツ保護ポリシーの管理、エンドポイントのストリーミング、API アクセスの取得、リンクされたストレージ アカウントの管理、監視タスクの実行を行うことができます。 |
| **アカウントとストレージ** | |
| Azure のロールベースのアクセス制御 (RBAC) | お客様は、独自のロールを定義し、Media Services ARM API 内の各エンティティへのアクセスを制御できるようになりました。 これは、AAD アカウントによるリソースへのアクセスを制御するのに役立ちます。 |
| マネージド ID | マネージド ID を使用すると、開発者は、Azure AD で Azure リソースの ID を提供することにより、資格情報を管理する必要がなくなります。 マネージド ID の詳細については、[こちら](../../active-directory/managed-identities-azure-resources/overview.md)を参照してください。 |
| プライベート リンクのサポート | お客様は、VNet 上の PrivateEndpoint を介して、キー配信、LiveEvents、StreamingEndpoints の Media Services エンドポイントにアクセスします。 |
| [カスタマー マネージド キー](concept-use-customer-managed-keys-byok.md) または Bring Your Own Key (BYOK) のサポート | お客様は、Azure Key Vault 内のキーを使用して Media Services アカウント内のデータを暗号化できます。 |
| **アセット** | |
| 1 つの資産に、[ダイナミック パッケージ](dynamic-packaging-overview.md)および動的暗号化の設定が異なる複数の[ストリーミング ロケーター](streaming-locators-concept.md)を含めることができる。 | 各資産に対して許可されるストリーミング ロケーターは 100 に制限されます。 お客様は、資産にメディア コンテンツの単一のコピーを保存できますが、さまざまなストリーミング URL を、対象ユーザーに基づくさまざまなストリーミング ポリシーまたはコンテンツ保護ポリシーと共有できます。
| **ジョブの処理** ||
| V3 では、ファイル ベースのジョブ処理のための [変換](transforms-jobs-concept.md) の概念を導入。 | 変換を使用して、再利用可能な構成を構築し、Azure Resource Manager テンプレートを作成し、複数の顧客またはテナント間の処理の設定を分離することができます。 |
| ファイル ベースのジョブ処理で、入力として HTTP(S) URL を使用可能。 | Azure にコンテンツをあらかじめ格納しておく必要も、資産を作成する必要もありません。 |
| **ライブ イベント** ||
| Premium 1080p ライブ イベント | 新しいライブ イベント SKU を使用すると、お客様は、最大 1080p の解像度の出力でクラウド エンコードを取得できます。 |
| ライブ イベントでの新しい[低遅延](live-event-latency.md)ライブ ストリーミングのサポート。 | これにより、ユーザーは、この設定を有効にしていない場合と比べて、よりリアルタイムでライブ イベントを視聴できます。 |
| ライブ イベント プレビューで、 [ダイナミック パッケージ](dynamic-packaging-overview.md) と動的暗号化をサポート。 | これにより、プレビュー、DASH、HLS パッケージでコンテンツを保護することができます。 |
| プログラムに置き換わるライブ出力 | ライブ出力は、v2 API のプログラム エンティティよりも簡単に使用できます。 |
| ライブ イベントの RTMP 取り込みが改善され、より多くのエンコーダーをサポート | 安定性が向上し、ソース エンコーダーの柔軟性が提供されます。 |
| ライブ イベントを 24 時間 365 日ストリーミング可能。 | ライブ イベントをホストし、長期間にわたって対象ユーザーを引き付けておくことができます。 |
| ライブ イベントでのライブ トラスクリプト | ライブ トラスクリプトを使用すると、お客様は、ライブ イベントの配信中、自動的にリアルタイムで音声言語をテキストに文字起こしすることができます。 これにより、ライブ イベントのアクセシビリティが大幅に向上します。 |
| ライブ イベントでの[スタンバイ モード](live-events-outputs-concept.md#standby-mode) | スタンバイ状態のライブ イベントは、ライブ イベントを実行するよりもコストが低減されます。 これにより、お客様は、実行中の一連のライブ イベントを維持するよりも低コストで、数秒以内に開始できる一連のライブ イベントを維持できます。 スタンバイ ライブ イベントの値下げは、ほとんどのリージョンで 2021 年 2 月に、その他は 2021 年 4 月に有効になりなります。
|**コンテンツ保護** ||
| [コンテンツ保護](content-key-policy-concept.md) でマルチキー機能をサポート。 | お客様は、ストリーミング ロケーターで複数のコンテンツ暗号化キーを使用できるようになりました。 |
| **Monitoring** | |
| [Azure EventGrid](monitoring/reacting-to-media-services-events.md) 通知のサポート | EventGrid 通知の機能がより充実しています。 より多くの種類の通知、独自のアプリケーションで通知を受信するためのより広範な SDK サポートがあり、イベント ハンドラーとして機能できる既存の Azure サービスも増えています。 |
| [Azure portal での Azure Monitor のサポートと統合](monitoring/monitor-events-portal-how-to.md) | これにより、お客様は、Media Services アカウントのクォータの使用率、ストリーミング エンドポイントのリアルタイム統計、ライブ イベントの取り込みとアーカイブの統計を視覚化できます。 お客様は、アラートを設定し、リアルタイムのメトリック データに基づいて必要なアクションを実行できるようになりました。 |

## <a name="next-steps"></a>次のステップ

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]