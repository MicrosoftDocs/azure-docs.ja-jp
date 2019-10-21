---
title: Azure Security Center のセキュリティ プレイブック | Microsoft Docs
description: このドキュメントは、Azure Security Center でセキュリティ プレイブックを使用して、セキュリティ インシデントへの対応を自動化する際に役立ちます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: faf297ef4b07b76f98bb7773c636cd8f6228bbc0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201725"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Azure Security Center のセキュリティ プレイブック (プレビュー)
このドキュメントは、Azure Security Center のセキュリティ プレイブックを使用して、セキュリティ関連の問題に対応する際に役立ちます。

## <a name="what-is-security-playbook-in-security-center"></a>Security Center のセキュリティ プレイブックとは
セキュリティ プレイブックとは、選択したアラートから特定のプレイブックがトリガーされると Security Center から実行できるプロシージャの集合です。 セキュリティ プレイブックは、Security Center で検出された特定のセキュリティ アラートへの応答を自動化および調整するのに役立ちます。 Security Center のセキュリティ プレイブックは、[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) に基づいています。つまり、Logic Apps テンプレートのセキュリティ カテゴリに用意されているテンプレートを使用することも、必要に応じてそれらを変更することもできます。また、[Azure Logic Apps ワークフロー](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app)を使用し、Security Center をトリガーとして使用する新しいプレイブックを作成することもできます。

> [!NOTE]
> プレイブックでは Azure Logic Apps が利用されるため、料金が適用されます。 詳細については、[Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) の価格ページを参照してください。

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Security Center からセキュリティ プレイブックを作成する方法
Security Center から新しいセキュリティ プレイブックを作成するには、次の手順に従います。

1.  **[Security Center]** ダッシュボードを開きます。
2.  左側のウィンドウで、 **[Automation & Orchestration]\(オートメーションとオーケストレーション\)** セクションの **[Playbooks (Preview)]\(プレイブック (プレビュー)\)** をクリックします。

    ![ロジック アプリ](./media/security-center-playbooks/security-center-playbooks-fig17.png)

3. **[Security Center - Playbooks (Preview)]\(Security Center - プレイブック (プレビュー)\)** ページで、 **[追加]** ボタンをクリックします。

    ![ロジック アプリを作成する](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. **[ロジック アプリの作成]** ページで、新しいロジック アプリを作成するために要求された情報を入力し、 **[作成]** ボタンをクリックします。 作成が完了すると、新しいプレイブックが一覧に表示されます。 表示されない場合は、 **[更新]** ボタンをクリックしてください。 表示されたら、それをクリックしてこのプレイブックの編集を開始します。

    ![ロジック アプリを作成する](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. **ロジック アプリ デザイナー**が表示されます。 **[空のロジック アプリ]** をクリックして、新しいプレイブックを作成します。 また、カテゴリで **[セキュリティ]** を選択すると、テンプレートのいずれかを使用することもできます。

    ![ロジック アプリ デザイナー](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. **[すべてのコネクタとトリガーを検索する]** フィールドに「*Azure Security Center*」と入力し、 **[Azure Security Center 通知への応答がトリガーされるとき]** を選択します。

    ![トリガー](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. これで、プレイブックをトリガーしたときの動作を定義できます。 アクション、論理条件、スイッチ ケースの条件、またはループを追加できます。

    ![ロジック アプリ デザイナー](./media/security-center-playbooks/security-center-playbooks-fig5.png)

## <a name="how-to-run-a-security-playbook-in-security-center"></a>Security Center でセキュリティ プレイブックを実行する方法

調整して他のサービスからより多くの情報を取得する場合、または修復する場合は、Security Center でセキュリティ プレイブックを実行できます。 プレイブックにアクセスするには、次の手順に従います。

1.  **[Security Center]** ダッシュボードを開きます。
2.  左側のウィンドウの **[脅威検出]** で **[Security incidents & alerts]\(セキュリティ インシデントとアラート\)** をクリックします。

    ![アラート](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  調査するアラートをクリックします。
4.  アラートのページの上部にある **[プレイブックの実行]** ボタンをクリックします。

    ![プレイブックを実行する](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. [プレイブック] ページで、実行するプレイブックを選択し、 **[実行]** ボタンをクリックします。 トリガーする前にプレイブックを表示するには、それをクリックすると、デザイナーが開きます。

    ![プレイブック](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>履歴

プレイブックの実行後に、以前の実行に加え、以前に実行したプレイブックの状態の詳細を含むステップにもアクセスできます。 この履歴はアラートごとにコンテキストが設定されています。つまり、このページに表示されるプレイブックの履歴は、このプレイブックをトリガーしたアラートと相関関係にあります。

![履歴](./media/security-center-playbooks/security-center-playbooks-fig16.png)

特定のプレイブックの実行の詳細を表示するには、プレイブック自体をクリックします。その結果、ロジック アプリの実行ページが表示され、ワークフロー全体が示されます。

![詳細](./media/security-center-playbooks/security-center-playbooks-fig14.png)

このワークフローでは、各タスクの実行にかかった時間が表示されます。また、各タスクを展開して結果を確認することができます。

### <a name="changing-an-existing-playbook"></a>既存のプレイブックの変更

Security Center で既存のプレイブックを変更して、アクションまたは条件を追加できます。 これを行うには、変更するプレイブックの名前を [プレイブック] タブでクリックするだけです。そうすると、ロジック アプリ デザイナーが開きます。

> [!NOTE]
> Azure ロジック アプリを使用して独自のプレイブックを作成する方法の詳細については、[クラウド アプリとクラウド サービスの間のプロセスを自動化する最初のロジック アプリ ワークフローの作成](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app)に関する記事を参照してください。


## <a name="see-also"></a>関連項目
このドキュメントでは、Azure Security Center でプレイブックを使用する方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
