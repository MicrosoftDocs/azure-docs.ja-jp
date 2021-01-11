---
title: Azure Policy を使用して VM 作成時のバックアップを自動的に有効にする
description: Azure Policy を使用して、特定のスコープで作成されたすべての VM のバックアップを自動的に有効化する方法を説明する記事
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: de4923000bc842203535e03727fd532c67a8f517
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826074"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Azure Policy を使用して VM 作成時のバックアップを自動的に有効にする

組織でのバックアップまたはコンプライアンス管理者の主な役割の 1 つは、ビジネスに重要なすべてのコンピューターが適切なリテンション期間でバックアップされるようにすることです。

現在、Azure Backup には、**サブスクリプションまたはリソース グループ内の指定された場所にあるすべての Azure VM** に割り当てることができる組み込みのポリシー (Azure Policy を使用) が用意されています。 このポリシーが特定のスコープに割り当てられると、そのスコープ内に作成されたすべての新しい VM は、**同じ場所およびサブスクリプション内の既存のコンテナー**にバックアップするように自動的に構成されます。 ユーザーは、バックアップされた VM を関連付けるコンテナーと保有期間ポリシーを指定できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

* 組み込みのポリシーは、現在、Azure VM でのみサポートされています。 ユーザーは、割り当て時に指定されたリテンション期間ポリシーが VM リテンション期間ポリシーであることを確認する必要があります。 このポリシーでサポートされているすべての VM SKU については、[この](./backup-azure-policy-supported-skus.md)ドキュメントを参照してください。

* ポリシーは、一度に 1 つの場所とサブスクリプションに割り当てることができます。 場所とサブスクリプションの全体で VM のバックアップを有効にするには、場所とサブスクリプションの組み合わせごとに 1 つずつ、ポリシー割り当ての複数のインスタンスを作成する必要があります。

* 指定されたコンテナーと、バックアップ用に構成された VM は、異なるリソース グループに属することができます。

* 管理グループのスコープは、現在サポートされていません。

* 組み込みのポリシーは、現在、国内のクラウドでは使用できません。

## <a name="using-the-built-in-policy"></a>組み込みポリシーの使用

必要なスコープにポリシーを割り当てるには、以下の手順に従います。

1. Azure Portal にサインインし、 **[ポリシー]** ダッシュボードに移動します。
1. 左側のメニューの **[定義]** を選択して、Azure リソース全体にわたるすべての組み込みポリシーの一覧を取得します。
1. **[カテゴリ] = [バックアップ]** の一覧をフィルター処理します。 一覧にフィルターが適用されて絞り込まれ、[Configure backup on VMs of a location to an existing central Vault in the same location] (特定の場所の VM を同じ場所にある既存の中央コンテナーにバックアップするように構成する) という名前の 1 つのポリシーが表示されます。
![[ポリシー] ダッシュボード](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. ポリシーの名前を選択します。 このポリシーの詳細な定義にリダイレクトされます。
![[ポリシー定義] ペイン](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. ペインの上部にある **[割り当て]** ボタンを選択します。 これにより、 **[ポリシーの割り当て]** ペインにリダイレクトされます。
1. **[基本]** で、 **[スコープ]** フィールドの横にある 3 つのドットを選択します。 これにより、適切なコンテキスト ペインが開き、そこでポリシーを適用するサブスクリプションを選択できます。 必要に応じて、リソース グループを選択して、ポリシーが特定のリソース グループ内の VM にのみ適用されるようにすることもできます。
![ポリシー割り当ての [基本]](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. **[パラメーター]** タブで、ドロップダウンから場所を選択し、スコープ内の VM を関連付ける必要があるコンテナーとバックアップ ポリシーを選択します。
![ポリシー割り当ての [パラメーター]](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. **[効果]** が deployIfNotExists に設定されていることを確認します。
1. **[レビュー + 作成]** に移動し、 **[作成]** を選択します。

> [!NOTE]
>
> [修復](../governance/policy/how-to/remediate-resources.md)を使用して、既存の VM で Azure Policy を使用することもできます。

> [!NOTE]
>
> このポリシーは、一度に 200 を超える VM に割り当てないようにすることをお勧めします。 ポリシーが 200 を超える VM に割り当てられていると、スケジュールによって指定された数時間後にバックアップがトリガーされることがあります。

## <a name="next-steps"></a>次の手順

[Azure Policy に関する詳細情報](../governance/policy/overview.md)
