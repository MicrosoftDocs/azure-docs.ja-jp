---
title: リスク評価レポートの作成
description: 個々のセンサーによって検出されたネットワーク リスクや、すべてのセンサーによって検出されたリスクの集計ビューを把握できます。
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784256"
---
# <a name="risk-assessment-reporting"></a>リスク評価レポートの作成

## <a name="about-risk-assessment-reports"></a>リスク評価レポートについて

リスク評価レポートには次の情報が含まれます。

- 組織のセンサーによって検出されたデバイスの全体的なセキュリティ スコア。

- 個々のセンサーによって検出された各ネットワーク デバイスのセキュリティ スコア。

- 脆弱なデバイス、改善が必要なデバイス、およびセキュリティで保護されたデバイスの数の内訳。

-  セキュリティおよび運用上の問題に関する分析情報:

    - 構成の問題

    - セキュリティ レベルによって優先順位が付けられたデバイスの脆弱性

    - ネットワーク セキュリティの問題

    - ネットワークの運用上の問題

    - ICS ネットワークへの接続

    - Internet connections (インターネット接続)

    - 産業用マルウェアのインジケーター

    - プロトコルの問題

    - 攻撃ベクトル

### <a name="risk-mitigation"></a>リスクの軽減

レポートには、セキュリティ スコアの向上に役立つ推奨事項が記載されています。 たとえば、最新のセキュリティ更新プログラムをインストールする、ファームウェアを最新バージョンにアップグレードする、アラートをフォローアップするなどです。

## <a name="about-security-scores"></a>セキュリティ スコアについて

各レポートには、全体的なネットワーク セキュリティ スコアが生成されます。 スコアは、100% のセキュリティに対する割合を表します。 たとえば、スコアが 30% の場合は、ネットワークが 30% 安全であることを示します。

リスク評価スコアは、パケット インスペクション、行動モデリング エンジン、および SCADA 固有のステート マシン設計から得られた情報に基づきます。

**セキュリティで保護されたデバイス** は、セキュリティ スコアが 90% 以上のデバイスです。

**改善が必要なデバイス**: セキュリティ スコアが 70% から 89% のデバイスです。

**脆弱なデバイス** は、セキュリティ スコアが 70% 未満のデバイスです。

## <a name="create-risk-assessment-reports"></a>リスク評価レポートの作成

PDF のリスク評価レポートを作成します。 レポート名は、risk-assessment-report-1.pdf のように自動的に生成されます。 数字は、新しいレポートを作成するたびに更新されます。  作成日時が表示されます。

### <a name="create-a-sensor-risk-assessment-report"></a>センサー リスク評価レポートの作成

ログインしているセンサーによる検出に基づいて、リスク評価レポートを作成します。

レポートを作成するには:

1. センサー コンソールにログインします。
1. サイドメニューから **[Risk Assessment]\(リスク評価\)** を選択します。
1. **[Generate Report]\(レポートの生成\)** を選択します。 レポートは、[Archived Reports]\(アーカイブされたレポート\) セクションに表示されます。
1. [Archived Reports]\(アーカイブされたレポート\) セクションからレポートを選択してダウンロードします。

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="リスク評価のビュー。":::

会社のロゴをインポートするには、次のようにします。

- **[Import Logo]\(ロゴのインポート\)** を選択します。

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>オンプレミスの管理コンソールのリスク評価レポートの作成

オンプレミスの管理コンソールで管理されているセンサーのいずれかによって行われた検出に基づいて、リスク評価レポートを作成します。 

レポートを作成するには:

1. サイドメニューから **[Risk Assessment]\(リスク評価\)** を選択します。

2. **[Select sensor]\(センサーの選択\)** ドロップダウン リストからセンサーを選択します。

3. **[Generate Report]\(レポートの生成\)** を選択します。

4. **[Archived Reports]\(アーカイブされたレポート\)** セクションから、 **[Download]\(ダウンロード\)** を選択します。

会社のロゴをインポートするには、次のようにします。

- **[Import Logo]\(ロゴのインポート\)** を選択します。

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="[リスク評価] ビューでロゴをインポートする。":::

## <a name="see-also"></a>関連項目

[攻撃ベクトル レポートの作成](how-to-create-attack-vector-reports.md)

