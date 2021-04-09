---
title: Microsoft Threat Modeling Tool 機能の概要 - Azure
description: Threat Modeling Tool で使用できる、分析ビューやレポートなどのすべての機能について説明します。
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 65a88f309602462e77336181316c0d5bf19a8a1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980684"
---
# <a name="threat-modeling-tool-feature-overview"></a>Threat Modeling Tool 機能の概要

Threat Modeling Tool は、脅威モデリングのニーズに応えます。 ツールの基本的な概要については、「[Threat Modeling Tool の概要](threat-modeling-tool-getting-started.md)」を参照してください。

> [!NOTE]
>Threat Modeling Tool はよく更新されるため、このガイドを頻繁にチェックして最新の機能と改善点をご確認ください。

空白のページを開くには、 **[Create A Model]\(モデルの作成\)** を選択します。

![空白のページ](./media/threat-modeling-tool-feature-overview/tmtstart.png)

ツールで現在利用可能な機能を確認するには、[概要](threat-modeling-tool-getting-started.md)に関するページの例で Microsoft チームが作成した脅威モデルを使用してください。

![基本的な脅威モデル](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>「ナビゲーション」

組み込みの機能について説明する前に、ツールの主なコンポーネントについて見ておきましょう。

### <a name="menu-items"></a>メニュー項目

操作方法は、他の Microsoft 製品と似ています。 上部のメニュー項目を確認してみましょう。

![メニュー項目](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Label                               | 詳細      |
| --------------------------------------- | ------------ |
| **[最近使ったファイル]** | <ul><li>ファイルを開く、保存する、閉じる</li><li>OneDrive アカウントのサインイン/サインアウトを行う。</li><li>リンクを共有する (表示と編集)。</li><li>ファイル情報を表示する。</li><li>新しいテンプレートを既存のモデルに適用する。</li></ul> |
| **[編集]** | 操作の取り消しとやり直し、コピー、貼り付け、削除を行う。 |
| **表示** | <ul><li>**分析** ビューと **デザイン** ビューを切り替える。</li><li>閉じているウィンドウ (ステンシル、要素プロパティ、メッセージなど) を開く。</li><li>レイアウトを既定の設定にリセットする。</li></ul> |
| **Diagram (ダイアグラム)** | ダイアグラムの追加と削除、ダイアグラムのタブ間の移動を行う。 |
| **レポート** | 他のユーザーと共有できる HTML レポートを作成する。 |
| **Help (ヘルプ)** | ツールの使い方を説明したガイドを参照する。 |

シンボルは、上部のメニューのショートカットです。

| Symbol                               | 詳細      |
| --------------------------------------- | ------------ |
| **[ファイル]** | 新しいファイルを開く。 |
| **および** | 現在のファイルを保存する。 |
| **Design (デザイン)** | モデルを作成できる **デザイン** ビューを開く。 |
| **分析** | 生成された脅威とそのプロパティを表示する。 |
| **ダイアグラムの追加** | 新しいダイアグラムを追加する (Excel の新しいタブと似ています)。 |
| **ダイアグラムの削除** | 現在のダイアグラムを削除する。 |
| **Copy (コピー)/Cut (切り取り)/Paste (貼り付け)** | 要素のコピー、切り取り、貼り付けを行う。 |
| **Undo (元に戻す)/Redo (やり直し)** | 操作を取り消す/やり直す。 |
| **拡大/縮小** | 見やすくするためにダイアグラムを拡大/縮小する。 |
| **フィードバック** | MSDN フォーラムを開く。 |

### <a name="canvas"></a>キャンバス

キャンバスは、要素をドラッグ アンド ドロップする領域です。 ドラッグ アンド ドロップは、最も簡単で効率的なモデルの構築方法です。 次に示すように、右クリックしてメニューから項目を選択し、要素の汎用バージョンを追加することもできます。

#### <a name="drop-the-stencil-on-the-canvas"></a>キャンバスにステンシルをドロップする

![キャンバスのドロップ](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>ステンシルを選択する

![要素のプロパティ](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>ステンシル

選択したテンプレートに基づいて、使用できるすべてのステンシルが表示されます。 正しい要素が見つからない場合は、別のテンプレートを使用します。 または、ニーズに合わせてテンプレートを変更できます。 一般に、次のようなカテゴリの組み合わせが表示されます。

| ステンシル名                               | 詳細      |
| --------------------------------------- | ------------ |
| **[処理]** | アプリケーション、ブラウザー プラグイン、スレッド、仮想マシン |
| **外部入力/終端** | 認証プロバイダー、ブラウザー、ユーザー、Web アプリケーション |
| **データ ストア** | キャッシュ、ストレージ、構成ファイル、データベース、レジストリ |
| **データ フロー** | バイナリ、ALPC、HTTP、HTTPS/TLS/SSL、IOCTL、IPSec、名前付きパイプ、RPC/DCOM、SMB、UDP |
| **Trust line/Border boundary (信頼回線/境界)** | 企業ネットワーク、インターネット、マシン、サンドボックス、ユーザー/カーネル モード |

### <a name="notesmessages"></a>メモ/メッセージ

| コンポーネント                               | 詳細      |
| --------------------------------------- | ------------ |
| **Messages (メッセージ)** | 要素間にデータ フローがないなど、エラーが発生した場合にユーザーに警告する内部的なツール ロジック。 |
| **メモ** | デザインおよびレビュー プロセス全体でエンジニアリング チームがファイルに手動で追加したメモ。 |

### <a name="element-properties"></a>要素のプロパティ

要素のプロパティは、選択した要素によって異なります。 信頼境界を除くと、その他のすべての要素には 3 つの一般的な項目があります。

| 要素のプロパティ                               | 詳細      |
| --------------------------------------- | ------------ |
| **Name** | 簡単に認識できるように、プロセス、ストア、入力/終端、フローに名前を付ける場合に役立ちます。 |
| **スコープ外** | オンにすると、要素は脅威生成マトリックスから取得されます (推奨されません)。 |
| **Reason for out of scope (スコープ外の理由)** | スコープ外が選択された理由をユーザーに通知する理由のフィールド。 |

要素のカテゴリごとにプロパティは変わります。 使用可能なオプションを調べるには、各要素を選択します。 または、テンプレートを開いて詳細を調べることができます。 機能を見てみましょう。

## <a name="welcome-screen"></a>[ようこそ] 画面

アプリを開くと、**ようこそ** 画面が表示されます。

### <a name="open-a-model"></a>モデルを開く

**[Open A Model]\(モデルを開く\)** にポインターを置くと、 **[Open From This Computer]\(このコンピューターから開く\)** と **[Open From OneDrive]\(OneDrive から開く\)** という 2 つのオプションが表示されます。 最初のオプションでは、 **[File Open]\(ファイルを開く\)** 画面が表示されます。 2 番目のオプションでは、OneDrive のサインイン プロセスが実行されます。 認証が成功すると、フォルダーとファイルを選択できます。

![モデルを開く](./media/threat-modeling-tool-feature-overview/openmodel.png)

![コンピューターまたは OneDrive から開く](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>フィードバック、提案、問題

**[Feedback, Suggestions and Issues]\(フィードバック、提案、問題\)** を選択すると、SDL ツールの MSDN フォーラムが表示されます。 回避策や新しいアイデアなど、他のユーザーがツールについて述べている内容を読むことができます。

![スクリーンショットには、"Feedback, Suggestions and Issues" というテキストが示されています。](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>デザイン ビュー

新しいモデルを開いたり作成したりすると、**デザイン** ビューが開きます。

### <a name="add-elements"></a>要素の追加

2 つの方法でグリッドに要素を追加することができます。

- **ドラッグ アンド ドロップ**: 目的の要素をグリッドにドラッグします。 そのうえで、要素のプロパティを使用して追加情報を入力します。
- **右クリック**: グリッドの任意の場所を右クリックし、ドロップダウン メニューから項目を選択します。 選択した要素の一般的な表現が画面に表示されます。

### <a name="connect-elements"></a>要素の接続

2 つの方法で要素を接続することができます。

- **ドラッグ アンド ドロップ**: 目的のデータ フローをグリッドにドラッグし、両端を適切な要素に接続します。
- **Shift キーを押しながらクリック**: 1 つ目の要素 (データの送信) をクリックし、Shift キーを押しながら 2 つ目の要素 (データの受信) をクリックします。 右クリックし、 **[接続]** を選択します。 双方向データ フローを使用している場合、順番は重要ではありません。

### <a name="properties"></a>Properties

 ステンシル上で変更できるプロパティを表示するには、ステンシルを選択します。それに応じて情報が設定されます。 以下の例は、 **[データベース]** ステンシルをダイアグラムにドラッグする前後を示しています。

#### <a name="before"></a>[指定日付より前]

![[指定日付より前]](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>After

![After](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>メッセージ

脅威モデルを作成し、データ フローを要素に接続し忘れた場合は、通知が表示されます。 メッセージを無視するか、指示に従って問題を修正することができます。 

![スクリーンショットには、要素に接続されていない脅威モデル コネクタと、この問題が発生したことを示すメッセージが示されています。](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>メモ

ダイアグラムにメモを追加するには、 **[メッセージ]** タブから **[メモ]** タブに切り替えます。

## <a name="analysis-view"></a>分析ビュー

ダイアグラムを作成した後、ショートカット ツール バーの **分析** シンボル (虫眼鏡) を選択して、**分析** ビューに切り替えます。

![分析ビュー](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>生成された脅威項目

脅威を選択すると、3 つの異なる機能を使用できます。

| 機能                               | Information      |
| --------------------------------------- | ------------ |
| **既読インジケーター** | <p>脅威が既読としてマークされます。これは、確認済みの項目を追跡するうえで役立ちます。</p><p>![既読/未読インジケーター](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **相互作用フォーカス** | <p>脅威に含まれているダイアグラム内の相互作用が、強調表示されます。</p><p>![相互作用フォーカス](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **脅威のプロパティ** | <p>脅威に関する詳細情報が **[Threat Properties]\(脅威のプロパティ\)** ウィンドウに表示されます。</p><p>![脅威のプロパティ](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>優先度の変更

生成された各脅威の優先度レベルを変更することができます。 異なる色を使用すると、優先度が高、中、低の脅威を簡単に識別できます。

![優先度の変更](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>脅威のプロパティの編集可能なフィールド

上の図のように、ツールによって生成された情報を変更することができます。 また、特定のフィールドに情報 (理由など) を追加することもできます。 これらのフィールドは、テンプレートによって生成されます。 各脅威についてさらに情報が必要な場合は、変更を行えます。

![脅威のプロパティ](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Reports

優先度の変更と、生成された各脅威の状態の更新が終わったら、ファイルを保存したり、レポートを印刷したりすることができます。 **[レポート]**  >  **[Create Full Report]\(フル レポートの作成\)** の順に移動します。 レポートに名前を付けると、次の図のような内容が表示されます。

![スクリーンショットには、概要、図、およびその他の情報を含む、脅威のモデル化レポートの例が示されています。](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>次のステップ

- ご質問、ご意見、懸念事項は tmtextsupport@microsoft.com に送信してください。 **[Threat Modeling Tool をダウンロード](https://aka.ms/threatmodelingtool)** すると、すぐに使い始めることができます。
- コミュニティにテンプレートを投稿するには、[GitHub](https://github.com/Microsoft/threat-modeling-templates) ページにアクセスしてください。