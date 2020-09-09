---
title: ルール エンジンを使用して Standard Azure CDN で HTTPS を強制する | Microsoft Docs
description: Microsoft Standard Azure Content Delivery Network (Azure CDN) に対するルール エンジンを使用して、特定種類のコンテンツの配信ブロック、キャッシュ ポリシーの定義、HTTP ヘッダーの変更など、Azure CDN による HTTP 要求の処理方法をカスタマイズします。 この記事では、ユーザーを HTTPS にリダイレクトするルールを作成する方法について説明します。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 1c596bf10d8afbce504b5abf04eacb877989521a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887543"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN に対する Standard ルール エンジンを設定する

この記事では、Azure Content Delivery Network (Azure CDN) に対する Standard ルール エンジンを設定および使用する方法について説明します。

## <a name="standard-rules-engine"></a>Standard ルール エンジン

Azure CDN に対する Standard ルール エンジンを使用して、HTTP 要求の処理方法をカスタマイズできます。 たとえば、ルール エンジンを使うと、コンテンツ配信で特定のプロトコルを強制的に使用したり、キャッシュ ポリシーを定義したり、HTTP ヘッダーを変更したりすることができます。 この記事では、ユーザーを HTTPS に自動的にリダイレクトするルールを作成する方法について説明します。 

> [!NOTE]
> この記事で説明されているルール エンジンは、Microsoft の標準 Standard Azure CDN に対してのみ使用できます。 

## <a name="redirect-users-to-https"></a>ユーザーを HTTPS にリダイレクトする

1. Microsoft プロファイルで、Azure Content Delivery Network に移動します。

1. **[CDN プロファイル]** ページで、ルールを作成するエンドポイントを選択します。
  
1. **[ルール エンジン]** タブを選択します。
   
    **[ルール エンジン]** ペインが開き、使用可能なグローバル ルールの一覧が表示されます。 
   
    [![Azure CDN の新しいルール ページ](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 複数のルールが一覧表示される順序は、ルールの処理方法に影響します。 あるルールで指定されているアクションは、後続のルールで上書きされる可能性があります。
   >

1. **[ルールの追加]** を選択し、ルールの名前を入力します。 ルール名は文字で始める必要があり、数字と文字のみを含めることができます。

1. ルールを適用する要求の種類を指定するには、一致条件を作成します。
    1. **[条件の追加]** を選択し、 **[要求プロトコル]** 一致条件を選択します。
    1. **[演算子]** では、 **[等号]** を選択します。
    1. **[値]** では、 **[HTTP]** を選択します。
   
   [![Azure CDN ルールの一致条件](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > **[条件の追加]** ドロップダウン リストでは、複数の一致条件から選択できます。 一致条件の詳細な一覧については、[Standard ルール エンジンでの一致条件](cdn-standard-rules-engine-match-conditions.md)に関する記事をご覧ください。
   
1. 一致条件を満たす要求に適用するアクションを選択します。
   1. **[アクションの追加]** を選択し、 **[URL のリダイレクト]** を選択します。
   1. **[種類]** では、 **[検出 (302)]** を選択します。
   1. **[プロトコル]** は **[HTTPS]** を選択します。
   1. 他のすべてのフィールドは空白のままにし、受信した値を使用します。
   
   [![Azure CDN のルール アクション](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > **[アクションの追加]** ドロップダウン リストでは、複数のアクションから選択できます。 アクションの詳細な一覧については、[Standard ルール エンジンでのアクション](cdn-standard-rules-engine-actions.md)に関する記事をご覧ください。

6. **[保存]** を選択して新しいルールを保存します。 これで、ルールを使用できるようになりました。
   
   > [!IMPORTANT]
   > ルールの変更が Azure CDN 全体に反映されるには、最大で 15 分かかります。
   >
   

## <a name="next-steps"></a>次のステップ

- [Azure CDN の概要](cdn-overview.md)
- [Standard のルール エンジンのリファレンス](cdn-standard-rules-engine-reference.md)
- [Standard ルール エンジンの一致条件](cdn-standard-rules-engine-match-conditions.md)
- [Standard ルール エンジンのアクション](cdn-standard-rules-engine-actions.md)
