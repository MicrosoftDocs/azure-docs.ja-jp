---
title: Azure Policy を使用して VM 作成時のバックアップを自動的に有効にする
description: Azure Policy を使用して、特定のスコープで作成されたすべての VM のバックアップを自動的に有効化する方法を説明する記事
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: ea3c0d217c8495078e91e171caef695bb32d129b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979944"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Azure Policy を使用して VM 作成時のバックアップを自動的に有効にする

組織でのバックアップまたはコンプライアンス管理者の主な役割の 1 つは、ビジネスに重要なすべてのコンピューターが適切なリテンション期間でバックアップされるようにすることです。

現在、Azure Backup には、**サブスクリプションまたはリソース グループ内の指定された場所にあるすべての Azure VM** に割り当てることができる組み込みのポリシー (Azure Policy を使用) が用意されています。 このポリシーが特定のスコープに割り当てられると、そのスコープ内に作成されたすべての新しい VM は、**同じ場所およびサブスクリプション内の既存のコンテナー**にバックアップするように自動的に構成されます。 ユーザーは、バックアップされた VM を関連付けるコンテナーとリテンション期間ポリシーを指定できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ 

* 組み込みのポリシーは、現在、Azure VM でのみサポートされています。 ユーザーは、割り当て時に指定されたリテンション期間ポリシーが VM リテンション期間ポリシーであることを確認する必要があります。 このポリシーでサポートされているすべての VM SKU については、[この](https://aka.ms/PolicySupportedSKUs)ドキュメントを参照してください。

* ポリシーは、一度に 1 つの場所とサブスクリプションに割り当てることができます。 場所とサブスクリプションの全体で VM のバックアップを有効にするには、場所とサブスクリプションの組み合わせごとに 1 つずつ、ポリシー割り当ての複数のインスタンスを作成する必要があります。

* 指定されたコンテナーと、バックアップ用に構成された VM は、異なるリソース グループに属することができます。

* 管理グループのスコープは、現在サポートされていません。

## <a name="using-the-built-in-policy"></a>組み込みポリシーの使用

必要なスコープにポリシーを割り当てるには、次の手順に従ってください。

1. Azure Portal にサインインし、 **[ポリシー]** ダッシュボードに移動します。
2. 左側のメニューの **[定義]** を選択して、Azure リソース全体にわたるすべての組み込みポリシーの一覧を取得します。
3. **[カテゴリ] = [バックアップ]** の一覧をフィルター処理します。 [Configure backup on VMs of a location to an existing central Vault in the same location] (特定の場所の VM を同じ場所にある既存の中央コンテナーにバックアップするように構成する) という名前の 1 つのポリシーにフィルターが適用された一覧が表示されます。
![[ポリシー] ダッシュボード](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. ポリシーの名前をクリックします。 このポリシーの詳細な定義にリダイレクトされます。
![[ポリシー定義] ブレード](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. ブレードの上部にある **[割り当て]** ボタンをクリックします。 これにより、 **[ポリシーの割り当て]** ブレードにリダイレクトされます。
6. **[基本]** で、 **[スコープ]** フィールドの横にある 3 つのドットをクリックします。 これにより、適切なコンテキスト ブレードが開き、ポリシーを適用するサブスクリプションを選択できます。 必要に応じて、リソース グループを選択して、ポリシーが特定のリソース グループ内の VM にのみ適用されるようにすることもできます。
![ポリシー割り当ての [基本]](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. **[パラメーター]** タブで、ドロップダウンから場所を選択し、スコープ内の VM を関連付ける必要があるコンテナーとバックアップ ポリシーを選択します。
![ポリシー割り当ての [パラメーター]](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. **[効果]** が deployIfNotExists に設定されていることを確認します。
9. **[レビュー + 作成]** に移動し、 **[作成]** をクリックします。

> [!NOTE]
>
> [修復](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources)を使用して、既存の VM で Azure Policy を使用することもできます。

> [!NOTE]
>
> このポリシーは、一度に 200 を超える VM に割り当てないようにすることをお勧めします。 ポリシーが 200 を超える VM に割り当てられていると、スケジュールによって指定された数時間後にバックアップがトリガーされることがあります。

## <a name="next-steps"></a>次の手順

[Azure Policy に関する詳細情報](https://docs.microsoft.com/azure/governance/policy/overview)