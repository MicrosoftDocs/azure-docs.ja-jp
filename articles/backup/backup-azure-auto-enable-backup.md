---
title: Azure Policy を使用して VM 作成時のバックアップを自動的に有効にする
description: Azure Policy を使用して、特定のスコープで作成されたすべての VM のバックアップを自動的に有効化する方法を説明する記事
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: dfa4364eeaa9f5b60af3f5d6a19aaeb188d4f65e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101707304"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Azure Policy を使用して VM 作成時のバックアップを自動的に有効にする

組織でのバックアップまたはコンプライアンス管理者の主な役割の 1 つは、ビジネスに重要なすべてのコンピューターが適切なリテンション期間でバックアップされるようにすることです。

現在、Azure Backup には、お使いのAzure 仮想マシンがバックアップ用に構成されていることを自動的に確認できるように、([Azure Policy](../governance/policy/overview.md) を使用した) さまざまな組み込みのポリシーが用意されています。 バックアップ チームとリソースの編成方法に応じて、下のポリシーのいずれかを使用できます。

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>ポリシー 1 - 特定のタグが付いていない VM の、同じ場所にある既存の Recovery Services コンテナーへのバックアップを構成する

アプリケーション チーム間のバックアップを管理する中央バックアップ チームが組織にある場合は、このポリシーを使用して、管理対象の VM と同じサブスクリプションと場所にある既存の中央 Recovery Services コンテナーへのバックアップを構成できます。 このポリシーのスコープから、特定のタグを含む VM を **除外** できます。

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>ポリシー 2 - [プレビュー] 特定のタグが付いた VM の、同じ場所にある既存の Recovery Services コンテナーへのバックアップを構成する
このポリシーは、上記のポリシー 1 と同様に機能しますが、唯一の違いは、このポリシーを使用すると、特定のタグを含む VM をこのポリシーのスコープに **含める** ことができる点です。 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>ポリシー 3 - [プレビュー] 特定のタグが付いていない VM の、既定のポリシーでの新しい Recovery Services コンテナーへのバックアップを構成する
アプリケーションを専用のリソース グループに編成し、それらを同じコンテナーによってバックアップする場合は、このポリシーを使用すると、このアクションを自動的に管理できます。 このポリシーのスコープから、特定のタグを含む VM を **除外** できます。

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>ポリシー 4 - [プレビュー] 特定のタグが付いた VM の、既定のポリシーでの新しい Recovery Services コンテナーへのバックアップを構成する
このポリシーは、上記のポリシー 3 と同様に機能しますが、唯一の違いは、このポリシーを使用すると、特定のタグを含む VM をこのポリシーのスコープに **含める** ことができる点です。 

上記に加えて、Azure Backup には、[監査のみの](../governance/policy/concepts/effects.md#audit)ポリシー (**仮想マシンに対して Azure Backup を有効にする必要がある**) も用意されています。 このポリシーにより、バックアップが有効になっていない仮想マシンが特定されますが、これらの VM のバックアップは自動的には構成されません。 これは、単に VM の全体的なコンプライアンスを評価しようとしていて、すぐに対処する予定はない場合に便利です。

## <a name="supported-scenarios"></a>サポートされるシナリオ

* 組み込みのポリシーは、現在、Azure VM でのみサポートされています。 ユーザーは、割り当て時に指定されたリテンション期間ポリシーが VM リテンション期間ポリシーであることを確認する必要があります。 このポリシーでサポートされているすべての VM SKU については、[この](./backup-azure-policy-supported-skus.md)ドキュメントを参照してください。

* ポリシー 1 と 2 は、一度に 1 つの場所およびサブスクリプションに割り当てることができます。 場所とサブスクリプションの全体で VM のバックアップを有効にするには、場所とサブスクリプションの組み合わせごとに 1 つずつ、ポリシー割り当ての複数のインスタンスを作成する必要があります。

* ポリシー 1 と 2 では、管理グループのスコープは、現在サポートされていません。

* ポリシー 1 と 2 では、指定されたコンテナーと、バックアップ用に構成された VM は、異なるリソース グループに属することができます。

* ポリシー 1、2、3、4 は現在、国内クラウドでは使用できません。

* ポリシー 3 と 4 は、一度に 1 つのサブスクリプション (またはサブスクリプション内のリソース グループ) に割り当てることができます。

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>組み込みポリシーの使用

下の手順で、「ポリシー 1:**特定のタグが付いていない VM の、同じ場所にある既存の Recovery Services コンテナーへのバックアップを構成する**」を特定のスコープに割り当てるエンド ツー エンドのプロセスについて説明します。 同様の手順は、その他のポリシーにも当てはまります。 割り当てられると、スコープ内に作成された新しい VM がバックアップ用に自動的に構成されます。

1. Azure Portal にサインインし、 **[ポリシー]** ダッシュボードに移動します。
2. 左側のメニューの **[定義]** を選択して、Azure リソース全体にわたるすべての組み込みポリシーの一覧を取得します。
3. **[カテゴリ] = [バックアップ]**  で一覧をフィルター処理し、[Configure backup on VMs of a location to an existing central Vault in the same location]\(特定の場所の VM を同じ場所にある既存の中央コンテナーにバックアップするように構成する\) という名前のポリシーを選択します。
![[ポリシー] ダッシュボード](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. ポリシーの名前を選択します。 このポリシーの詳細な定義にリダイレクトされます。
![[ポリシー定義] ペイン](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. ペインの上部にある **[割り当て]** ボタンを選択します。 これにより、 **[ポリシーの割り当て]** ペインにリダイレクトされます。
6. **[基本]** で、 **[スコープ]** フィールドの横にある 3 つのドットを選択します。 これにより、適切なコンテキスト ペインが開き、そこでポリシーを適用するサブスクリプションを選択できます。 必要に応じて、リソース グループを選択して、ポリシーが特定のリソース グループ内の VM にのみ適用されるようにすることもできます。
![ポリシー割り当ての [基本]](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. **[パラメーター]** タブで、ドロップダウンから場所を選択し、スコープ内の VM を関連付ける必要があるコンテナーとバックアップ ポリシーを選択します。 タグ名と、タグ値の配列を指定することもできます。 特定のタグに対して指定された値のいずれかが含まれている VM は、ポリシー割り当てのスコープから除外されます。
![ポリシー割り当ての [パラメーター]](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. **[効果]** が deployIfNotExists に設定されていることを確認します。
9. **[レビュー + 作成]** に移動し、 **[作成]** を選択します。

> [!NOTE]
>
> [修復](../governance/policy/how-to/remediate-resources.md)を使用して、既存の VM で Azure Policy を使用することもできます。

> [!NOTE]
>
> このポリシーは、一度に 200 を超える VM に割り当てないようにすることをお勧めします。 ポリシーが 200 を超える VM に割り当てられていると、スケジュールによって指定された数時間後にバックアップがトリガーされることがあります。

## <a name="next-steps"></a>次の手順

[Azure Policy に関する詳細情報](../governance/policy/overview.md)