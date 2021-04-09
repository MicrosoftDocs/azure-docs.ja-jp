---
title: Azure Marketplace での AppSource Dynamics 365 Finance and Operations プランの機能検証。
description: Azure Marketplace で Dynamics 365 Finance and Operations プランを機能的に検証する方法。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: 9be90cdac742a581c6346f923f44e769c8a70f76
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613631"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>AppSource Dynamics 365 Finance and Operations の機能検証

[パートナー センター](https://partner.microsoft.com/dashboard/home)での最初の発行を完了するには、Dynamics 365 Finance and Operations のプランについて、次の 2 つの機能検証を行う必要があります。

- 基本的な機能を示す Dynamics 365 環境のデモ ビデオをアップロードします。
- ソリューションの [Lifecycle Services](https://lcs.dynamics.com/) (LCS) 環境を示すスクリーンショットを提出します。

> [!NOTE]
> 以降の再認証の発行では、デモは必要ありません。 詳細については、[AppSource Policy のドキュメント](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops)を参照してください。

## <a name="how-to-validate"></a>検証方法

機能検証には、次の 2 つのオプションがあります。

- 太平洋標準時 (PST) の営業時間中に 30 分間の電話会議を開催して、[LCS](https://lcs.dynamics.com/) 環境とソリューションのデモンストレーションと記録を行います。
- パートナー センターで、[[コマーシャル マーケットプレース]](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) >  **[概要]** にアクセスし、プランの [Supplemental Content]\(補足コンテンツ\) タブでデモ ビデオ (URL から) と LCS のスクリーンショットをアップロードします。

Microsoft 認定チームはビデオとファイルをレビューしてから、ソリューションを承認するか、次の手順について電子メールで通知します。

### <a name="option-1-30-minute-conference-call"></a>オプション 1: 30 分間の電話会議

最終レビューの電話会議をスケジュールするには、[appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) にお問い合わせください。プランの名前と午前 8 時から午後 5 時 (太平洋標準時) の間のご希望の時間帯を記載してください。

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>オプション 2:デモ ビデオと LCS スクリーンショットをアップロードする

1. ビデオを記録し、選択したホスティング サイトにそのアドレスをアップロードします。 次のガイドラインに従ってください。

    - Microsoft 認定チームが参照可能であること。
    - 20 分未満。
    - Dynamics 365 環境内のソリューションの最大 3 つのコア機能のハイライトが含まれていること。

    > [!NOTE]
    > ガイドラインを満たしている場合は、既存のマーケティング ビデオを使用することができます。

2. 発行するプランまたはソリューションに一致する [LCS](https://lcs.dynamics.com/) 環境の次のスクリーンショットを取得します。 これらのスクリーンショットは十分に明瞭で、認証チームがテキストを読み取れる必要があります。 スクリーンショットを JPG ファイルとして保存します。 スクリーンショットを提供する代わりに、[appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) に LCS 環境へのアクセス許可を提供して、弊社がセットアップを確認できるようにすることができます。

    1. **[LCS]**  >  **[ビジネス プロセス モデラー]**  >  **[プロジェクト ライブラリ]** に移動します。 すべての [プロセス] のステップのスクリーンショットを取得します。 次に示すように、 **[ダイアグラム]** 列と **[確認済]** 列を含めます。

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="プロジェクト ライブラリ ウィンドウを表示します。":::

      2. **[LCS]**  >  **[ソリューション管理]**  >  **[ソリューション パッケージのテスト]** に移動します。 次の例に示すように、パッケージの概要と内容を含むスクリーンショットを取得します。

    | フィールド | Image |
    | --- | --- |
    | パッケージの概要 | [![[パッケージの概要] ウィンドウを示すスクリーンショット。](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>ソリューションの承認者</li></ul> | [![[パッケージの概要] 画面](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | パッケージの内容<ul><li>モデル</li><li>ソフトウェア配置可能パッケージ</li></ul> | [![[パッケージの内容] 画面 1](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>GER コンフィギュレーション</li><li>データベース バックアップ</li></ul><br>**[GER コンフィギュレーション]** セクションには、コンポーネントは必要ありません。 | [![[パッケージの内容] 画面 2](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI レポート モデル</li><li>BPM コンポーネント</li></ul><br>**Power BI** セクションには、コンポーネントは必要ありません。 | [![[パッケージの内容] 画面 3](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>プロセス データ パッケージ</li><li>ソリューション ライセンス契約とプライバシー ポリシー</li></ul><br>**[GER コンフィギュレーション]** と **[Power BI レポート モデル]** セクションは、必要に応じて Finance and Operations プランに含めることができます。 | [![[パッケージの内容] 画面 4](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    LCS ポータルの各セクションの詳細については、「[LCS ユーザー ガイド](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide)」を参照してください。

3. パートナー センターにアップロードします。

    1. デモ ビデオのアドレスとスクリーン ショットを含むテキスト ドキュメントを作成するか、スクリーンショットを個別の JPG ファイルとして保存します。
    2. パートナー センターの [[コマーシャル マーケットプレース]](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) の Finance and Operations プランの **[Supplemental Content]** \(補足コンテンツ\) タブで、テキストと JPG ファイルを追加します。

    [![プロジェクト ライブラリ ウィンドウを表示します](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>次のステップ

プランの作成の詳細については、「[Dynamics 365 for Operations オファーの作成](./partner-center-portal/create-new-operations-offer.md)」を参照してください。