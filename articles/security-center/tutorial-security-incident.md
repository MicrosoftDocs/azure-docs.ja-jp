---
title: Azure Security Center チュートリアル - セキュリティ インシデントへの対応 | Microsoft Docs
description: Azure Security Center チュートリアル - セキュリティ インシデントへの対応
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: d726006d3ecce69f129b1576c7c6d12833582873
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58081992"
---
# <a name="tutorial-respond-to-security-incidents"></a>チュートリアル:セキュリティ インシデントへの対応
Security Center では、高度な分析および脅威インテリジェンスを使用してハイブリッド クラウド ワークロードを継続的に分析し、悪意のあるアクティビティに関するアラートを受け取ることができます。 さらに、他のセキュリティ製品およびサービスからのアラートを Security Center に統合し、独自のインジケーターまたはインテリジェンス ソースに基づいたカスタム アラートを作成できます。 アラートが生成されたら、調査と修復を行うために迅速なアクションが必要になります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * セキュリティ アラートのトリアージ
> * セキュリティ インシデントの根本原因とスコープを特定するための追加の調査
> * 調査に役立つセキュリティ データの検索

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルで説明されている機能を実行するには、Security Center の Standard 価格レベルを使用する必要があります。 Security Center Standard は無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。 Standard にアップグレードする方法については、[Azure サブスクリプションでの Security Center Standard の利用開始](security-center-get-started.md)に関するクイックスタートを参照してください。

## <a name="triage-security-alerts"></a>セキュリティ アラートのトリアージ
Security Center では、すべてのセキュリティ アラートを統合された 1 つのビューで確認できます。 セキュリティ アラートは、潜在的な関連アラートがセキュリティ インシデントにまとめられたタイミングと重大度に基づいてランク付けされます。 アラートとインシデントのトリアージでは、以下を行う必要があります。

- 追加のアクションが必要でないアラートを無視する (例: アラートが誤検知の場合)
- 既知の攻撃に対する修復を行う (例: 悪意のある IP アドレスからのネットワーク トラフィックのブロック)
- 追加の調査が必要なアラートを特定する


1. Security Center のメイン メニューの **[検出]** で、**[セキュリティ アラート]** を選択します。

   ![セキュリティのアラート](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)  

2. アラートの一覧でセキュリティ インシデント (アラートのコレクション) をクリックして、そのインシデントについて詳しく確認します。 **[Security incident detected]\(セキュリティ インシデントが検出されました\)** が開きます。

   ![セキュリティ インシデント](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. この画面では、セキュリティ インシデントの説明が上部に表示されるほか、このインシデントに含まれているアラートの一覧が表示されます。 詳しく調査したいアラートをクリックして、詳細を表示します。

   ![セキュリティ インシデント](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   アラートの種類はさまざまです。アラートの種類と実行可能な修復の手順について詳しくは、「[Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)」を参照してください。 安心して無視できるアラートについては、そのアラートを右クリックして **[無視]** オプションを選択できます。

   ![アラート:](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. 悪意のあるアクティビティの根本原因とスコープが不明な場合、さらに調査するために次の手順に進みます。

## <a name="investigate-an-alert-or-incident"></a>アラートまたはインシデントの調査
1. **[セキュリティの警告]** ページで **[調査の開始]** ボタンをクリックします (既に開始している場合、名前は **[調査の続行]** になります)。

   ![調査](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   調査マップは、このセキュリティ アラートまたはインシデントに関連付けられたエンティティをグラフで表示したものです。 マップ内のエンティティをクリックすると、そのエンティティに関する情報として新しいエンティティが表示され、マップが拡大します。 マップ内のエンティティを選択すると、そのプロパティがページの右側にあるウィンドウに強調表示されます。 各タブで利用できる情報は、選択したエンティティによって異なります。 調査プロセス中は、攻撃者の行動に対する理解を深めるためにすべての関連情報を確認してください。

2. さらに証拠が必要な場合、または調査中に見つかったエンティティについてさらに調査する必要がある場合、次の手順に進みます。

## <a name="search-data-for-investigation"></a>調査用のデータの検索

Security Center の検索機能を使用して、システムの侵害に関する他の証拠や、調査対象のエンティティの詳しい情報を見つけることができます。

検索を実行するには、**Security Center** ダッシュボードを開きます。左側のナビゲーション ウィンドウにある **[検索]** をクリックし、検索したいエンティティが含まれたワークスペースを選択してから、検索クエリに入力して検索ボタンをクリックします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 引き続き次のクイックスタートとチュートリアルを行う予定の場合、Standard レベルの実行を継続して、自動プロビジョニングを有効のままにしてください。 続行しないまたは Free レベルに戻したい場合:

1. Security Center のメイン メニューに戻り、**[セキュリティ ポリシー]** を選択します。
2. Free に戻したいサブスクリプションまたはポリシーを選択します。 **[セキュリティ ポリシー]** が開きます。
3. **[ポリシー コンポーネント]** で、**[価格レベル]** を選択します。
4. **[Free]** を選択して、Standard レベルから Free レベルにサブスクリプションを変更します。
5. **[保存]** を選択します。

自動プロビジョニングを無効にする場合:

1. Security Center のメイン メニューに戻り、**[セキュリティ ポリシー]** を選択します。
2. 自動プロビジョニングを無効にするサブスクリプションを選択します。
3. **[セキュリティ ポリシー - データ収集]** で、**[オンボード]** の **[オフ]** を選択して、自動プロビジョニングを無効にします。
4. **[保存]** を選択します。

>[!NOTE]
> 自動プロビジョニングを無効しても、Microsoft Monitoring Agent がプロビジョニングされている Azure VM からエージェントは削除されません。 自動プロビジョニングを無効にすると、リソースのセキュリティの監視が制限されます。
>

## <a name="next-steps"></a>次の手順
このチュートリアルでは、セキュリティ インシデントに対応する際に使用される Security Center の機能について説明しました。例:

> [!div class="checklist"]
> * リソースに関するアラートを集約したセキュリティ インシデント
> * セキュリティ アラートまたはインシデントに関連付けられたエンティティをグラフで表示する調査マップ
> * システムの侵害に関する他の証拠を見つけるための検索機能

Security Center の調査機能については、次を参照してください。

> [!div class="nextstepaction"]
> [インシデントとアラートの調査](security-center-investigation.md)
