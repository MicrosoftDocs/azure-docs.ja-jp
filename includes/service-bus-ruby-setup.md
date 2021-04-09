---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 5c2959a1bf6225c164f8538c3c437e464d834b96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028280"
---
## <a name="create-a-ruby-application"></a>Ruby アプリケーションの作成
手順については、[Microsoft Azure での Ruby アプリケーションの作成](/previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app) に関するページを参照してください。

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus を使用するようにアプリケーションを構成する
Service Bus を使用するには、Azure Ruby パッケージをダウンロードして使用します。このパッケージには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems を使用してパッケージを取得する
1. **PowerShell** (Windows)、**ターミナル** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使用します。
2. コマンド ウィンドウに「gem install azure」と入力して、gem と依存関係をインストールします。

### <a name="import-the-package"></a>パッケージをインポートする
任意のテキスト エディターを使用して、ストレージを使用するアプリケーションの Ruby ファイルの先頭に次のコードを追加します。

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Service Bus 接続の設定
名前空間の値、キーの名前、キー、署名者、ホストを設定するには、次のコードを使用します。

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

作成した値には、URL 全体ではなく、名前空間値を設定してください。 たとえば、 **"yourexamplenamespace.servicebus.windows.net"** ではなく、"yourexamplenamespace" を使用します。

複数の名前空間を使用する場合、`SharedAccessSigner` オブジェクトを作成するときに、キーとその名前をコンストラクターに渡すことができます。

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```