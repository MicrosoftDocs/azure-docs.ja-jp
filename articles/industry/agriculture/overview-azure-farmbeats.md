---
title: Azure FarmBeats の概要
description: Azure FarmBeats の概要について説明します
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: f173dfb055f3d9c75ee63570cd413276ffb6ba37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182827"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Azure FarmBeats (プレビュー) の概要

Azure FarmBeats は、Azure Marketplace で入手できる企業間オファリングです。 これにより、複数のプロバイダーにわたる農業データ セットを集約できます。 Azure FarmBeats を使用すると、合成したデータ セットに基づいて人工知能 (AI) または機械学習 (ML) モデルを構築できます。 Azure FarmBeats を使用することによって、農業関連産業は、Data Engineering という差別化につながらない煩雑な作業ではなく、核となる付加価値に注力できます。

> [!NOTE]
> 現在、Azure FarmBeats はパブリック プレビュー段階にあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 Azure FarmBeats は、サービス レベル アグリーメントなしに提供されます。 サポートについては、[Azure FarmBeats サポート フォーラム](/answers/topics/azure-farmbeats.html)をご利用ください。

![プロジェクト (Farm Beats)](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Azure FarmBeats プレビューでは、次のことができます。

- 衛星画像に基づく植生指数と水指数を使用して、ファームの正常性を評価します。
- 使用する土壌水分センサーの数や配置場所についての推奨情報を取得します。
- さまざまなベンダーのセンサーによって収集された土地のデータを視覚化して、ファームの状態を追跡します。
- 衛星およびセンサー データの合成に基づいて、土壌水分マップを取得します。
- 集約されたデータセットの上に AI/ML モデルを構築して、アクションにつながる分析情報を得ます。
- ファームの正常性の勧告を提供して、デジタル農業ソリューションを構築または強化します。

## <a name="datahub"></a>データ ハブ

Azure FarmBeats Datahub は、プロバイダー間でのさまざまな農業データセットの集計、正規化、コンテキスト化を可能にする API レイヤーです。 Azure FarmBeats を使用すると、次のものを取得できます。
- 2 つのセンサー プロバイダー ([Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/)、[Teralytic](https://teralytic.com/)、[Pessl Instruments](https://metos.at/)) からの **センサー データ**
- 欧州宇宙機関の [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) 衛星ミッションからの **衛星画像**
- 3 つのドローン映像プロバイダー ([senseFly](https://www.sensefly.com/)、[SlantRange](https://slantrange.com/)、[DJI](https://dji.com/)) からの **ドローン映像**

Datahub は、拡張可能な API プラットフォームとして設計されています。 Microsoft では、さらに多くのプロバイダーと連携して Azure FarmBeats との統合を進めているため、お客様がソリューションを構築する際には選択肢がさらに多くなります。

## <a name="accelerator"></a>アクセラレータ

Azure FarmBeats アクセラレータは、Datahub 上に構築されるサンプル Web アプリケーションです。 アクセラレータにより、ユーザー インターフェイスとモデルの開発を即座に開始できます。 Azure FarmBeats アクセラレータでは、Azure FarmBeats の API シリーズを使用します。 アクセラレータは、取り込んだセンサー データをグラフとして視覚化し、モデル出力をマップとして視覚化します。 たとえば、アクセラレータを使用すると、ファームをすばやく作成し、そのファームの植生指数マップやセンサー配置マップを簡単に取得できます。

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure ロールベースのアクセス制御 (Azure RBAC)

管理者は、事前に定義されたロールのいずれかを使用して Azure FarmBeats のアクセス ルールを定義できます。 ユーザーにアクセスが許可されるアプリケーションの領域とユーザーが実行できるアクションがロールにより決定されます。 Azure FarmBeats には、ユーザー向けとパートナー向けの 2 種類の役割があります。

### <a name="user-roles"></a>ユーザー ロール

[管理者がユーザーを追加して管理](manage-users-in-azure-farmbeats.md)できます。ユーザーのアクセス レベルは、管理と読み取り専用という 2 つのユーザー ロールに基づいて定義できます。

### <a name="partner-roles"></a>パートナーのロール

管理者は、データ プロバイダーとして複数のパートナーを Azure FarmBeats に追加できます。 次の表は、FarmBeats で使用できるパートナー ロールとそのアクセス許可をまとめたものです。

| パートナーの種類    |   Actions  | Scope |
| ---- | -------- | -------- |
| センサー パートナー  |   作成、読み取り、更新 <br/> <br/> 読み取り、更新 | DeviceModel、Device、SensorModel、Sensor <br/> <br/> ExtendedType |
| 画像パートナー  |   作成、読み取り、更新 <br/> <br/> 読み取り、更新 <br/> <br/> Read | Scene、SceneFile <br/> <br/> ExtendedType <br/> <br/> Farm |
| 天気パートナー <br/> <br/>  (* 近日対応予定) |   作成、読み取り、更新 <br/> <br/> 読み取り、更新 <br/> <br/> Read | WeatherDataModel、WeatherDataLocation、JobType <br/> <br/> ExtendedType <br/> <br/> Farm |

## <a name="resources"></a>リソース

Azure FarmBeats は、追加料金なしで提供されます。お支払いいただくのは、ご利用の Azure リソース分のみです。 オファリングの詳細については、以下のリソースを参照してください。

- [Azure FarmBeats ブログ](https://aka.ms/farmbeatsblog)では、Azure FarmBeats に関する最新の情報を入手できます。
- [Azure FarmBeats サポート フォーラム](/answers/topics/azure-farmbeats.html)に質問を投稿して、助言を求めることができます。
- [Azure FarmBeats フィードバック フォーラム](https://aka.ms/farmbeatsfeedback)で機能のアイデアについて投稿または投票して、意見をフィードバックすることができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure FarmBeats をインストールする](install-azure-farmbeats.md)