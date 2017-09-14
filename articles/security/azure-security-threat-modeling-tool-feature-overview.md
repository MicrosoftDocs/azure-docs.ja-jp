---
title: Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: "Threat Modeling Tool で使用できるすべての機能について説明します"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 621ff305d7e782f85eeaae6c3fb02031673549c6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="threat-modeling-tool-feature-overview"></a>Threat Modeling Tool 機能の概要

脅威モデリングのニーズに合わせて、Threat Modeling Tool をお選びいただきありがとうございます。 ツールの基本については、「**[Getting Started with the Threat Modeling Tool](./azure-security-threat-modeling-tool-getting-started.md)**」(Threat Modeling Tool の概要) を参照してください。

> このツールはよく更新されるため、このガイドに頻繁にアクセスして最新の機能と改善点をご確認ください。

[Create a New Model]\(新しいモデルの作成\) ボタンをクリックすると、次のように空白の開始ページが開きます。

![空白の開始ページ](./media/azure-security-threat-modeling-tool/tmtstart.png)

**[概要](./azure-security-threat-modeling-tool-getting-started.md)**の例で Microsoft チームが作成した脅威モデルを使用して、現在のツールで使用できるすべての機能を見てみましょう。

![基本的な脅威モデル](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>ナビゲーション

組み込みの機能について説明する前に、ツールに含まれる主なコンポーネントについて説明します

### <a name="menu-items"></a>メニュー項目

操作方法は、他の Microsoft 製品と似ています。 まず、最上位のメニュー項目を見てみましょう。

![メニュー項目](./media/azure-security-threat-modeling-tool/menuitems.png)

| ラベル                               | 詳細      |
| --------------------------------------- | ------------ |
| **File (ファイル)** | <ul><li>ファイルを開く、保存、閉じる</li><li>OneDrive アカウントのサインイン/サインアウト</li><li>リンクを共有する (表示 + 編集)</li><li>ファイル情報の表示</li><li>新しいテンプレートを既存のモデルに適用する</li></ul> |
| **Edit (編集)** | 元に戻す/やり直し操作、コピー、貼り付け、削除 |
| **View (表示)** | <ul><li>**[Analysis]\(分析\)** ビューと **[Design]\(デザイン\)** ビュー間で切り替えます。</li><li>閉じているウィンドウ (ステンシル、要素プロパティ、メッセージなど) を開く</li><li>レイアウトを既定の設定にリセットする</li></ul> |
| **Diagram (ダイアグラム)** | ダイアグラムの追加/削除、ダイアグラムのタブの操作 |
| **Reports (レポート)** | 他のユーザーと共有できる HTML レポートを作成する |
| **Help (ヘルプ)** | このツールの使い方を説明するガイド |

アイコンは上位メニューのショートカットです。

| アイコン                               | 詳細      |
| --------------------------------------- | ------------ |
| **Open (開く)** | 新しいファイルを開く |
| **Save (保存)** | 現在のファイルを保存する |
| **Design (デザイン)** | モデルを作成できるデザイン ビューに切り替える |
| **Analyze (分析)** | 生成された脅威とそのプロパティを表示する |
| **Add Diagram (ダイアグラムの追加)** | 新しいダイアグラムを追加する (Excel のタブの追加と似ています) |
| **Delete Diagram (ダイアグラムの削除)** | 現在のダイアグラムを削除する |
| **Copy (コピー)/Cut (切り取り)/Paste (貼り付け)** | 要素のコピー/切り取り/貼り付け |
| **Undo (元に戻す)/Redo (やり直し)** | 操作を元に戻す/やり直す |
| **Zoom In (拡大)/Zoom Out (縮小)** | 見やすくするためにダイアグラムを拡大/縮小する |
| **Feedback (フィードバック)** | MSDN フォーラムを開く |

### <a name="canvas"></a>キャンバス

要素をドラッグ アンド ドロップする領域です。 ドラッグ アンド ドロップは、最も簡単で効率的なモデルの構築方法です。 また、右クリックし、メニューから選択することもできます。次の図のように、使用している要素の一般的なバージョンが追加されます。

#### <a name="dropping-the-stencil-on-the-canvas"></a>キャンバスにステンシルをドロップする

![キャンバスのドロップ](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="clicking-on-the-stencil"></a>ステンシルをクリックする

![要素のプロパティ](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>ステンシル

選択したテンプレートに基づいて、使用できるすべてのステンシルが表示されます。 適切な要素が見つからない場合は、別のテンプレートを使用するか、ニーズに合わせてテンプレートを変更してください。 一般的に、次のようなカテゴリを組み合わせると見つけることができます。

| ステンシル名                               | 詳細      |
| --------------------------------------- | ------------ |
| **Process (プロセス)** | Applications (アプリケーション)、Browser Plugins (ブラウザー プラグイン)、Threads (スレッド)、Virtual Machines (仮想マシン) |
| **External Interactor (外部入力/終端)** | Authentication Providers (認証プロバイダー)、Browsers (ブラウザー)、Users (ユーザー)、Web Applications (Web アプリケーション) |
| **Data Store (データ ストア)** | Cache (キャッシュ)、Storage (記憶域)、Configuration Files (構成ファイル)、Databases (データベース)、Registry (レジストリ) |
| **Data Flow (データ フロー)** | Binary (バイナリ)、ALPC、HTTP、HTTPS/TLS/SSL、IOCTL、IPSec、Named Pipe (名前付きパイプ)、RPC/DCOM、SMB、UDP |
| **Trust Line/Border Boundary (信頼回線/境界)** | Corporate Networks (企業ネットワーク)、Internet (インターネット)、Machine (コンピューター)、Sandbox (サンドボックス)、User/Kernel Mode (ユーザー/カーネル モード) |

### <a name="notesmessages"></a>メモ/メッセージ

| コンポーネント                               | 詳細      |
| --------------------------------------- | ------------ |
| **Messages (メッセージ)** | 要素間にデータ フローがないなど、エラーが発生した場合に、ユーザーに警告する内部的なツール ロジック |
| **Notes (メモ)** | デザインおよびレビュー プロセス全体でエンジニアリング チームがファイルに手動で追加したメモ |

### <a name="element-properties"></a>要素のプロパティ

プロパティは選択した要素によって異なります。 信頼境界とは別に、その他すべての要素には 3 つの一般的な項目があります。

| 要素のプロパティ                               | 詳細      |
| --------------------------------------- | ------------ |
| **Name (名前)** | 簡単に認識できるように、プロセス、ストア、入力/終端、およびフローに名前を付ける場合に役立ちます |
| **Out of Scope (スコープ外)** | オンにすると、要素は脅威生成マトリックスから取得されます (推奨されません) |
| **Reason for Out of Scope (スコープ外の理由)** | スコープ外が選択された理由をユーザーに通知する理由のフィールド |

要素のカテゴリごとにプロパティは変わります。 各要素をクリックして使用できるオプションを確認できます。また、テンプレートを開いて詳細を確認することができます。 機能を確認してみましょう。

## <a name="welcome-screen"></a>ようこそ画面

ようこそ画面とは、アプリを開いたときに表示される最初の画面です。

### <a name="open-a-model"></a>Open A model (モデルを開く)

[Open a Model]\(モデルを開く\) ボタンにマウスを移動すると、非表示だった 2 つのオプション [Open From this Computer]\(このコンピューターから開く\) と [Open from OneDrive]\(OneDrive から開く\) が表示されます。 1 つのオプションを選択すると [File Open]\(ファイルを開く\) 画面が表示されますが、2 つ目のオプションを選択すると、OneDrive のサインイン プロセスが表示され、認証に成功した場合にのみ、フォルダーとファイルを選択できます。

![モデルを開く](./media/azure-security-threat-modeling-tool/openmodel.png)

![コンピューターまたは OneDrive から開く](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, suggestions and issues (フィードバック、提案、問題)

このオプションを選択すると、SDL ツールの MSDN フォーラムが表示されます。 回避策や新しいアイデアなど、他のユーザーのツールについて話している内容を確認することをお勧めします。

![フィードバック](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>デザイン ビュー

新しいモデルを開くか作成すると、まずデザイン ビューが表示されます。

### <a name="adding-elements"></a>要素を追加する

グリッドに要素を追加するには、2 つの方法があります。

- **ドラッグ アンド ドロップ** - 目的の要素をグリッドにドラッグし、要素のプロパティを使用してその他の情報を指定します。
- **右クリック** - グリッドの任意の場所を右クリックし、ドロップダウン メニューから選択します。 その要素の一般的な表記が画面に表示されます。

### <a name="connecting-elements"></a>要素を接続する

ツールで要素を接続するには、2 つの方法があります。

- **ドラッグ アンド ドロップ** - 目的のデータフローをグリッドにドラッグし、両端を適切な要素に接続します。
- **Shift キーを押しながらクリック** - 1 つ目の要素 (データの送信) をクリックし、Shift キーを押しながら 2 つ目の要素 (データの受信) をクリックします。 右クリックし、[Connect]\(接続\) を選択します。 双方向データフローを使用している場合、順番は重要ではありません。

### <a name="properties"></a>プロパティ

ダイアグラムに配置されているステンシルで、編集できるすべてのプロパティが表示されます。 プロパティを表示するには、ステンシルをクリックします。クリックしたステンシルの情報が表示されます。 以下の例は、"Database" (データベース) ステンシルをダイアグラムにドラッグする前と後を示しています。

#### <a name="before"></a>前

![前](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>後

![後](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>メッセージ

脅威モデルを作成し、データ フローを要素に接続し忘れた場合、操作を求めるメッセージ ウィンドウが表示されます。指示を無視するか、指示にしたがって問題を修正することができます。 

![メッセージ](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>メモ

[Messages]\(メッセージ\) から [Notes]\(メモ\) にタブを切り替えると、ダイアグラムにメモを追加して、自分の考えをすべて残すことができます。

## <a name="analysis-view"></a>分析ビュー

ダイアグラムの構築が完了したら、上部のメニュー項目でペイント パレットの横にある虫眼鏡を選択して、分析ビューに切り替えます。

![分析ビュー](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>生成された脅威項目

脅威をクリックすると、3 つの固有の機能を利用できます。

| 機能                               | 情報      |
| --------------------------------------- | ------------ |
| **既読インジケーター** | <p>脅威は赤くマークされるので、既読の項目の追跡を簡単に継続できます</p><p>![既読/未読インジケーター](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **相互作用フォーカス** | <p>ダイアグラムの相互作用は、強調表示されているその脅威に属しています</p><p>![相互作用フォーカス](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **脅威のプロパティ** | <p>脅威に関する詳細情報が [Threat Properties]\(脅威のプロパティ\) ウィンドウに表示されます</p><p>![脅威のプロパティ](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>優先度の変更

生成された各脅威の優先度を変更すると、脅威が色分けされるので、高、中、低の優先度の脅威がわかりやすくなります。

![優先度の変更](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>脅威のプロパティの編集可能なフィールド

上の図からわかるように、ユーザーはツールから生成された情報を変更し、特定のフィールド (理由など) に情報を追加することもできます。 これらのフィールドはテンプレートによって生成されるので、各脅威に合わせて他の情報が必要な場合は、変更することをお勧めします。

![脅威のプロパティ](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>レポート

優先度を変更し、生成された各脅威の状態を更新し終わったら、[Report]\(レポート\)、[Create Full Report]\(フル レポートの作成\) の順に選択して、ファイルを保存したり、レポートを印刷したりすることができます。 レポートに名前を付けるように求められます。名前を付けると、次の図のような画面が表示されます。

![レポート](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>次のステップ

コミュニティにテンプレートを投稿するには、**[GitHub](https://github.com/Microsoft/threat-modeling-templates)** ページにアクセスしてください。 **[ツールをダウンロード](https://aka.ms/tmtpreview)**すると、すぐに使い始めることができます。

