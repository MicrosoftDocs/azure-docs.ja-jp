---
title: Azure Monitor ビューのフィルター | Microsoft Docs
description: Azure Monitor ビューのフィルターを使用すると、ユーザーはビュー自体を変更せずに、特定のプロパティの値によってビュー内のデータをフィルター処理することができます。  この記事では、フィルターの使用方法およびカスタム ビューへの追加方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 988bb1467e7bf2cca31151d85c57677b235cab30
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043305"
---
# <a name="filters-in-azure-monitor-views"></a>Azure Monitor ビューのフィルター
[Azure Monitor ビュー](view-designer.md)内の **フィルター** を使用すると、ユーザーはビュー自体を変更せずに、特定のプロパティの値によってビュー内のデータをフィルター処理することができます。  たとえば、ビューのユーザーが、特定のコンピューターまたは複数のコンピューターからのデータだけをフィルター表示したビューを利用できます。  1 つのビューに複数のフィルターを作成して、ユーザーが複数のプロパティでフィルター処理することが可能です。  この記事では、フィルターの使用方法およびカスタム ビューへの追加方法について説明します。

## <a name="using-a-filter"></a>フィルターを使用する
ビューの上部にある日時範囲をクリックしてドロップダウンを開き、ビューの日時範囲を変更できます。

![Azure Monitor のビューの日時範囲をドロップダウン メニューのスクリーンショット。選択した [過去 7 日間] のラジオ ボタンが表示されます。](media/view-designer-filters/filters-example-time.png)

**[+]** をクリックして、ビューに定義されているカスタム フィルターを使用してフィルターを追加します。 ドロップダウンからフィルターの値を選択するか、値を入力します。 **[+]** をクリックしてさらにフィルターを追加します。 


![Azure Monitor でカスタム フィルターを追加するためのダイアログのスクリーンショット。 [プロパティの選択] ドロップダウン メニューで、[コンピューター] プロパティが選択されています。](media/view-designer-filters/filters-example-custom.png)

フィルターのすべての値を削除すると、そのフィルターは適用されなくなります。


## <a name="creating-a-filter"></a>フィルターを作成する

[ビューの編集](view-designer.md)時に、**[フィルター]** タブからフィルターを作成します。  フィルターはビューに対してグローバルで、ビュー内のすべての部分に適用されます。  

![フィルターの設定](media/view-designer-filters/filters-settings.png)

フィルターの設定を次の表に示します。

| 設定 | 説明 |
|:---|:---|
| フィールド名 | フィルター処理に使用されるフィールドの名前です。  このフィールドは、**[値のクエリ]** にある集計フィールドと一致している必要があります。 |
| 値のクエリ | ユーザー用のフィルターのドロップダウン リストを設定するために実行するクエリ。  特定のフィールドに対して一意の値を指定するには、このクエリにおいて [summarize](/azure/kusto/query/summarizeoperator) または [distinct](/azure/kusto/query/distinctoperator) のいずれかを使用する必要があり、また、それが **[フィールド名]** と一致している必要があります。  [sort](/azure/kusto/query/sortoperator) を使用すると、ユーザーに表示される値を並び替えることができます。 |
| タグ | フィルターをサポートするクエリで使用されるフィールドの名前です。ユーザーへの表示にも使われます。 |

### <a name="examples"></a>例

次の表に、一般的なフィルターの例をいくつか挙げます。  

| フィールド名 | 値のクエリ | タグ |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | [Computers (コンピューター)] |
| EventLevelName | Event &#124; distinct EventLevelName | 重大度 |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | 重大度 |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>ビューのクエリを変更する

フィルターが適用されるようにするには、ビューでクエリを変更し、選択された値でフィルター処理を行う必要があります。  ビュー内でクエリを変更しないと、ユーザーが選択した値はどれも有効にはなりません。

クエリでフィルター値を使用する構文は次のようになります。 

`where ${filter name}`  

たとえば、イベントを返して _Computers_ というフィルターを使用するクエリがビューにある場合、以下のクエリを使用することができます。

```kusto
Event | where ${Computers} | summarize count() by EventLevelName
```

Severity という別のフィルターを追加した場合は、両方のフィルターを適用する次のクエリを使用できます。

```kusto
Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName
```

## <a name="next-steps"></a>次のステップ
* カスタム ビューに追加できる[視覚エフェクト パーツ](view-designer-parts.md)の詳細についてはこちらをご覧ください。
