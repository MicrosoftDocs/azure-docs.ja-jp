---
title: バックアップ センターを使用してバックアップ資産を管理する
description: バックアップ センターを使用して、バックアップの観点からすべてのリソースが準拠していることを確認するために Azure 環境を管理する方法について説明します。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 283c99c4b17683850f71b25fb2006784e43f3b8f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506211"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>バックアップ センターを使用してバックアップ資産を管理する

バックアップ センターは、バックアップの観点からすべてのリソースが準拠していることを確保するために Azure 環境を管理するのに役立ちます。 バックアップ センターのガバナンス機能のいくつかを次に示します。

* バックアップ用の Azure ポリシーの表示と割り当て

* バックアップ用のすべての組み込み Azure ポリシーに関するリソースのコンプライアンスを表示します。

* バックアップ用に構成されていないすべてのデータソースの表示。

## <a name="supported-scenarios"></a>サポートされるシナリオ

* サポートされるシナリオとサポートされないシナリオの詳細な一覧については、[サポート マトリックス](backup-center-support-matrix.md)を参照してください。

## <a name="azure-policies-for-backup"></a>バックアップ用の Azure ポリシー

バックアップに使用できるすべての [Azure ポリシー](../governance/policy/overview.md)を表示するには、 **[バックアップ用の Azure ポリシー]** メニュー項目を選択します。 これにより、サブスクリプションとリソース グループへの割り当てに使用できる、組み込みとカスタムのすべての[バックアップ用の Azure ポリシー定義](policy-reference.md)が表示されます。

いずれかの定義を選択すると、スコープに[ポリシーを割り当てる](../governance/policy/tutorials/create-and-manage.md#assign-a-policy)ことができます。

![Azure ポリシー定義を選択する](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>バックアップ コンプライアンス

[バックアップ コンプライアンス] メニュー項目をクリックすると、Azure 環境に割り当てたさまざまな組み込みのポリシーに従って、リソースの[コンプライアンス](../governance/policy/how-to/get-compliance-data.md)を表示できます。 すべてのポリシーに準拠しているリソースの割合、および 1 つ以上の非対応リソースを持つポリシーを表示できます。

![バックアップ コンプライアンスの表示](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>保護可能なデータソース

**[保護可能なデータソース]** メニュー項目を選択すると、バックアップ用に構成されていないすべてのデータソースを表示できます。 この一覧は、データソースのサブスクリプション、リソース グループ、場所、種類、タグでフィルター処理できます。 バックアップする必要のあるデータソースを識別したら、対応するグリッド項目を右クリックし、 **[バックアップ]** を選択してそのリソースのバックアップを構成できます。

![[保護可能なデータソース] メニュー](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> データソースの種類として **[Azure VM の SQL]** を選択した場合、 **[保護可能なデータソース]** ビューには、バックアップ用に構成されている SQL データベースがないすべてのギャラリー VM の一覧が表示されます。
> データソースの種類として **[Azure Storage (Azure Files)]** を選択した場合、 **[保護可能なデータソース]** ビューには、バックアップ用に構成されているファイル共有がないすべての (ファイル共有がサポートされている) ストレージ アカウントの一覧が表示されます。


## <a name="next-steps"></a>次のステップ

* [バックアップを監視および操作する](backup-center-monitor-operate.md)
* [バックアップ センターを使用してアクションを実行する](backup-center-actions.md)
* [バックアップに関する分析情報を取得する](backup-center-obtain-insights.md)