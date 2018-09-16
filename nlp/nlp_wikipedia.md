# Wikipediaダウンロード

- Wikipediaは定期的に全データを書き出している
- [Wikipedia:データベースダウンロード - Wikipedia](https://ja.wikipedia.org/wiki/Wikipedia:%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E3%83%80%E3%82%A6%E3%83%B3%E3%83%AD%E3%83%BC%E3%83%89 "Wikipedia:データベースダウンロード - Wikipedia")

## (A) jawiki-latest-pages-articles.xml.bz2

- 全ページの記事本文を含むXML
- 2017年9月で2.5GBほど（解凍すると10GBぐらい）

###  wikiextractor

- Wikipediaのdumpデータのパース
- [attardi/wikiextractor: A tool for extracting plain text from Wikipedia dumps](https://github.com/attardi/wikiextractor "attardi/wikiextractor: A tool for extracting plain text from Wikipedia dumps")
- [【Python】日本語Wikipediaのダンプデータから本文を抽出する - プログラムは、用いる言葉の選択で決まる](http://taka-say.hateblo.jp/entry/2016/05/20/221817 "【Python】日本語Wikipediaのダンプデータから本文を抽出する - プログラムは、用いる言葉の選択で決まる")
- 標準では ```<doc>``` が着く

```
<doc>
タイトル

本文本文本文本文本文本文
</doc>
<doc>...</doc>
```
- 学習用には ```WikiExtractor.py``` の566行や569行あたりのヘッダーやフッターを修正して，```install``` する

```
header=""
fotter="\n"
```

- 638行あたりの ```title_str``` をコメントアウトすると本文のみ

```
text = [title_str] + text
```


- コマンド例

```
# インストール
$ python setup.py install 

# パース
$ WikiExtractor.py -b 500K -o extracted jawiki-latest-pages-articles.xml.bz2

# 結合
$ find extracted -name 'wiki*' -exec cat {} \; > text.xml

# 結合かつMecabでワカチ
$ find extracted -name 'wiki*' -exec mecab -b 1000000 -d /opt/local/lib/mecab/dic/mecab-ipadic-neologd -O wakati {} > wiki_mecabed.txt \;
```


- [doc2vecでWikipediaを学習する - TadaoYamaokaの日記](http://tadaoyamaoka.hatenablog.com/entry/2017/04/29/122128 "doc2vecでWikipediaを学習する - TadaoYamaokaの日記")

## (B) jawiki-latest-abstract.xml

- 全ページの要約（タイトル、ディスクリプション、構成要素）
- このデータではあまり精度良くベクトル化できない

### パース

- [Windows(Bash on Windows)でfastTextを使う - TadaoYamaokaの日記](http://tadaoyamaoka.hatenablog.com/entry/2017/04/19/000832 "Windows(Bash on Windows)でfastTextを使う - TadaoYamaokaの日記")

---

# 句読点などの削除する

- 必要に応じて，句読点などを削除する

```
# -*- coding: utf-8 -*-   

import os
import re
from collections import defaultdict
import codecs

def preprocess_text(input, output=None):

    if os.path.exists(input) == False:
        print("export_low_frequency_word/input({}) is None".format(input))
        return None

    output_path = output
    if output is None:
        temp_path = os.path.dirname(input)
        temp_name, temp_ext = os.path.splitext(os.path.basename(input))
        output_path = os.path.join(temp_path, temp_name+"_preprocessed"+temp_ext)

    if os.path.exists(output_path) == True:
        os.remove(output_path)
    else:
        temp_path = os.path.dirname(output_path)
        os.makedirs(temp_path, exist_ok=True)

    print("replace_punctuations/input:{}, output:{}".format(input, output_path))

    f = open(output_path, "w", encoding="utf-8")
    with codecs.open(input, 'r', 'utf8', 'ignore') as file:
        for line in file:
            dst = re.sub(r'([，．、。（）「」『』【】《》〈〉〔〕‥？！・＞＜／＼”“’‘＝→←↓↑＋＃…：；［］｛｝〜＾｜＿\[\]<>:;~(){}/_"\'\\*+.,?^$\-|])', " ", line.strip())
            words = re.split(" +", dst.strip())
            if len(words) == 0 or (len(words) == 1 and (words[0] == '\n' or words[0] == '')):
                continue
            else:
                dst2 = ' '.join(words) + '\n'
                f.write(dst2)
    f.close()

    return output_path
def main():
    input = "wiki20170912_mecab.txt"
    output = preprocess_text(input=input)
    print("output:", output)

if __name__ == "__main__":
    main()
```



# 参考

- [ダウンロード版ウィキペディア](http://dicwizard.jp/logophile_ug/format/WikiDump.html "ダウンロード版ウィキペディア")
- [【Python】日本語Wikipediaのダンプデータから本文を抽出する - プログラムは、用いる言葉の選択で決まる](http://taka-say.hateblo.jp/entry/2016/05/20/221817 "【Python】日本語Wikipediaのダンプデータから本文を抽出する - プログラムは、用いる言葉の選択で決まる")
- [attardi/wikiextractor: A tool for extracting plain text from Wikipedia dumps](https://github.com/attardi/wikiextractor "attardi/wikiextractor: A tool for extracting plain text from Wikipedia dumps")



