---
title: "新しいログ検索への Azure Log Analytics のアップグレード | Microsoft Docs"
description: "新しい Log Analytics クエリ言語の準備がほぼ整ったので、パブリック プレビューに参加できます。  この記事では、新しい言語の利点、およびワークスペースを変換する方法について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 4a3ee3c4d1fa9b626a51f24997603adceed8311f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="upgrade-your-azure-log-analytics-workspace-to-new-log-search"></a>新しいログ検索への Azure Log Analytics ワークスペースのアップグレード

> [!NOTE]
> 新しい Log Analytics クエリ言語へのアップグレードは現時点ではオプションであるため、[新しい言語を導入](https://go.microsoft.com/fwlink/?linkid=856078)する前に、ある程度の時間を確保できます。  

新しい Log Analytics クエリ言語の準備ができました。これを利用するには、ワークスペースをアップグレードする必要があります。  この記事では、新しい言語の利点、およびワークスペースを変換する方法について説明します。  今すぐアップグレードしなければ、ワークスペースはこれまでと同じように引き続き動作しますが、後日、自動的に変換されます。  それまでかなりの時間が確保されます。日程が決まった時点で、通知が届きます。

この記事では、新しい言語とアップグレード プロセスについて詳しく説明します。

## <a name="why-the-new-language"></a>新しい言語を使用する理由
移行が面倒であることは承知していますし、好き好んでクエリ言語を変更しているわけではありません。  この変更が Log Analytics のユーザーに大きな価値がもたらす理由は複数あります。

- **シンプルながらもパワフル。** 新しい言語は従来の言語よりもわかりやすく、より自然言語に近いコンストラクトを備え、SQL に類似しています。
- **完全なパイプ処理言語。**  新しい言語は、従来の言語より広範なパイプ処理機能を備えています。  事実上すべての出力を他のコマンドにパイプ処理して、以前より複雑なクエリを作成できます。
- **検索時間フィールドの抽出。**  新しい言語は、従来の言語より高度なランタイム計算フィールドをサポートしています。  拡張フィールドで複雑な計算を使用し、結合、集計などの追加コマンドに対して計算フィールドを使用することができます。
- **高度な結合。**  新しい言語では、従来の言語より高度な結合が用意されています。たとえば、複数フィールドでのテーブルの結合、内部結合や外部結合の使用、拡張フィールドでの結合が可能です。
- **日付/時刻の関数。**  新しい言語は、従来の言語より高度な日付/時刻関数を備えています。
- **スマート分析。**  新しい言語には、データセット内のパターンを評価し、さまざまなデータセットを比較する高度なアルゴリズムが用意されています。
- **高度な分析ポータル。**  高度な分析ポータルでは、複数行でのクエリの編集、追加の視覚化、高度な診断など、Log Analytics ポータルでは使用できない分析機能を使用できます。
- **他のアプリケーションとの一貫性。**  新しい言語と高度な分析ポータルは、既に Application Insights での分析に使用されています。  これを Log Analytics 用に実装することで、Azure サービス全体で一貫性が確保されます。
- **改善された Power BI との統合。** 新しい言語のクエリを Power BI Desktop にエクスポートして、豊富なデータ変換機能を活用できます。
- **その他。** 新しい言語の詳細とチュートリアルについては、「[Azure Log Analytics Query Language (Azure Log Analytics クエリ言語)](https://docs.loganalytics.io)」のサイトを参照してください。


## <a name="when-can-i-upgrade"></a>アップグレードのタイミング
アップグレードは、すべての Azure リージョンでロールアウトされるため、一部のリージョンが他のリージョンよりも早く入手可能になる場合があります。  ワークスペースがアップグレードできるようになると、ワークスペース上部にアップグレードを促す紫のバナーが表示されます。

![アップグレード 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

## <a name="what-happens-when-i-upgrade"></a>アップグレード後の動作
ワークスペースを変換すると、保存したすべての検索、警告ルール、およびビュー デザイナーで作成したビューが自動的に新しい言語に変換されます。  ソリューションに含まれる検索は自動的には変換されませんが、検索を開いた時点ですぐに変換されます。  この処理は、ユーザーからは見えないところで実行されます。

## <a name="can-i-go-back-after-i-upgrade"></a>アップグレード後の復元
アップグレードするときに、ワークスペースの完全なバックアップが作成されます。このバックアップには、保存したすべての検索、警告ルール、およびビューのスナップショットが含まれます。  これにより、後で必要になったときに、古いワークスペースを復元できます。

従来のワークスペースを復元するには、ワークスペースの **[設定]**、**[アップグレードの概要]** の順に移動します。  その後、**[Restore legacy workspace]\(従来のワークスペースの復元\)** オプションを選択できます。  

![従来のワークスペースの復元](media/log-analytics-log-search-upgrade/restore-legacy-b.png)

## <a name="how-do-i-perform-the-upgrade"></a>アップグレードの実行方法
ポータル上部に紫のバナーが表示されたら、ワークスペースをアップグレードできます。  

1.  **[詳細を表示して、アップグレードする]** と表示されている紫のバナーをクリックして、アップグレード プロセスを開始します。<br>![アップグレード 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>
2.  アップグレード情報ページでアップグレードに関する追加情報を確認します。<br>![アップグレード 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>
3.  **[今すぐアップグレード]** をクリックして、アップグレードを開始します。<br>![アップグレード 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>右上隅にある通知ボックスに状態が表示されます。<br>![アップグレード 5](media/log-analytics-log-search-upgrade/upgrade-05.png)
4.  これで終了です。  [ログ検索] ページに移動して、アップグレードされたワークスペースを確認します。<br>![アップグレード 6](media/log-analytics-log-search-upgrade/upgrade-06.png)<br>

問題が発生してアップグレードに失敗した場合は、[ディスカッション フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)に移動して質問を投稿するか、Azure Portal から[サポート要求を作成](../azure-supportability/how-to-create-azure-support-request.md)できます。

## <a name="how-do-i-learn-the-new-language"></a>新しい言語を学習する方法
新しい言語は複数のサービスによって使用されているため、新しい言語に関する[ドキュメントをホストする外部サイト](https://docs.loganalytics.io/)を作成しました。  このサイトには、新しい言語を理解するうえで役立つチュートリアル、サンプル、および完全なリファレンスが含まれています。 新しい言語のチュートリアルは「[Getting Started with Queries (クエリの概要)](https://go.microsoft.com/fwlink/?linkid=856078)」で実行できます。また、[Log Analytics クエリ言語](https://go.microsoft.com/fwlink/?linkid=856079)では言語リファレンスにアクセスできます。  

ただし、従来の Log Analytics クエリ言語に既に慣れている場合は、アップグレードの一部としてワークスペースに追加される言語コンバーターを使用できます。

従来のクエリを入力して、**[変換]** をクリックするだけで、変換後のバージョンが表示されます。  次に、検索ボタンをクリックして検索を実行するか、変換後のクエリをコピーし、警告ルールなど、他の場所で使用できるように貼り付けます。

![言語コンバーター](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>次のステップ
- [新しい言語のチュートリアル](https://go.microsoft.com/fwlink/?linkid=856078)を確認します。
- 新しいクエリ言語で[ログ検索ポータルを使用したチュートリアル](log-analytics-log-search-log-search-portal.md)を実行します。
- 新しい[高度な分析ポータル](https://go.microsoft.com/fwlink/?linkid=856587)の概要を確認します。

