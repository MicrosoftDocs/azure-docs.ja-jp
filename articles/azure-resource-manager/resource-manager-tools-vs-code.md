---
title: Visual Studio Code を使って Azure Resource Manager テンプレートを作成する
description: Visual Studio Code と Azure Resource Manager ツール拡張機能をインストールして使用する方法について説明します。
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 8d2b818846eeae44ffe05b4546a333510c638621
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196873"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Visual Studio Code を使って Azure Resource Manager テンプレートを作成する

Visual Studio Code は、軽量でマルチプラットフォームのオープンソース エディターです。 Azure Resource Manager テンプレート ツール拡張機能は、Resource Manager テンプレートを開発するためのプラグインです。 この拡張機能は、テンプレートの言語サポートを追加して、IntelliSense、構文の強調表示、インライン ヘルプ、およびその他の多くの言語機能を提供します。 これらを組み合わせて使うことで、推奨されるテンプレート開発エクスペリエンスが提供されます。

## <a name="install-visual-studio-code"></a>Visual Studio Code のインストール

Visual studio Code は、MacOS、Windows、Linux をサポートしています。  [Visual Studio Code](https://code.visualstudio.com/) からインストールできます。

## <a name="install-resource-manager-tools-extension"></a>Resource Manager ツール拡張機能のインストール

1. Visual Studio Code を開きます。
1. 左側のメニューから **[拡張機能]** を選択します。 または、 **[表示]** メニューから **[拡張機能]** を選択して、[拡張機能] ウィンドウを開きます。

    ![Visual Studio Code Resource Manager ツール拡張機能をインストールする](./media/resource-manager-tools-vs-code/resource-manager-visual-studio-code-tools-extension.png)
1. 「**Resource Manager**」を検索します。
1. **[Azure Resource Manager ツール]** で **[インストール]** を選択します。

## <a name="the-extension-features"></a>拡張機能のフィーチャー

### <a name="colorization-for-template-language-expressions"></a>テンプレート言語式の色づけ

パラメーター、変数、関数、名前、および式は、次のスクリーンショットに示すように色分けされます。

![Visual Studio Code Resource Manager ツール拡張機能の色付け](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-colorization.png)

テンプレート アウトライン ビューを使用すると、大きなテンプレート間を簡単に移動できます。

### <a name="intellisense"></a>IntelliSense

Resource Manager テンプレート拡張機能では、関数名、パラメーター、変数、および参照の入力候補が認識されます。 入力時に IntelliSense の候補がポップアップ表示されます。 文字の入力を続けると、メンバー (変数、メソッドなど) の一覧がフィルター処理されて、入力した文字を含むメンバーのみが含まれるようになります。 **タブ**または **Enter** キーを押すと、選択したメンバーが挿入されます。

- 組み込み関数名

    ![Visual Studio Code Resource Manager ツール拡張機能の IntelliSense 関数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-functions.png)

- パラメーター参照

    ![Visual Studio Code Resource Manager ツール拡張機能の IntelliSense パラメーター](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-parameters.png)

- 変数参照

    ![Visual Studio Code Resource Manager ツール拡張機能の IntelliSense 変数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroup() プロパティ

    ![Visual Studio Code Resource Manager ツール拡張機能の IntelliSense 関数](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-resourcegroup.png)

さらに、IntelliSense は、subscription() プロパティと、オブジェクトである変数への参照のプロパティでも機能します。

### <a name="signature-help-for-function-parameters"></a>関数パラメーターのシグネチャ ヘルプ

関数名の上にカーソルを置くと、拡張機能により、関数パラメーターのシグネチャ ヘルプが表示されます。

![Visual Studio Code Resource Manager ツール拡張機能のシグネチャ機能](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>変数参照とパラメーター参照の定義への移動

**Ctrl キーを押しながらクリック**するか、スクリーンショットに示されているようにコンテキスト メニューを使用して、定義に移動できます。![Visual Studio Code Resource Manager ツール拡張機能の定義への移動](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-context-menu.png)

**Ctrl キーと Alt キーを押しながらクリック**すると、横に定義を開くことができます。

### <a name="peek-for-variable-and-parameter-definitions"></a>変数とパラメーターの定義のピーク

ピーク エディターを開くには、前のスクリーンショットに示されているように、コンテキスト メニューを使用します。

次のスクリーンショットはピーク エディターを示しています。

![Visual Studio Code Resource Manager ツール拡張機能のピーク エディター](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>変数とパラメーターのすべての参照の検索

すべての参照を検索するには、前のスクリーンショットに示されているように、コンテキスト メニューを使用します。

次のスクリーンショットは、参照がどのように強調表示されるかを示しています。

![Visual Studio Code Resource Manager ツール拡張機能のすべての参照を検索](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>変数とパラメーターのすべての参照の名前変更

変数とパラメーターのすべての参照の名前を変更するには、前のスクリーンショットに示されているように、コンテキスト メニューを使用します。

### <a name="hover-for-parameter-description"></a>パラメーターの説明のホバー

![Visual Studio Code Resource Manager ツール拡張機能での定義のホバー](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>かっこの一致

近くにカーソルを置くと、対応する角かっこがすぐに強調表示されます。 中かっこをクリックすると、次のスクリーンショットに示すように、対応する中かっこも強調表示されます。

![Visual Studio Code Resource Manager ツール拡張機能のかっこの一致](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>エラーおよび警告の表示

拡張機能によって識別されるエラーには次のものがあります。

- 未定義のパラメーター参照
- 未定義の変数参照
- 認識されない関数名
- 変数定義での reference() 関数の使用
- 関数の引数の数が正しくない

警告には次のようなものがあります。

- 未使用のパラメーター
- 未使用の変数

## <a name="next-steps"></a>次の手順

- Resource Manager と Resource Manager テンプレートの詳細については、「[Azure Resource Manager の概要](./resource-group-overview.md)」を参照してください。
- Visual Studio Code を使用してクイックスタートを実行するには、「[クイック スタート:Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)」を参照してください。
