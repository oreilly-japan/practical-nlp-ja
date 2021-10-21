# Scrapyを用いた本のスクレイピング

Scrapyは、Webサイトのスクレイピングやクローリングを行うためのPythonフレームワークです。このチュートリアルでは、Scrapyを使ってデモサイト[books.toscrape.com](http://books.toscrape.com/)から大量のデータを素早くスクレイピングする方法を紹介します。

## インストール

```bash
pip install Scrapy
```

さらなる詳細は[公式ドキュメント](https://docs.scrapy.org/en/latest/intro/install.html)を参照してください。

## チュートリアル

### セットアップ

まず、新しいScrapyプロジェクトを作成します。

```bash
scrapy startproject tutorial
```

Scrapyはテンプレートとなるファイルを自動で生成します。ディレクトリ構造は以下のようになるはずです。

```
.
└── tutorial
    ├── scrapy.cfg
    └── tutorial
        ├── __init__.py
        ├── items.py
        ├── middlewares.py
        ├── pipelines.py
        ├── settings.py
        └── spiders
            └── __init__.py
```

では、`cd tutorial`を実行して、プロジェクトのベースとなるディレクトリに移動しましょう。

### スパイダー

`tutorial/spiders`の中に新しいスパイダーを作ります。スパイダーとは、`scrapy.Spider`クラスを継承したPythonのクラスのことです。各スパイダーは、実行時に使用される一意の`name`と、スパイダーの実行時にクローリングの起点となるURLのリスト`start_urls`を持ちます。ここでは、`start_urls`をスクレイピングしたいWebサイトである`["http://books.toscrape.com/"]`に設定します。新しいファイルを`tutorial/spiders/books_spider.py`に作成します。このファイルには、次のような内容を記述します。

```python
import scrapy

class BookSpider(scrapy.Spider):
    name = "books"

    start_urls = ["http://books.toscrape.com/"]
```

スパイダーが実行されると、すべての`start_urls`に対して`parse`関数を実行します。`parse`関数は`response`というパラメータを持ち、取得したページに関するすべての情報を格納します。`response.url`には、現在取得しているページのURLが格納されます。fetch関数でURLを表示して、その動作を確認してみましょう。以下のコードは、先に定義したBookSpiderの中にあるはずです。

```python
    def parse(self, response):
        print(response.url)
```

それでは、スパイダーを実行してみましょう。ベースプロジェクトのディレクトリ（`tutorial`）に移動して、実行してください。なお、`books`は、BookSpiderクラスで指定したスパイダーの名前です。

```bash
scrapy crawl books
```

大量のテキストが出力されるので、その中から`http://books.toscrape.com/`を探してみてください。

### CSSとXPathのセレクタ

では、実際にWebサイトからコンテンツを抽出する作業に移りましょう。CSSを使ったことがある方なら、特定の要素にスタイルを適用するためのセレクタをご存知でしょう。同じタイプのセレクタを使って、特定の要素からデータを抽出できます。これは、`response.css(".css selector here")`を用いて得られたCSSオブジェクトの`getall()`や`get()`関数を使って、実際に要素を取得することで実現できます。セレクタの中で`::text`を使えば、必要な要素に含まれるテキストを取得できます。要素の特定のプロパティ、たとえば`href`を取得するには、`response.css`が返すオブジェクトに対して`attrib["href"]`を使用します。

より強力なセレクタとしてXPathがあります。XPathについては、[こちら](https://devhints.io/xpath)にチートシートがあり、[こちら](https://www.guru99.com/xpath-selenium.html)にはより詳しいチュートリアルがあります。このチュートリアルで使用されるXPathセレクタの概要は次のとおりです。

- `//element`:DOM上の任意の場所にある、タイプが`element`のすべての要素を取得する。
- `element[@class='classname']]`:タイプが`element`でクラスが`classname`の要素のみを取得する。クラスは完全にマッチする必要があることに注意すること。たとえば、ある要素に`class1 class2`というクラスが付いていても、`@class='class1'`ではマッチしない。
- `element[contains(@class, 'classname')]`: `class`として`classname`を含む`element`タイプの要素を取得する。
- `element/text()`: 要素に含まれるテキストを取得する。
- `element/@href`: `href`属性を取得する（任意の属性に置き換え可能）。
- `element1/element2`: `element1`の直接の子である`element2`を取得する。

### データの抽出

それでは、スクレイピングしたいWebサイトを見てみましょう。サイドバーには、すべてのカテゴリのリストが表示され、右にはすべての本が表示されています。

このチュートリアルでは、それぞれのカテゴリを訪問し、その中のすべての本をスクレイピングするというアプローチをとっています。

セレクタをリアルタイムでテスト、実験するのに最適なツールは **Scrapyシェル** です。次のコマンドを使って、興味のあるWebサイトのscrapyシェルを起動します。

```bash
scrapy shell "http://books.toscrape.com/"
```
（Tip: Scrapyシェルの開発体験を向上させるには、IPythonをインストールします）

シェルからは、`view(response)`を使って得られた正確なレスポンスを見ることができます。スクレイピングしたいWebサイトでは、先に進む前に必ずこの操作をしてください。ScrapyにはデフォルトではJavaScriptが搭載されていないため、お使いのブラウザやScrapyによって、Webサイトの見え方は変わる場合があります。

では、カテゴリのリストを選択する方法を見てみましょう。ここでは、便利な*Inspect Element*ツールを使います。ソースを見ると、カテゴリのリストは`side_categories`クラスの`div`に入っていることがわかります。このdivには、順序を持たないリストを含み、そのリストの唯一の要素も別の順序を持たないリストです。2番目の`ul`が目的の要素です。というのも、各要素に各カテゴリのURLを記述したアンカータグが含まれているからです。したがって、XPathは次のようになります（必要な出力が得られるまで、scrapyシェルでXPathを試してみてください）。

```python
cat_names = response.xpath("//div[@class='side_categories']/ul/li/ul/li/a/text()").getall()
cat_urls = response.xpath("//div[@class='side_categories']/ul/li/ul/li/a/@href").getall()
```

<!-- `cat_names` stores the list of all categories and `cat_urls` the corresponding URLs. We can iterate over these using zip. There is one issue though, the URLs are relative to the current URL; to fix this, we use `response.urljoin` to get the absolute URL. Now that we have the URLs, we need a way to scrape them separately, the `parse` function is specifically made to get the list of categories; hence, we need a separate function which will parse all books in a category - we call this function `parse_category`. To tell scrapy to parse a particular URL, we need to create an object of `scrapy.Request` and return it. Since we have a list of URLs to return, we'll use `yield` instead of return to return multiple Requests to scrapy. A `scrapy.Request` object required two parameters - the URL and the function to pass the handle to after a response is received, called `callback`. We use another parameter `cb_kwargs` to pass additional parameters to the callback function instead of just the response. Here is the `parse` function after adding all the above mentioned features. -->

`cat_names`にはすべてのカテゴリのリストが、`cat_urls`には対応するURLが格納されています。これらをZIPを使って反復処理できます。問題は、URLが相対URLであることです。この問題を解決するために、`response.urljoin`を使用して絶対URLを取得します。必要なURLが得られたので、それらを別々にスクレイピングする方法が必要です。`parse`関数は特にカテゴリのリストを取得するために作られているので、カテゴリ内のすべての本を解析する別の関数が必要です。この関数を`parse_category`と名付けましょう。Scrapyに特定のURLを解析するように指示するには、`scrapy.Request`のオブジェクトを作り、それを返す必要があります。返すべきURLのリストがあるので、複数のRequestをScrapyに返すために、returnの代わりに`yield`を使います。`scrapy.Request`オブジェクトには、URLと、レスポンスを受け取った後の処理をするための関数（`callback`）をパラメータとして渡す必要がありました。ここでは、レスポンスだけではなく、コールバック関数に追加のパラメータを渡すために、別のパラメータ`cb_kwargs`を使用しています。以下は、上記の機能をすべて追加した後の`parse`関数です。

```python
def parse(self, response):
    num_cats_to_parse = 5
    cat_names = response.xpath("//div[@class='side_categories']/ul/li/ul/li/a/text()").getall()
    cat_urls = response.xpath("//div[@class='side_categories']/ul/li/ul/li/a/@href").getall()
    for _, name, url in zip(range(num_cats_to_parse), cat_names, cat_urls):
        name = name.strip()
        url = response.urljoin(url)
        yield scrapy.Request(
            url,
            callback=self.parse_category,
            cb_kwargs=dict(cat_name=name)
        )
```

カテゴリ数がかなり多いので、クロールするカテゴリ数を制限しています。

それでは、カテゴリに含まれるすべての本をスクレイピングしてみましょう。これは`parse_category`関数の中で行われます。カテゴリのURLを1つ選んでScrapyシェルを使うと、セレクタをインタラクティブに構築できます。ここでは、各本がclassとして`product_pod`を持つ`article`の中にあり、本のURLにリンクするアンカータグを含む`h3`を持つことがわかります。したがって、すべての本を取得する行は次のようになります。

```python
book_urls = response.xpath("//article[@class='product_pod']/h3/a/@href").getall()
```

本のURLをループさせて、各URLに対して`scrapy.Request`を生成し、コールバックに後ほど定義する新しい関数`parse_book`を渡します。いくつかのカテゴリでは、1つのページに表示するには本の数が多すぎるため、ページ分割されており、ページの下部には`next`ボタンがあることに気がついたかもしれません。本に対するすべてのリクエストを`yield`した後、`next`ボタンを見つけ、そのURLを取得します。これは別のリクエストを`yield`するために使用され、コールバックは`parse_category`となります。この関数のコード全体は次のとおりです。

```python
def parse_category(self, response, cat_name):
    book_urls = response.xpath("//article[@class='product_pod']/h3/a/@href").getall()

    for book_url in book_urls:
        book_url = response.urljoin(book_url)
        yield scrapy.Request(
            book_url,
            callback=self.parse_book,
            cb_kwargs=dict(cat_name=cat_name)
        )

    next_button = response.css(".next a")
    if next_button:
        next_url = next_button.attrib["href"]
        next_url = response.urljoin(next_url)
        yield scrapy.Request(
            next_url,
            callback=self.parse_category,
            cb_kwargs=dict(cat_name=cat_name)
        )
```

最後に、各本をスクレイピングするための`parse_book`関数を書きましょう。このチュートリアルでは、タイトル、価格、在庫情報、星評価のみをスクレイピングします。Scrapyシェルを使って、そのためのセレクタを作ることができます。タイトルと価格の取得は簡単です。`instock`セレクタは、主にスペースと改行で構成される文字列のリストを与え、その中に実際の情報が含まれているので、`strip` と `join` を使って必要な情報を取得します。星の評価を取得するのは、その情報がクラス名にしか含まれていないので厄介です。そこで、XPathを使ってクラス名を取得し、その中の2番目の単語を選択します。最後に、必要な情報を辞書で返します。その理由は、Scrapyがこれらの辞書を`items`とみなしているからです。これにより、スパイダーの実行中に`-o output.json`オプションを用いて、データを直接JSONにエクスポートできます。`items`を返す主な理由は、Scrapyのパイプラインを使ってプログラム的にデータを処理するためです。たとえば、データを自動的にデータベースに挿入したり、ファイルに書き込んだりするパイプラインを書くことができます。

### パイプラインを用いたデータの保存

スパイダーがアイテムを返したら、アイテムパイプラインに送られ、すべてのパイプラインオブジェクトによって順次処理されます。パイプラインの各オブジェクトは単なるPythonクラスで、`process_item`、`open_spider`（任意）、`close_spider`（任意）などのいくつかの特別な関数を実装しています。

それでは、データをCSVファイルに保存するパイプラインを作ってみましょう。ここでは、Python組み込みのcsvモジュール、特にその中の`DictWriter`オブジェクトを使用します。詳細は[ドキュメント](https://docs.python.org/3/library/csv.html#csv.DictWriter)を参照してください。

`tutorial/pipelines.py`を開き、既存のサンプルパイプラインを削除し、次のように書き換えます。

```python
import csv

class BookCsvPipeline():
    def open_spider(self, spider):
        self.file = open("output.csv", "at")
        fieldnames = [
            "title",
            "price",
            "stock",
            "rating",
            "category"
        ]
        self.writer = csv.DictWriter(self.file, fieldnames=fieldnames)
        if self.file.tell() == 0:
            self.writer.writeheader()

    def close_spider(self, spider):
        self.file.close()

    def process_item(self, item, spider):
        self.writer.writerow(item)
        return item
```

パイプラインの`process_item`は、パイプラインの次のプロセッサが処理できるように、処理した`item`を返す必要があることに注意してください。

スパイダーを実行する前に、先ほど作成したパイプラインを有効にする必要があります。`tutorial/settings.py`を開き、`ITEM_PIPELINES`を探し、コメントを外して次のように書き換えます。

```python
ITEM_PIPELINES = {
    'tutorial.pipelines.BookCsvPipeline': 300,
}
```

`300`はパイプラインの中での優先順位を示しています。`0`が最も優先順位が高く、`1000`が最も低いことを示しています。

これでスパイダーを実行する準備ができました。

```
scrapy crawl books -o output.json
```

これには数秒かかり、大量の出力をします。実行が完了すると、実行したのと同じフォルダに`output.json`と`output.csv`があります。CSVは、`num_cats_to_parse`が5に設定されていた場合、163行が含まれているはずです（ヘッダを除く）。

これで、クローラは完成です。Scrapyが提供する便利な機能をたくさん使いました。
