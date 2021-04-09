---
title: jar の依存関係を安全に管理する - Azure HDInsight
description: この記事では、HDInsight アプリケーションの Java アーカイブ (JAR) 依存関係を管理するためのベスト プラクティスについて説明します。
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 9868e32665c89bbe9aadc06f1c2834704e6534e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942393"
---
# <a name="safely-manage-jar-dependencies"></a>jar の依存関係を安全に管理する

HDInsight クラスターにインストールされているコンポーネントには、サードパーティ製ライブラリへの依存関係があります。 通常、これらの組み込みコンポーネントによって、特定のバージョンの共通モジュール (Guava など) が参照されます。 その依存関係があるアプリケーションを送信すると、同じモジュールの異なるバージョンとの間で競合が発生する可能性があります。 最初にクラスパスでコンポーネントのバージョンを参照した場合、バージョンの非互換性により、組み込みコンポーネントによって例外がスローされる可能性があります。 しかし、最初に組み込みコンポーネントによってクラスパスに依存関係が挿入された場合は、アプリケーションから `NoSuchMethod` のようなエラーがスローされる可能性があります。

バージョンの競合を回避するには、アプリケーションの依存関係のシェーディングを検討してください。

## <a name="what-does-package-shading-mean"></a>パッケージのシェーディングとは
シェーディングとは、依存関係を含めたり、その名前を変更したりする方法を指します。 クラスを再配置し、影響を受けるバイトコードとリソースを書き換えて、ご自分の依存関係のプライベート コピーを作成します。

## <a name="how-to-shade-a-package"></a>パッケージをシェーディングする方法

### <a name="use-uber-jar"></a>uber-jar を使用する
uber-jar は、アプリケーション jar とその依存関係の両方を含む単一の jar ファイルです。 uber-jar の依存関係は、既定ではシェーディングされていません。 他のコンポーネントまたはアプリケーションによって別のバージョンのライブラリが参照されると、場合によってはバージョンの競合が発生することがあります。 これを回避するには、一部 (またはすべて) の依存関係がシェーディングされた uber-jar ファイルをビルドします。

### <a name="shade-package-using-maven"></a>Maven を使用してパッケージをシェーディングする
Maven では、Java と Scala の両方で記述されたアプリケーションをビルドできます。 maven-shade-plugin を使用すると、シェーディングされた uber-jar を簡単に作成できます。

以下の例で示すのは、maven-shade-plugin を使用してパッケージをシェーディングするよう更新されたファイル `pom.xml` です。  XML セクション `<relocation>…</relocation>` では、対応する JAR ファイルのエントリを移動させ、影響を受けるバイトコードを書き換えることによって、パッケージ `com.google.guava` からパッケージ `com.google.shaded.guava` にクラスを移動させます。

`pom.xml` の変更後、`mvn package` を実行して、シェーディングされた uber-jar をビルドできます。

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>SBT を使用してパッケージをシェーディングする
SBT も、Scala および Java 向けのビルド ツールです。 SBT には、maven-shade-plugin のようなシェーディング用プラグインはありません。 `build.sbt` ファイルを変更することでパッケージをシェーディングできます。 

たとえば、`com.google.guava` をシェーディングするには、`build.sbt` ファイルに次のコマンドを追加します。

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

次に、`sbt clean` と `sbt assembly` を実行して、シェーディングされた jar ファイルをビルドできます。 

## <a name="next-steps"></a>次の手順

* [HDInsight IntelliJ ツールを使用する](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [IntelliJ で Spark 用の Scala Maven アプリケーションを作成する](./apache-spark-create-standalone-application.md)