---
layout: post
title: "16 ファイル処理"
date:   2017-04-24 18:00:00 +0900

---

## データ永続化

今までは、プログラムの中で扱ったデータを単に変数に格納していたので、プログラムを終了するとデータはすべて失われてしまいます。プログラムを終了してもデータが失われないようにすることを、「データの永続化」といいます。

今回は、データの永続化のひとつの方法として、ファイルの処理を学んでみます。

## ファイル処理の概要

C言語におけるファイル処理は、以下の手順で行います。

1. ファイルポインタの宣言
2. ファイルのオープンとエラー処理
3. ファイルの読み書き
4. ファイルのクローズ

## ファイル処理のサンプルプログラム

ファイルを読み込むプログラムの例です。ファイル名は、 read_file.c とします。  
プログラムを実行する前に、[data.txt]({{site.baseurl}}/files/data.txt)を read_file.c と同じディレクトリに配置してください。（Windowsなら【右クリック】→【名前をつけて保存】）

{% highlight c linenos %}
#include <stdio.h>

int main(void)
{
        // 1: ファイルポインタの宣言
        FILE *file; 
        int a, b;

        // 2. ファイルのオープン
        if ((file = fopen("data.txt", "r")) == NULL) {
                // 2. エラー処理
                printf("ファイルを開けません。\n");     
        } else {
                // 3. ファイルの読み込み
                while(fscanf(file, "%d, %d", &a, &b) != EOF) {
                        printf("%d + %d = %d\n", a, b, a + b);
                }
                // 4. ファイルのクローズ
                fclose(file);
        }

        return 0;
}
{% endhighlight %}

続いて、ファイルに書き込むプログラムの例です。ファイル名は、 write_file.c とします。  
プログラム実行後、同じディレクトリに output.txt というファイルがあるはずなので、中身を確認してみてください。（ cat output.txt でファイルの中身を確認できます。catコマンドを参照。）

{% highlight c linenos %}
#include <stdio.h>

int main(void)
{
        // 1. ファイルポインタの宣言
        FILE *file;

        int input;

        // 2. ファイルのオープン
        if ((file = fopen("output.txt", "a")) == NULL) {
                // 2. エラー処理
                printf("ファイルが開けませんでした。\n");
        } else {
                printf("ファイルに書き込む数字を入力\n--> ");
                scanf("%d", &input);

                // 3. ファイルの書き込み
                fprintf(file, "%d\n", input);

                // 4. ファイルのクローズ
                fclose(file);
        }
}
{% endhighlight %}


## ファイル処理基礎知識

### FILE型

C言語では、ファイルポインタを通してファイルを扱います。詳しくは触れませんが、以下のようにしてファイルポインタを宣言します。

```
FILE *file;
```

### fopen() - ファイルのオープン

fopen() 関数を使ってファイルをオープンします。fopen()関数は以下のように使います。

```
file = fopen(ファイル名, モード);
```

第1引数にはファイル名を、第2引数にはモードを指定します。

モードは、以下のようなものがあります。用途によって使い分けましょう。

| モード | 動作             | ファイルがあるとき       | ファイルがない時 |
|--------|------------------|--------------------------|------------------|
| "r"    | 読み込み専用     | 正常                     | NULLを返す       |
| "w"    | 書き込み専用     | 上書きする               | 新規作成する     |
| "a"    | 追記専用         | ファイルの最後に追加する | 新規作成する     |
| "r+"   | 読み書き         | 正常                     | NULLを返す       |
| "w+"   | 読み書き         | 上書きする               | 新規作成する     |
| "a+"   | 読み書き（追記） | ファイルの最後に追加する | 新規作成する     |

また、"r"、"r+"モードの時は、以下のようにしてファイルがなかった時用のエラー処理を行うようにしましょう。

```
if ((file = fopen("data.txt", "r")) == NULL) {
		printf("ファイルを開けません。\n");
} else {
		// 処理
}
```

### fscanf - ファイルの読み込み

fscanf()関数を使ってファイルの内容を読み込みます。読み込みは、1行ずつ行われます。第1引数にファイルポインタを置く以外は、通常のscanf()関数と同じ使い方です。

なお、fscanf()関数がファイルの終わりを読み込んだときは、EOF(End Of Fileの略)を返すので、それをループの終了条件にすることが多いです。

```
fscanf(ファイルポインタ, フォーマット, 変数, [変数2, 変数3, ...])
```

例えば、以下のようなカンマ区切りで整数が2つ並んでいるテキストファイルが有るときは、このようにfscanf()関数を使います。

```
1, 1
2, 3
5, 8
13, 21
```

```
int a, b;
while(fscanf(file, "%d, %d", &a, &b) != EOF) {
	// 処理
}
```


### fprintf - ファイルへの書き込み

fprintf()関数を使ってファイルへデータを書き込みます。第1引数にファイルポインタを置く以外は、通常のprintf()と同じ使い方です。

```
fprintf(ファイルポインタ, フォーマット, 変数, [変数2, 変数3, ...])
```

```
// サンプル
int age = 21;
fprintf(file, "I am %d years old.\n", age);
// "I am 21 years old." とファイルに書き込まれる。
```

### ファイルのクローズ

fopen()関数でオープンしたファイルは、処理の終了後に必ず閉じる必要があります。fclose()関数にファイルポインタを渡すことによってファイルを閉じることができます。

```
fclose(ファイルポインタ)
```


```
// サンプル
FILE *file;
file = fopen("hello.txt", "r");

/*
 *	何らかの処理
 */

fclose(file); // プログラム終了前に必ずファイルを閉じること！

return 0;
```

