---
title: Horizon SDK
description: Azure Defender for IoT 開発者は Horizon SDK を使用すると、自動化された Defender for IoT ネットワーク分析プログラムで処理できるようにネットワーク トラフィックをデコードする dissector プラグインを設計できます。
ms.date: 1/13/2021
ms.topic: article
ms.openlocfilehash: 500f1d78a6a2ab91284c993a5c56ef8e679db83f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782488"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Horizon の独自プロトコル dissector

Horizon は、独自のプロトコルを実行する IoT および ICS デバイスのセキュリティ保護のために使用されるオープン開発環境 (ODE) です。

この環境により、お客様とテクノロジ パートナー向けに次のソリューションが提供されます。

- 一般的、独自、カスタムのプロトコル、またはすべての標準に逸脱したプロトコルの無制限で完全なサポート。 

- DPI 開発における新しいレベルの柔軟性と範囲。

- Defender for IoT プラットフォームのバージョンをアップグレードすることなく、OT の可視性と制御を指数的に拡張するツール。

- 機密情報を危険にさらすことなく、独自の開発を可能にするセキュリティ。

Azure Defender for IoT 開発者は Horizon SDK を使用すると、自動化された Defender for IoT ネットワーク分析プログラムで処理できるようにネットワーク トラフィックをデコードする dissector プラグインを設計できます。

プロトコル dissector を外部プラグインとして開発し、広範な種類の Defender for IoT サービスに統合します。 たとえば、監視、アラート、レポートの機能を提供するサービスなどです。

## <a name="secure-development-environment"></a>セキュリティで保護された開発環境 

Horizon ODE を使用すると、組織の外部で共有することができない、カスタムまたは独自のプロトコルを開発することが可能になります。 共有できない理由には、法規制や企業ポリシーなどがあります。

dissector プラグインの開発に次のことは伴いません。 

- プロトコルがどのように定義されているかに関する財産的価値のある情報を公開する。

- 機密 PCAP を共有する。

- コンプライアンスの規制に違反する。

## <a name="customization-and-localization"></a>カスタマイズとローカライズ  

