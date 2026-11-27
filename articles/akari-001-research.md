---
title: "同じ「正の整数」の検査なのに、Pydantic と Zod で精度が違った ── 差は検査の前、JSON を読む所で生まれていた"
emoji: "📝"
type: "tech"
topics: ["python", "typescript", "pydantic", "zod", "ai"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=b3775a7e7b0d29419db1d8fd6f81f0edeef8bf707e999ca8ef25f439264a8619 -->

上原正吉（EarthLink Network Co., Ltd.）。AI会社・AI社員による業務自動化と、人の逐次介入なしに24時間自動開発を回す仕組みを自社で実践しています。開発ノウハウと具体的な設定方法、運用して分かった改善点を公開しています。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# 同じ「正の整数」の検査なのに、Pydantic と Zod で精度が違った ── 差は検査の前、JSON を読む所で生まれていた

![同じ「正の整数」の検査なのに、Pydantic と Zod で精度が違った ── 差は検査の前、JSON を読む所で生まれていた](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/akari-001/hero-ogp.png)

この記事は、連載「あかりちゃんが試してみた」の第 1 回です。私の会社の AI 社員のあかりが、ソフトやサービスを手元で動かして確かめた記録です。出典は、試したソフトの公式の文書と公開のリポジトリです。

**連載の目次**

1. **同じ「正の整数」の検査なのに、Pydantic と Zod で精度が違った（この記事）**
2. 音声 AI に「やめて」と言えば、裏の仕事も止まるのか（予定）
3. スキルの検査の点数は、悪意の証明ではなかった（予定）
4. EmDash のプラグインに許す通信先を、8 つの例で確かめた（予定）

Python の処理を TypeScript に移す時、「quantity は正の整数」と同じ決まりを書けば、同じ値を受け付けると考えがちです。AI 社員のあかりが、Python の Pydantic と TypeScript の Zod に同じ 11 件の JSON を渡したところ、2 件で受け付ける値が違いました。ただし、差を生んだのは Pydantic・Zod の検査そのものではなく、検査の前に JSON を読む所です。`9007199254740993` は、JavaScript の数が 2 の 53 乗を超えると整数を丸めるという精度の差、`900.0` は、小数と整数を区別して読むかどうかの差でした。

## 試したこと

注文の数量 `quantity` を 1 つだけ持つ JSON を、両方の言語で検査しました。決まりは次の 3 つです。

- `quantity` は正の整数
- 文字列の `"900"` や `true` のような、数ではない値は受け付けない
- `quantity` 以外の項目があれば受け付けない

Python 側は Pydantic の strict mode（値の種類を変換しない検査）と `extra="forbid"`（余分な項目を禁じる設定）で書きました。

```python
class Order(BaseModel):
    model_config = ConfigDict(strict=True, extra="forbid")
    quantity: int = Field(gt=0)
```

TypeScript 側は Zod の `z.strictObject`（知らない項目があれば失敗する object）と `z.int().positive()` で書きました。

```ts
const Order = z.strictObject({ quantity: z.int().positive() });
```

検査を通った値は、数量が 1,000 を超えれば「能力超過」、1,000 以下なら「受理」に分けました。1,000 はこの試験のために決めた値です。検査で落ちた値は「形式で拒否」です。数量が多すぎることと、書き方がおかしいことは、直す人も直し方も違うので分けました。

材料は 1 つの JSON のファイルです。同じファイルを、それぞれの言語の標準の JSON の読み込みで読みました。言語ごとに似た値を書き直すと、読み込みの違いが見えなくなるためです。11 件は次のとおりです。

- 普通の値: `900`・`1200`
- 数ではない値: `"900"`（文字列）・`true`・`null`
- 範囲の外の値: `0`
- 整数でない値: `1.5`
- 項目の違い: `memo` という余分な項目つき・`quantity` が無い
- 境目の値: `900.0`・`9007199254740993`

## 11 件のうち 9 件は同じ、2 件で答えが分かれた

両方を流して、答えを 1 行ずつ並べました。

![compare.mjs を original で流したターミナルの出力。11 件の case ごとに python と typescript の答えと match の列。900.0 は python が rejected・typescript が accepted、9007199254740993 は python が over_capacity・typescript が rejected で、この 2 行は DIFF（色つき）。最後の行は「9/11 same, 2 different」。右にあかりが出力を指さしている](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/akari-001/ja_01-eleven-cases.png)

| 入力 | Python（Pydantic） | TypeScript（Zod） |
| --- | --- | --- |
| `900` | 受理 | 受理 |
| `1200` | 能力超過 | 能力超過 |
| `"900"` | 形式で拒否 | 形式で拒否 |
| `true` | 形式で拒否 | 形式で拒否 |
| `0` | 形式で拒否 | 形式で拒否 |
| `1.5` | 形式で拒否 | 形式で拒否 |
| `memo` つき | 形式で拒否 | 形式で拒否 |
| `quantity` が無い | 形式で拒否 | 形式で拒否 |
| `null` | 形式で拒否 | 形式で拒否 |
| **`900.0`** | **形式で拒否** | **受理** |
| **`9007199254740993`** | **能力超過** | **形式で拒否** |

最初の 9 件は同じ答えでした。数ではない値も、余分な項目も、両方とも止めています。分かれたのは、境目を狙って入れた `900.0` と `9007199254740993` の 2 件です。この 11 件は境目を狙って選んだので、2 件という割合は、本番で届く入力で起きる割合ではありません。

## 分かれたのは、検査の前の JSON を読む所

2 件とも、検査の書き方ではなく、JSON を読んだ時点で値が違っていました。検査の前に、それぞれの言語が 2 つの値を何として読むかを出しました。

![read-check.py と read-check.mjs を流したターミナルの出力。Python は {"quantity": 900.0} を 900.0（float）、9007199254740993 を 9007199254740993（int）と読む（900.0 の行に色）。JavaScript は 900.0 を 900（number・安全な整数）と読み、9007199254740993 を 9007199254740992（number・Number.isSafeInteger=false）と読む（この行にも色）。右にあかりが口に手を当てて驚いている](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/akari-001/ja_02-read-before-validate.png)

`900.0` は、Python では小数（float）として読まれます。Pydantic の strict mode は、公式の文書の説明どおり、int の項目に int 以外の値を渡すと受け付けません。そのため形式で拒否になりました。JavaScript には整数と小数の区別が無く、`900.0` は `900` と同じ数になります。Zod から見れば、ただの整数の 900 です。

`9007199254740993` は、Python では桁を落とさずに整数として読まれ、検査を通って能力超過になりました。JavaScript の数は、9007199254740991（`Number.MAX_SAFE_INTEGER`）を超えると、すべての整数を正しく表せません。JSON を読んだ時点で `9007199254740992` に丸まりました。Zod の公式の文書は、`z.int()` を「安全な整数の範囲に限る」と説明しています。丸まった値はこの範囲の外なので、形式で拒否になりました。

どちらかのライブラリが間違っているわけではありません。どちらも文書に書いてあるとおりに動いています。「正の整数」という同じ言葉の中に、小数点のついた書き方を許すか、どこまで大きな数を許すか、という決めていない所が残っていました。

## 差をなくす直し方を 2 つ試した

決めていない所を決めれば、答えはそろうはずです。決め方は 2 つ考えられるので、両方を作って試しました。どちらにも、形式の上限として 1,000,000 を足しました。大きな整数で言語の差が出る前に、両方で止めるためです。流したのは元の 11 件と、直し方を確かめるために足した 3 件（`9e2`・`1000000`・`1000001`）です。

### 直し方 A: 数として整数なら受け付ける

`900.0` を `900` として扱います。直すのは Python 側だけです。Pydantic の BeforeValidator（Pydantic の検査の前に値を整える関数）で、小数点以下が 0 の小数を整数に直してから、strict mode の検査に渡します。

```python
def float_to_int_if_whole(v):
    if isinstance(v, float) and v.is_integer():
        return int(v)
    return v

quantity: Annotated[int, BeforeValidator(float_to_int_if_whole), Field(gt=0, le=1_000_000)]
```

![compare.mjs を fix-integer-value で流したターミナルの出力。元の 11 件は「11/11 same, 0 different」、足した 3 件は「3/3 same, 0 different」。900.0 と 9e2 は両方で accepted、9007199254740993 は両方で rejected（この行に色）。右にあかりが親指を立てている](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/akari-001/ja_03-fix-a.png)

14 件すべてで答えがそろいました。`900.0` と `9e2` は両方で受理です。`9007199254740993` は両方で形式で拒否になりました。上限の 1,000,000 を超えるためで、TypeScript 側は丸めた値が安全な整数の範囲の外でもあります。

### 直し方 B: 小数点の無い書き方だけ受け付ける

`900.0` を両方で拒否します。直すのは TypeScript 側だけです。JavaScript の `JSON.parse` に渡す reviver（読み込んだ値を 1 つずつ受け取る関数）は、数の元の書き方を `context.source` で受け取れます。元の書き方に小数点や指数（`9e2` の `e`）が入っていれば、数ではない値に置き換え、Zod の検査で落とします。

```ts
JSON.parse(text, function (key, value, context?: { source?: string }) {
  if (key === 'quantity' && typeof value === 'number' && !/^-?\d+$/.test(context?.source ?? '')) {
    return `not-digits:${context?.source}`;
  }
  return value;
});
```

![compare.mjs を fix-digits-only で流したターミナルの出力。元の 11 件は「11/11 same, 0 different」、足した 3 件は「3/3 same, 0 different」。900.0 と 9e2 は両方で rejected、9007199254740993 も両方で rejected（この行に色）。右にあかりがあごに手を当てて考えている](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/akari-001/ja_04-fix-b.png)

こちらも 14 件すべてで答えがそろいました。直し方 A と違うのは、`900.0` と `9e2` が両方で形式で拒否になる所だけです。

## 人が決めること

あかりが試して分かったのは、どちらの直し方でも答えはそろう、ということまでです。どちらを選ぶかは、業務の決まりなので人が決めます。

- `900.0` のような書き方を送ってくる相手がいるか。いるなら直し方 A、送らせないなら直し方 B
- 数量の上限をいくつにするか。今回の 1,000,000 はこの試験のために決めた値で、業務の上限ではありません
- 桁の多い番号を数として送っていないか。注文の番号や ID のように大きな整数を送るなら、数ではなく文字列で送る決まりにします

AI にコードを別の言語へ移させる時は、移したコードと一緒に、同じ JSON を古い側と新しい側に渡した結果の表を出させると、人が見る所が表の DIFF の行に絞れます。

## 試した環境と費用

- 試したのは、AI 社員のあかり
- Python 3.14.2 と Pydantic 2.14.4。TypeScript は Node.js 24.22.2 と Zod 4.6.7 で、Node.js が .mts のファイルをそのまま動かす機能で実行した
- 動かした所: 手元の Mac。ライブラリは作業用の場所に入れ、ほかには入れていない
- 出典: Pydantic と Zod の公式の文書（[Pydantic strict mode](https://pydantic.dev/docs/validation/latest/concepts/strict_mode/)・[Pydantic の変換の表](https://pydantic.dev/docs/validation/latest/concepts/conversion_table/)・[Pydantic の validators](https://pydantic.dev/docs/validation/latest/concepts/validators/)・[Zod の API](https://zod.dev/api)）と、JSON.parse の説明（[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)）
- ライセンス: Pydantic も Zod も MIT ライセンスで、会社の仕事に使える
- 費用: 0 円。有料の API も、新しい契約も使っていない

## 用語

- **Pydantic**: Python で、受け取ったデータが決めた形に合うかを調べるライブラリ
- **Zod**: TypeScript で、受け取ったデータが決めた形に合うかを調べるライブラリ
- **strict mode**: Pydantic の、値の種類を変換しない検査。文字列の `"900"` を数に直したりしない
- **安全な整数**: JavaScript の数で、正しく表せることが保証された整数。-9007199254740991 から 9007199254740991 まで
- **reviver**: `JSON.parse` に渡す関数。読み込んだ値を 1 つずつ受け取り、置き換えられる
- **BeforeValidator**: Pydantic で、検査の前に値を整える関数を足す書き方

---

この連載では、AI 社員のあかりが、ソフトやサービスを実際に動かし、そのまま使える所と、人が決める所を分けて書いていきます。
興味のある方は、ぜひ「いいね」と記事の購読をお願いいたします。

自社プロダクトは https://www.eln.ne.jp/products にまとめています。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. 代表。AI社員に日々の業務を任せ、開発では人が逐一指示しなくても作業が進むHOOTL（Human Out Of The Loop）の仕組みを自社で実践しています。このブログでは、業務自動化と24時間自動開発のノウハウ、具体的な設定方法、日々の運用で見つかった問題と改善を共有します。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。
