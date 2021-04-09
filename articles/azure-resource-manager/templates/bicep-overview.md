---
title: Azure Resource Manager テンプレート用の Bicep 言語
description: Azure Resource Manager テンプレートを使用して Azure にインフラストラクチャをデプロイするための Bicep 言語について説明します。
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 74028c682b48a492c2e8f13bef538d1694370cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955909"
---
# <a name="what-is-bicep-preview"></a>Bicep とは (プレビュー)

Bicep は、Azure リソースを宣言によってデプロイするための言語です。 Azure Resource Manager テンプレート (ARM テンプレート) の開発には、JSON ではなく Bicep を使用できます。 Bicep では、簡潔な構文を提供して、コードの再利用のサポートを強化することで、作成エクスペリエンスを簡素化します。 Bicep は、ドメイン固有言語 (DSL) です。これは、特定のシナリオまたはドメイン向けに設計されていることを意味します。 これは、アプリケーションを作成するための一般的なプログラミング言語としては意図されていません。

テンプレートを作成するための JSON 構文は冗長になることがあり、複雑な式を必要とします。 Bicep であれば、JSON テンプレートの各種機能を失うことなくエクスペリエンスを向上させることができます。 ARM テンプレートのために JSON をわかりやすく抽出したものです。 各 Bicep ファイルは、標準の ARM テンプレートにコンパイルされます。 ARM テンプレートで有効なリソースの種類、API バージョン、およびプロパティは、Bicep ファイルで有効です。 現在のリリースでは、[既知の制限事項](#known-limitations)がいくつかあります。

Bicep は現在、プレビュー期間中です。 作業の状態を確認するには、[Bicep プロジェクトのリポジトリ](https://github.com/Azure/bicep)を参照してください。

Bicep の詳細については、次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>作業開始

Bicep の使用を開始するには、[ツールをインストール](bicep-install.md)します。

ツールをインストールしたら、[Bicep チュートリアル](./bicep-tutorial-create-first-bicep.md)を試してください。 このチュートリアル シリーズでは、Bicep の構造と機能について説明します。 Bicep ファイルをデプロイし、ARM テンプレートを同等の Bicep ファイルに変換します。

同等の JSON および Bicep のファイルを並べて表示するには、[Bicep プレイグラウンド](https://aka.ms/bicepdemo)を参照してください。

Bicep に変換したい既存の ARM テンプレートがある場合は、「[JSON と Bicep 間での ARM テンプレートの変換](bicep-decompile.md)」を参照してください。

## <a name="bicep-improvements"></a>Bicep の機能強化

Bicep では、同等の JSON と比較して、より簡単で簡潔な構文が提供されます。 `[...]` 式は使用しません。 代わりに、関数を直接呼び出したり、パラメーターと変数から値を取得したりします。 展開された各リソースにシンボリック名を与えます。それにより、テンプレートでそのリソースを参照しやすくなります。

たとえば、次の JSON からは、リソース プロパティからの出力値が返されます。

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Bicep では、同等の出力式をもっと簡単に記述できます。 次の例では、テンプレート内で定義されるリソースに対して、シンボリック名 **publicIP** を利用して同じプロパティが返されます。

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

構文の完全な比較については、「[テンプレートにおける JSON と Bicep の比較](compare-template-syntax.md)」参照してください。

Bicep では、リソース間の依存関係が自動的に管理されます。 リソースのシンボル名が別のリソース宣言で使用されている場合に `dependsOn` を設定しないようにすることができます。

Bicep を使用すると、プロジェクトを複数のモジュールに分割できます。

Bicep ファイルの構造は、JSON テンプレートよりも柔軟です。 ファイル内の任意の場所で、パラメーター、変数、および出力を宣言できます。 JSON では、テンプレートの対応するセクション内で、すべてのパラメーター、変数、および出力を宣言する必要があります。

Bicep の VS Code 拡張機能では、高度な検証と IntelliSense が提供されます。 たとえば、リソースのプロパティを取得するための拡張機能の IntelliSense を使用できます。

## <a name="known-limitations"></a>既知の制限事項

現在、次の制限があります。

* コピー ループでモードまたはバッチ サイズを設定できません。
* ループと条件を組み合わせることができません。
* `['a', 'b', 'c']` などの単一行のオブジェクトと配列はサポートされていません。

## <a name="faq"></a>よく寄せられる質問

**既存の言語を使用するのではなく、新しいものを作成するのはなぜですか?**

Bicep は、新しい言語ではなく、既存の ARM テンプレート言語のリビジョンと考えることができます。 構文は変更されましたが、コアとなる機能とランタイムは変わりません。

Bicep を開発する前に、既存のプログラミング言語を使用することを検討したことがありました。 対象ユーザーにとっては、別の言語を開始するよりも、Bicep を学習する方が容易であると判断しました。

**Terraform や他のサードパーティ製のコードとしてのインフラストラクチャ オファリングにエネルギーを集中させないのはなぜですか?**

構成言語とツールの好みはユーザーごとに異なります。 これらすべてのツールで、Azure での優れたエクスペリエンスが提供されるようにしたいと考えています。 Bicep は、その取り組みの一環です。

Terraform を使い慣れている場合は、切り替える理由はありません。 Microsoft は、Azure の Terraform を最高のものにするために尽力しています。

ARM テンプレートを選択したお客様の場合、Bicep によって作成エクスペリエンスが向上するものと確信しています。 また、Bicep は、コードとしてのインフラストラクチャを採用していないお客様の移行にも役立ちます。

**Bicep は Azure 専用ですか?**

Bicep は、完全なソリューションを Azure にデプロイすることに重点を置いた DSL です。 この目標を達成するには、Azure の外部にあるいくつかの API を使用する必要があります。 それらのシナリオのための機能拡張ポイントをご提供する予定です。

**既存の ARM テンプレートはどうなりますか?**

引き続き、今までと全く同様に機能します。 変更を加える必要はありません。 基になる ARM テンプレートの JSON 言語は引き続きサポートされます。 Bicep ファイルは JSON にコンパイルされ、その JSON はデプロイのために Azure に送信されます。

準備ができたら、[JSON ファイルを Bicep に変換](bicep-decompile.md)できます。

## <a name="next-steps"></a>次のステップ

[Bicep チュートリアル](./bicep-tutorial-create-first-bicep.md)を開始します。