次のようなさまざまなカスタマイズ オプションが、この SDK でサポートされています。

  - 関数コードのテキスト。 

  - アラート、イベント、プロトコルのパラメーターのテキストの完全なローカライズ。 詳細については、「[マッピング ファイルの作成 (JSON)](#create-mapping-files-json)」を参照してください。

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="完全にローカライズされたアラートの表示。":::

## <a name="horizon-architecture"></a>Horizon のアーキテクチャ

このアーキテクチャのモデルには、3 つの製品レイヤーが含まれています。

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender for IoT プラットフォーム レイヤー

Defender for IoT プラットフォームのバージョンをアップグレードすることなく、Defender for IoT プラットフォームのカスタム dissector プラグインの即時統合とリアルタイムでの監視が可能になります。

## <a name="defender-for-iot-services-layer"></a>Defender for IoT サービス レイヤー

各サービスは特定のプロトコルから切り離されたパイプラインとして設計されているため、より効率的な独立した開発が可能になります。

各サービスは、特定のプロトコルから切り離されたパイプラインとして設計されています。 サービスによってパイプライン上のトラフィックがリッスンされます。 プラグイン データとセンサーでキャプチャされたトラフィックの操作、デプロイされたプロトコルのインデックス作成およびトラフィックのペイロード分析によって、より効率的で独立した開発が実現します。

## <a name="custom-dissector-layer"></a>カスタム dissector レイヤー 

Defender for IoT 専用 SDK (C++ の実装と JSON 構成を含む) を使用した、以下のためのプラグインの作成を可能にします。 

- プロトコルの識別方法を定義する

- トラフィックから抽出するフィールドをマップする方法を定義し、それらを抽出する 

- Defender for IoT サービスと統合する方法を定義する

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="組み込みのレイヤー。":::

Defender for IoT には、一般的なプロトコル用の基本的な dissector が用意されています。 これらのプロトコルを基にしてご自身の dissector を構築できます。

## <a name="before-you-begin"></a>開始する前に

## <a name="what-this-sdk-contains"></a>この SDK に含まれる内容 

このキットには、開発に必要なヘッダー ファイルが含まれています。 開発プロセスには、この SDK で説明されている基本的なステップとオプションの高度なステップが必要です。

ヘッダー ファイルやその他のリソースを受け取る方法については、<support@cyberx-labs.com> にお問い合わせください。

## <a name="about-the-environment-and-setup"></a>環境とセットアップについて 

### <a name="requirements"></a>必要条件 

- 推奨される開発環境は Linux です。 Windows 環境で開発する場合は、Linux システムを使用している VM の使用を検討してください。

- コンパイル プロセスには GCC 7.4.0 以降を使用します。 C++17 でサポートされている stdlib の任意の標準クラスを使用します。

- Defender for IoT バージョン 3.0 以降。

### <a name="process"></a>Process

1. C または C++ 開発者向けの Eclipse IDE を[ダウンロード](https://www.eclipse.org/)します。 必要に応じて別の IDE を使用してもかまいません。 このドキュメントでは、Eclipse IDE を使用した構成方法について説明します。

1. Eclipse IDE を起動し、ワークスペース (プロジェクトが格納される場所) を構成したら、**Ctrl + n** キーを押して、C++ プロジェクトとして作成します。

1. 次の画面で、名前を開発するプロトコルに設定し、プロジェクトの種類として `Shared Library` と `AND Linux GCC` を選択します。

1. **[C/C++ Build]\(C/C++ ビルド\)**  >  **[Settings]\(設定\)**  >  **[Tool Settings]\(ツール設定\)**  >  **[GCC C++ Compiler]\(GCC C++ コンパイラ\)**  >  **[Miscellaneous]\(その他\)**  >  **[Tick Position Independent Code]\(独立コードの目盛り位置\)** でプロジェクトのプロパティを編集します。

1. SDK に付属しているコード例を貼り付け、それをコンパイルします。

1. アーティファクト (ライブラリ、config.json、メタデータ) を tar.gz ファイルに追加し、ファイル拡張子を \<XXX>.hdp に変更します。ここで、\<XXX> はプラグインの名前です。

### <a name="research"></a>研究 

開始する前に、次のことを確認します。

- プロトコル仕様 (ある場合) を読んだ。

- どのプロトコル フィールドを抽出するかがわかっている。

- マッピングの目的を計画済みである。

## <a name="about-plugin-files"></a>プラグイン ファイルについて 

開発プロセス中に、3 つのファイルが定義されます。

### <a name="json-configuration-file-required"></a>JSON 構成ファイル (必須) 

このファイルには、dissector の ID と宣言、依存関係、統合要件、検証パラメーター、(値を名前に、数値をテキストに変換するための) マッピング定義を定義する必要があります。 詳細については、次のリンクを参照してください。

- [構成ファイルの準備 (JSON)](#prepare-the-configuration-file-json)

- [実装コード検証の準備](#prepare-implementation-code-validations)

- [デバイス メタデータの抽出](#extract-device-metadata)

- [インデックス サービス (ベースライン) への接続](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>実装コード: C++ (必須)

実装コード (CPP) で生のトラフィックを解析し、それをサービス、クラス、関数コードなどの値にマップします。 レイヤー フィールドを抽出し、JSON 構成ファイルのインデックス名にマップします。 CPP から抽出するフィールドは、構成ファイルで定義されます。 詳細については、「[実装コードの準備 (C++)](#prepare-the-implementation-code-c)」を参照してください。

### <a name="mapping-files-optional"></a>マッピング ファイル (省略可能)

ご自身のエンタープライズ環境のニーズを満たすように、プラグインの出力テキストをカスタマイズすることができます。

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migration":::

マッピング ファイルを定義および更新して、コードを変更せずにテキストを更新することができます。 各ファイルで 1 つまたは複数のフィールドをマップできます。

  - フィールド値の名前へのマッピング (たとえば、1: リセット、2: 開始、3: 停止)。

  - 複数言語をサポートするためのテキストのマッピング。

詳細については、「[マッピング ファイルの作成 (JSON)](#create-mapping-files-json)」を参照してください。

## <a name="create-a-dissector-plugin-overview"></a>dissector プラグインの作成 (概要)

1. 「[環境とセットアップについて](#about-the-environment-and-setup)」セクションを確認します。

2.  [実装コード (C++) を準備](#prepare-the-implementation-code-c)します。 **template.json** ファイルをコピーし、ご自身のニーズに合わせて編集します。 キーを変更しないでください。 

3. [構成ファイル (JSON) を準備](#prepare-the-configuration-file-json)します。 **template.cpp** ファイルをコピーし、オーバーライド メソッドを実装します。 詳細については、「[horizon::protocol::BaseParser](#horizonprotocolbaseparser)」を参照してください。

4. [実装コード検証を準備](#prepare-implementation-code-validations)します。

## <a name="prepare-the-implementation-code-c"></a>実装コードの準備 (C++)

CPP ファイルは、次のことが行われるパーサーです。

- パケットのヘッダーとペイロード (ヘッダー長やペイロード構造など) の検証する。

- ヘッダーとペイロードから、定義されたフィールドにデータを抽出する。

- JSON ファイルによって構成されたフィールド抽出を実装する。

### <a name="what-to-do"></a>対処

テンプレート **.cpp** ファイルをコピーし、オーバーライド メソッドを実装します。 詳細については、「[horizon::protocol::BaseParser](#horizonprotocolbaseparser)」を参照してください。

### <a name="basic-c-template-sample"></a>基本的な C++ テンプレートのサンプル 

このセクションでは、サンプルの Defender for IoT Horizon プロトコルの標準的な機能を使用した、基本的なプロトコル テンプレートについて説明します。

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>基本的な C++ テンプレートの説明  

このセクションでは、基本的なプロトコル テンプレートを示し、サンプルの Defender for IoT Horizon プロトコルの標準的な機能について説明します。 

### <a name="include-pluginpluginh"></a>#include “plugin/plugin.h”

プラグインによって使用される定義。 ヘッダー ファイルには、開発を完了するために必要なすべてのものが含まれています。

### <a name="horizonprotocolbaseparser"></a>horizon::protocol::BaseParser

Horizon インフラストラクチャとプラグイン レイヤーとの間の通信インターフェイス。 詳細については、「[Horizon のアーキテクチャ](#horizon-architecture)」を参照してください。

processLayer は、データの処理に使用されるメソッドです。

- 関数コードの最初のパラメーターは、以前に処理されたデータの取得、新しいフィールドおよびレイヤーの作成に使用される処理ユーティリティです。

- 関数コードの 2 番目のパラメーターは、前のパーサーから渡された現在のデータです。

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

パーサーのインスタンスを作成するために使用します。

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>プロトコル関数のコード サンプル 

このセクションでは、コード番号 (2 バイト) とメッセージ長 (4 バイト) を抽出する方法の例を示します。

この処理は、JSON 構成ファイルに指定されているエンディアンに従って行われます。つまり、プロトコルが "*リトル エンディアン*" であり、センサーがリトル エンディアンのコンピューター上で実行される場合は、それが変換されます。

データを格納するためのレイヤーも作成されます。 新しいフィールドを作成するには、処理ユーティリティの *fieldsManager* を使用します。 フィールドには、次の型のうち 1 つのみを格納できます。*STRING*、*NUMBER*、*RAW DATA*、*ARRAY* (特定の型)、または *COMPLEX*。 このレイヤーには、数値、生データ、または ID を含む文字列を含めることができます。

次のサンプルでは、次の 2 つのフィールドが抽出されます。

- `function_code_number`

- `headerLength`

新しいレイヤーが作成され、抽出されたフィールドがそこにコピーされます。

次のサンプルでは、プラグイン処理用に実装された主要なロジックである、特定の関数について説明します。

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>関連する JSON フィールド 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="関連する JSON フィールド。":::

## <a name="prepare-the-configuration-file-json"></a>構成ファイルの準備 (JSON)

Horizon SDK には、データを格納および転送するための軽量な形式である標準の JavaScript Object Notation (JSON) が使用されており、専用のスクリプト言語は必要ありません。

このセクションでは、最低限の JSON 構成宣言と、関連する構造について説明し、プロトコルを定義するサンプル構成ファイルを示します。 このプロトコルは、デバイス探索サービスと自動的に統合されます。

## <a name="file-structure"></a>ファイル構造

次のサンプルでは、ファイル構造について説明します。

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="ファイル構造のサンプル。":::

### <a name="what-to-do"></a>対処

テンプレートの `config.json` ファイルをコピーし、ご自身のニーズに合わせて編集します。 キーを変更しないでください。 キーは、[JSON 構成ファイルのサンプル](#sample-json-configuration-file)で赤色で示されています。 

### <a name="file-naming-requirements"></a>ファイルの名前付けに関する要件 

JSON 構成ファイルは `config.json` 形式で保存する必要があります。

### <a name="json-configuration-file-fields"></a>JSON 構成ファイルのフィールド

このセクションでは、定義する JSON 構成フィールドについて説明します。 フィールドの "*ラベル*" は変更しないでください。

### <a name="basic-parameters"></a>基本パラメーター

このセクションでは、基本的なパラメーターについて説明します。

| パラメーターのラベル | 説明 | Type |
|--|--|--|
| **ID** | プロトコルの名前。 既定値を削除し、表示されるプロトコルの名前を追加します。 | String |
| **endianess** | マルチバイト データをエンコードする方法を定義します。 "little" または "big" という用語のみを使用します。 プロトコルの仕様またはトラフィックの記録から取得されます | String |
| **sanity_failure_codes** | コードの ID に関するサニティの矛盾がある場合に、パーサーから返されるコードです。 C++ でのマジック ナンバーの検証のセクションを参照してください。 | String |
| **malformed_codes** | これらは適切に識別されたコードですが、エラーが検出されました。 たとえば、フィールドの長さが短すぎる場合や長すぎる場合、または値が無効である場合などです。 | String |
| **dissect_as** | 特定のプロトコル トラフィックを受け取る場所を定義する配列。 | TCP や UDP、ポートなどです。 |
| **fields** | どのフィールドがトラフィックから抽出されるかを示す宣言。 各フィールドは、独自の ID (名前) と型 (numeric、string、raw、array、complex) を持ちます。 たとえば、実装パーサー ファイル内に抽出されるフィールドである [function](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) があります。 構成ファイルに記述されるフィールドは、レイヤーに追加できるものだけです。 |  |

### <a name="other-advanced-fields"></a>その他の高度なフィールド 

このセクションでは、その他のフィールドについて説明します。

| パラメーターのラベル | [説明] |
|-----------------|--------|
| **allow lists** | プロトコル値のインデックスを作成し、データ マイニング レポートに表示できます。 これらのレポートには、ネットワークのベースラインが反映されます。 :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="データ マイニング ビューのサンプル。"::: <br /> 詳細については、「[インデックス サービス (ベースライン) への接続](#connect-to-an-indexing-service-baseline)」を参照してください。 |
| **firmware** | プラグイン プロトコルに対して、ファームウェア情報の抽出、インデックス値の定義、ファームウェア アラートのトリガーを行うことができます。 詳細については、「[ファームウェア データの抽出](#extract-firmware-data)」を参照してください。 |
| **value_mapping** | マッピング ファイルを定義および更新することによって、ご自身のエンタープライズ環境のニーズを満たすために、プラグインの出力テキストをカスタマイズすることができます。 たとえば、言語ファイルにマップします。 コードに変更を加えたり影響を与えたりすることなく、テキストに変更を簡単に実装できます。 詳細については、「[マッピング ファイルの作成 (JSON)](#create-mapping-files-json)」を参照してください。 |

## <a name="sample-json-configuration-file"></a>JSON 構成ファイルのサンプル

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>実装コード検証の準備

このセクションでは、C++ の実装コード検証機能について説明し、サンプル コードを示します。 2 つのレイヤーの検証を使用できます。

- サニティ。

- 形式に誤りがあるコード。

機能するプラグインをビルドするために検証コードを作成する必要はありません。 検証コードを準備しない場合は、正常な処理を示すものとしてセンサーのデータ マイニング レポートを確認できます。

フィールド値をマッピング ファイル内のテキストにマップし、処理に影響を与えることなく、シームレスに更新することができます。

## <a name="sanity-code-validations"></a>サニティ コードの検証 

これにより、送信されたパケットがプロトコルの検証パラメーターに一致することが検証されます。これにより、トラフィック内のプロトコルを識別できます。

たとえば、最初の 8 バイトを "*マジック番号*" として使用します。 サニティが失敗した場合は、サニティ エラー応答が返されます。

以下に例を示します。

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

他の関連するプラグインがデプロイされている場合は、それらに照らしてパケットが検証されます。

## <a name="malformed-code-validations"></a>形式に誤りがあるコードの検証 

不正形式の検証は、プロトコルが肯定的に検証された後に使用されます。

プロトコルに基づいてパケットを処理できなかった場合は、エラー応答が返されます。

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="形式に誤りがあるコード":::

## <a name="c-sample-with-validations"></a>検証を含む C++ サンプル

次の例に示すように、関数に従ってプロセスが実行されます。

### <a name="function-20"></a>関数 20 

- ファームウェアとして処理されます。

- フィールドが関数に従って読み取られます。

- フィールドがレイヤーに追加されます。

### <a name="function-10"></a>関数 10 

- 関数には、より具体的な操作である別のサブ関数が含まれています。

- サブ関数が読み取られてレイヤーに追加されます。

これが完了すると、処理が完了します。 dissector レイヤーが正常に処理されたかどうかが戻り値で示されます。 されている場合は、レイヤーが使用可能になります。

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>デバイス メタデータの抽出

資産に関する次のメタデータを抽出できます。

  - `Is_distributed_control_system` - プロトコルが分散コントロール システムの一部であるかどうかを示します。 (例: SCADA プロトコルは false になります)

  - `Has_protocol_address` – プロトコル アドレスがあるかどうかを示します。現在のプロトコルの特定のアドレス (例: MODBUS ユニット識別子)

  - `Is_scada_protocol` - プロトコルが OT ネットワークに固有であるかどうかを示します

  - `Is_router_potential` - プロトコルが主にルーターによって使用されるかどうかを示します。 たとえば、LLDP、CDP、または STP です。

これを実現するには、メタデータ プロパティを使用して JSON 構成ファイルを更新する必要があります。

## <a name="json-sample-with-metadata"></a>メタデータを含む JSON サンプル 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>プログラミング コードの抽出 

プログラミング イベントが発生したときに、コードの内容を抽出できます。 抽出されたコンテンツを使用して次のことができます。

- さまざまなプログラミング イベントでコード ファイルの内容を比較する。

- 未承認のプログラミングに関するアラートをトリガーする。  

- プログラミング コード ファイルを受信するイベントをトリガーする。

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="プログラミング変更ログ。":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="ボタンをクリックしてプログラミングを表示します。":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="承認されていない PLC プログラミングのアラート。":::

これを実現するには、`code_extraction` プロパティを使用して JSON 構成ファイルを更新する必要があります。 

### <a name="json-configuration-fields"></a>JSON 構成フィールド 

このセクションでは、JSON 構成フィールドについて説明します。 

- **method**

  プログラミング イベント ファイルの受信方法を示します。 

  ALL (各プログラミング アクションで、変更のないファイルがある場合でも、すべてのコード ファイルが受信されます)。

- **file_type**  

  コードのコンテンツの種類を示します。  

  TEXT (各コード ファイルにはテキスト情報が含まれています)。

- **code_data_field**
  
  コードの内容を提供するために使用する実装フィールドを示します。

  FIELD。

- **code_name_field**

  コーディング ファイルの名前を提供するために使用する実装フィールドを示します。

  FIELD。

- **size_limit**

  各コーディング ファイルの内容のサイズ制限をバイト単位で示します。コード ファイルは、設定された制限を超えた場合は削除されます。 このフィールドが指定されていない場合、既定値は 15,000,000 つまり 15 MB です。

  数値。

- **metadata**

  コード ファイルの追加情報を示します。

  2 つのプロパティを持つオブジェクトを含む配列:

    - name (String) - メタデータ キー XSense で現在はユーザー名キーのみがサポートされていることを示します。
 
    - value (Field) - メタデータのデータを提供するために使用する実装フィールドを示します。

## <a name="json-sample-with-programming-code"></a>プログラミング コードを含む JSON のサンプル

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>カスタムの Horizon アラート

プロトコル関数のコードによっては、エラーが発生することがあります。 たとえば、常に特定の温度で保管する必要がある、特定の化学薬品の入ったコンテナーをプロトコルで制御しているとします。 この場合、温度計でのエラーを示す関数コードが存在する可能性があります。 たとえば、関数コードが 25 の場合、Web コンソールで、コンテナーに問題があることを示すアラートをトリガーできます。 このような場合は、混みいったパケット アラートを定義できます。

プラグインの `config.json` に **alerts** パラメーターを追加します。

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>JSON 構成フィールド

このセクションでは、JSON 構成フィールドについて説明します。 

| フィールド名 | 説明 | 設定可能な値 |
|--|--|--|
| **ID** | 1 つのアラート ID を表します。 このコンテキストで一意である必要があります。 | 数値 0 - 10000 |
| **message** | ユーザーに表示される情報。 このフィールドには、さまざまなフィールドを使用できます。 | お使いのプロトコルまたは下位のレイヤーのプロトコルにある任意のフィールドを使用します。 |
| **title** | アラートのタイトル |  |
| **式 (expression)** | このアラートをいつポップアップ表示するか。 | 下位レイヤーまたは現在のレイヤーで検出された数値フィールドをどれでも使用します。</br></br> 各フィールドは `{}` を使用したラッパーである必要があります。それを SDK でフィールドとして検出するために、サポートされている論理演算子は次のとおりです。</br> == - 等しい</br> <= - 以下</br> >= - 以上</br> > - より大きい</br> < - より小さい</br> ~= - 等しくない |

## <a name="more-about-expressions"></a>式の詳細

Horizon SDK で式が呼び出され、それが *true* になるたびに、センサーでアラートがトリガーされます。

同じアラートに複数の式を含めることができます。 たとえば、次のように入力します。

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

この式は、パケット ipv4 src が 10.10.10.10 の場合にのみ関数コードを検証します。 これは、数値表現での IP アドレスの未加工の表現です。

式を結合するには、`and` または `or` を使用できます。

## <a name="json-sample-custom-horizon-alerts"></a>カスタム Horizon アラートの JSON のサンプル

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>インデックス サービス (ベースライン) への接続

プロトコル値のインデックスを作成し、データ マイニング レポートに表示できます。

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="データ マイニング オプションのビュー。":::

これらの値は、任意の言語で、後から特定のテキストにマップすることができます。たとえば、テキストとしての数値のマッピングや情報の追加などです。

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migration":::

詳細については、「[マッピング ファイルの作成 (JSON)](#create-mapping-files-json)」を参照してください。

以前に解析されたプロトコルの値を使用して、追加情報を抽出することもできます。

たとえば、TCP に基づく値の場合、IPv4 レイヤーの値を使用できます。 このレイヤーから、パケットの送信元や送信先などの値を抽出できます。

これを実現するには、`whitelists` プロパティを使用して JSON 構成ファイルを更新する必要があります。

## <a name="allow-list-data-mining-fields"></a>許可リスト (データ マイニング) フィールド

次の許可リスト フィールドを使用できます。

- name – インデックス作成に使用される名前。

- alert_title – イベントについて説明する短い一意のタイトルです。

- alert_text – 追加情報

複数の許可リストを追加でき、インデックス作成における完全な柔軟性が実現します。

## <a name="json-sample-with-indexing"></a>インデックス作成を含む JSON サンプル 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>ファームウェア データの抽出

プラグイン プロトコルに対して、ファームウェア情報の抽出、インデックス値の定義、ファームウェア アラートのトリガーを行うことができます。 たとえば、次のように入力します。

- ファームウェアのモデルまたはバージョンを抽出します。 この情報は、CVE を識別するためにさらに利用できます。

- 新しいファームウェア バージョンが検出されたときにアラートをトリガーします。

これを実現するには、ファームウェア プロパティを使用して JSON 構成ファイルを更新する必要があります。

## <a name="firmware-fields"></a>ファームウェアのフィールド

このセクションでは、JSON のファームウェア構成フィールドについて説明します。

- **name**
  
  センサー コンソールでのフィールドの表示方法を示します。

- **value**

 データを提供するために使用する実装フィールドを示します。 

- **firmware_index:**

  次のいずれかを選択します。  
  - **モデル**:デバイス モデル。 CVE の検出を可能にします。
  - **serial**: デバイスのシリアル番号。 シリアル番号は、常にすべてのプロトコルで使用できるとは限りません。 この値はデバイスごとに一意です。
  - **rack**: デバイスがラックの一部である場合に、ラック識別子を示します。
  - **slot**: デバイスがラックの一部である場合に、スロット識別子を示します。  
  - **module_address**: モジュールを別のデバイスの背後に表示できる場合は、階層を示すために使用します。 より簡単なプレゼンテーションであるラック スロットの組み合わせの代わりに適用できます。  
  - **firmware_version**: デバイスのバージョンを示します。 CVE の検出を可能にします。
  - **alert_text**: ファームウェアの偏差を説明するテキスト (バージョンの変更など) を示します。  
  - **index_by**: デバイスの識別とインデックス作成に使用するフィールドを示します。 次の例では、デバイスは IP アドレスによって識別されます。 特定のプロトコルでは、より複雑なインデックスを使用できます。 たとえば、別のデバイスが接続されていて、その内部パスを抽出する方法がわかっているとします。 たとえば、IP アドレスとユニット識別子の異なる組み合わせとしての MODBUS ユニット ID をインデックスの一部として使用できます。
  - **firmware_fields**: どのフィールドがデバイス メタデータ フィールドであるかを示します。 この例では、model、revision、および name が使用されます。 各プロトコルで独自のファームウェア データを定義できます。

## <a name="json-sample-with-firmware"></a>ファームウェアを含む JSON サンプル 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>デバイスの属性の抽出 

デバイスで使用可能な情報を、インベントリ、データ マイニング、およびその他のレポートで強化できます。

- 名前

- Type

- ベンダー

- オペレーティング システム

これを実現するには、**Properties** プロパティを使用して JSON 構成ファイルを更新する必要があります。 

これは、基本的なプラグインを記述して必要なフィールドを抽出した後に行うことができます。

## <a name="properties-fields"></a>プロパティのフィールド

このセクションでは、JSON のプロパティ構成フィールドについて説明します。 

**config_file** 

`key` フィールド内の各キーの処理方法を定義するファイル名を格納します。 構成ファイル自体は JSON 形式であること、およびプラグイン プロトコル フォルダーの一部として含まれている必要があります。

JSON の各キーは、パケットからこのキーを抽出するときに実行される一連のアクションを定義します。

各キーは以下を持つことができます。

- **パケット データ** - パケットから抽出されたデータに基づいて更新されるプロパティを示します (そのデータを提供するために使用される実装フィールド)。

- **静的データ** - 更新する必要がある `property-value` アクションの定義済みセットを示します。

このファイルで構成できるプロパティは次のとおりです。 

- **Name** - デバイス名を示します。

- **Type** - デバイスの種類を示します。

- **Vendor** - デバイスの製造元を示します。

- **Description** - デバイスのファームウェア モデル ("モデル" よりも低い優先度) を示します。

- **operatingSystem** - デバイスのオペレーティング システムを示します。

### <a name="fields"></a>フィールド

| フィールド | 説明 |
|--|--|
| key | キーを示します。 |
| value | データを提供するために使用する実装フィールドを示します。 |
| is_static_key | `key` フィールドがパケットからの値として派生しているかどうか、または事前に定義された値であるかどうかを示します。 |

### <a name="working-with-static-keys-only"></a>静的キーのみを使用する

静的キーを使用している場合は、`config.file` を構成する必要はありません。 JSON ファイルのみを構成できます。

## <a name="json-sample-with-properties"></a>プロパティを含む JSON サンプル 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>マッピング ファイルの作成 (JSON)

ファイルを定義してマッピングを更新することによって、ご自身のエンタープライズ環境のニーズを満たすために、プラグインの出力テキストをカスタマイズすることができます。 コードに変更を加えたり影響を与えたりすることなく、テキストに変更を簡単に実装できます。 各ファイルで 1 つまたは複数のフィールドをマップできます。

- フィールド値の名前へのマッピング (たとえば、1: リセット、2: 開始、3: 停止)。

- 複数言語をサポートするためのテキストのマッピング。

2 種類のマッピング ファイルを定義できます。

  - [単純なマッピング ファイル](#simple-mapping-file)。

  - [依存関係のマッピング ファイル](#dependency-mapping-file)。

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="イーサネット":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="未処理のアラートのビュー。":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="既知のポリシー違反の一覧。":::

## <a name="file-naming-and-storage-requirements"></a>ファイルの名前付けと格納の要件 

マッピング ファイルは、メタデータ フォルダーの下に保存する必要があります。

ファイルの名前は、JSON 構成ファイルの ID と一致している必要があります。

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="JSON 構成ファイルのサンプル。":::

## <a name="simple-mapping-file"></a>単純なマッピング ファイル 

次のサンプルは、基本的な JSON ファイルをキー値として示しています。

許可リストを作成し、それに 1 つ以上のマップされたフィールドが含まれている場合。 値は、数値、文字列、または任意の型から、マッピング ファイルに示されている書式付きテキストに変換されます。

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>依存関係のマッピング ファイル 

ファイルが依存関係のファイルであることを示すには、マッピング構成にキーワード `dependency` を追加します。

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

このファイルには、依存関係フィールドと関数フィールドとの間のマッピングを含めます。 たとえば、関数とサブ関数との間です。 サブ関数は、指定された関数に従って変更されます。

次に示すように、以前に構成した許可リストには、依存関係の構成はありません。

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

依存関係は、特定の値またはフィールドに基づくことができます。 次の例では、フィールドに基づいています。 値を基にしている場合は、マッピング ファイルから読み取る抽出値を定義します。

次の例では、フィールドの同じ値について次のような依存関係があります。

たとえば、サブ関数 5 では、関数に基づいて意味が変更されます。

  - 読み取り関数の場合、5 はメモリの読み取りを意味します。

  - 書き込み関数の場合は、同じ値がファイルから読み取るために使用されます。

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>サンプル ファイル

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>マッピングを含む JSON サンプル

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>プラグインのパッケージ化、アップロード、監視 

このセクションでは、以下の方法について説明します。

  - プラグインをパッケージ化する。

  - プラグインをアップロードする。

  - プラグインを監視およびデバッグしてパフォーマンスの程度を評価する。

プラグインをパッケージ化するには:

1. **アーティファクト** (ライブラリ、config.json、またはメタデータ) を `tar.gz` ファイルに追加します。

1. ファイル拡張子を \<XXX.hdp> に変更します。ここで、\<XXX> はプラグインの名前です。

Horizon コンソールにサインインするには:

1.  管理者、CyberX、またはサポート ユーザーとしてとしてセンサーの CLI にサインインします。

2.  ファイル `/var/cyberx/properties/horizon.properties` で、**ui.enabled** プロパティを **true** (`horizon.properties:ui.enabled=true`) に変更します。

3.  センサー コンソールにサインインします。

4.  メイン メニューから **[Horizon]** オプションを選択します。

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="左側のペインで Horizon オプションを選択します。":::

    Horizon コンソールが開きます。

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Horizon コンソールとそのすべてのプラグインのビュー。":::

## <a name="plugins-pane"></a>プラグイン ペイン

プラグイン ペインには以下が一覧表示されます。

  - インフラストラクチャ プラグイン: Defender for IoT に既定でインストールされているインフラストラクチャ プラグイン。

  - アプリケーション プラグイン: Defender for IoT と、Defender for IoT または外部開発者によって開発されたその他のプラグインに既定でインストールされているアプリケーション プラグイン。
    
切り替えを使用して、アップロードされているプラグインを有効または無効にします。

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CIP トグル。":::

### <a name="uploading-a-plugin"></a>プラグインのアップロード

プラグインを作成してパッケージ化した後、それを Defender for IoT センサーにアップロードできます。 ネットワークを最大限に活用するには、組織内の各センサーにプラグインをアップロードする必要があります。

アップロードするには:

1.  センサーにサインインします。


2. **[アップロード]** を選択します。

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="プラグインをアップロードします。":::

3. プラグインを参照し、プラグインのダイアログ ボックスにドラッグします。 プレフィックスが `.hdp` であることを確認します。 プラグインが読み込まれます。

## <a name="plugin-status-overview"></a>プラグインの状態の概要 

Horizon コンソールの **[Overview]\(概要\)** ウィンドウには、アップロードしたプラグインに関する基本的な情報が表示され、それらを無効または有効にすることができます。

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Horizon コンソールの概要。":::

| フィールド | 説明 |
|--|--|
| Application | アップロードしたプラグインの名前。 |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="オンとオフのスイッチ。"::: | プラグインの **オン** と **オフ** を切り替えます。 プラグインをオフにすると、プラグインで定義されているプロトコル トラフィックは Defender for IoT によって処理されません。 |
| 時刻 | データが最後に分析された時刻。 5 秒ごとに更新されます。 |
| PPS | 1 秒間に送信されたパケットの数。 |
| 帯域幅 | 過去 5 秒以内に検出された平均帯域幅。 |
| 不正形式 | 不正形式の検証は、プロトコルが肯定的に検証された後に使用されます。 プロトコルに基づいてパケットを処理できなかった場合は、エラー応答が返されます。   <br><br>この列は、過去 5 秒間に発生した形式誤りエラーの数を示します。 詳細については、「[形式に誤りがあるコードの検証](#malformed-code-validations)」を参照してください。 |
| 警告 | パケットは構造と仕様に一致しますが、プラグインの警告の構成に基づく予期しない動作があります。 |
| エラー | 基本的なプロトコル検証に失敗したパケットの数。 パケットがプロトコル定義と一致することを検証します。 ここに表示される数値は、過去 5 秒間に検出されたエラーの数を示します。 詳細については、「[サニティ コードの検証](#sanity-code-validations)」を参照してください。 |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="モニター アイコン。"::: | プラグインで検出された不正形式と警告の詳細を確認します。 |

## <a name="plugin-details"></a>プラグインの詳細

プラグインに対して検出された "*不正な形式*" と "*警告*" の数を分析して、リアルタイムのプラグインの動作を監視することができます。 画面をフリーズして詳細な調査のためにエクスポートするためのオプションが使用可能です。

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="SNMP モニター画面。":::

監視を行うには:

[Overview]\(概要\) から、プラグインの [Monitor]\(モニター\) ボタンを選択します。

次のステップ

[Horizon API](references-horizon-api.md) を設定する
