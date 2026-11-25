---
title: "Ubuntu で入れた PHP は、php -v の番号が古くても直っていることがある ── 直ったかは OS の提供元の告知で確かめる"
emoji: "📝"
type: "tech"
topics: ["セキュリティ", "脆弱性", "php", "ubuntu", "debian"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=3cd38ae490f8db7ae87fd9166f5513c445b9e9b76b39fd276f75c7b56e595e3f -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# Ubuntu で入れた PHP は、php -v の番号が古くても直っていることがある ── 直ったかは OS の提供元の告知で確かめる

![Ubuntu で入れた PHP は、php -v の番号が古くても直っていることがある ── 直ったかは OS の提供元の告知で確かめる](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/sec-017/hero-ogp.png)

## 結論

- Ubuntu の OS のパッケージで入れた PHP は、`php -v` の番号が PHP の修正版より古くても、もう直っていることがあります。Ubuntu 24.04 の PHP は `php -v` が 8.3.6 のままで、今回の重大な弱点が直っていました（PHP の修正版は 8.3.36）。
- 直ったかは、PHP の番号ではなくパッケージの番号を、OS の提供元の告知にある「直った番号」と照らして確かめます。
- 逆に、パッケージの番号の後ろに OS の名前が付いているだけでは、直ったとは言えません。番号の付け方は OS ごとに違い、Debian 12 の直った番号は PHP の修正版と同じ番号でした。

## 前提

連載「自分のサービスは自分で守る」の回です。Ubuntu や Debian のサーバーに apt で PHP を入れて Web サイトを動かしている人に向けて書きます。

## PHP の修正版は 8.3.36。Ubuntu 24.04 の PHP は 8.3.6 のまま

2026 年 10 月、PHP の公式サイトが緊急の告知を出しました。ログインしていない人が、サーバーの上で好きな命令を実行できてしまう弱点が 2 件（CVE-2026-109618・CVE-2026-109619）。直ったのは 8.5.12・8.4.27・8.3.36・8.2.35 で、Web のリクエストを処理しているすべての PHP が対象です。

これを読んだら、手元で `php -v` を打つと思います。Ubuntu 24.04 で OS のパッケージから入れた PHP なら、こう出ます。

![Ubuntu 24.04 のコンテナに OS のパッケージで PHP を入れた直後の実出力。php -v は「PHP 8.3.6 (cli)」。dpkg-query -W php8.3-cli は「php8.3-cli 8.3.6-0ubuntu0.24.04.14」。パッケージの変更履歴（changelog.Debian.gz）を CVE の番号で探すと「CVE-2026-109618」と「CVE-2026-109619」の 2 行が出る。この 4 か所が黄色](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/sec-017/17-2-ubuntu-php-v.png)

8.3.6 です。修正版の 8.3.36 より 30 も小さい番号です。番号だけを見比べると、「直っていない。PHP を上げなければ」となります。

でも、このサーバーは直っています。2 つ目の命令で出したパッケージの番号は `8.3.6-0ubuntu0.24.04.14` です。3 つ目の命令でこのパッケージの変更履歴を探すと、今回の 2 件の弱点の番号が載っています。Web のリクエストを処理する PHP-FPM（`php8.3-fpm`）を入れて確かめても、同じ番号でした。

## Ubuntu は、番号を上げずに修正だけを取り込む

Ubuntu は、OS を出した時の PHP の番号を、その OS の間は変えません。新しい番号に上げると、その上で動いているアプリが動かなくなるおそれがあるからです。代わりに、PHP の開発チームが出した修正だけを、今の番号の PHP に取り込みます。これをバックポートと呼びます。

PHP の公式の告知にも、このことが書いてあります。

![PHP の公式サイト（php.net）の告知。題は「Security advisory: Critical remote code execution vulnerabilities, update now」。PHP 8.5.12・8.4.27・8.3.36・8.2.35 が、ログインなしでの遠隔のコード実行につながる重大な弱点 2 件を直すと書いてある。黄色の 2 行は「Linux の配布元と長期サポートの提供元は事前に修正を受け取っているので、更新したパッケージを提供元に確かめて」と「PHP 8.1 以前は PHP の開発元からは更新されない。配布元によっては修正を取り込んで（バックポートして）提供している」](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/sec-017/17-1-php-advisory.png)

黄色の 2 行は、こう言っています。

- Linux の OS の提供元は修正を前もって受け取っているので、更新したパッケージが出ているかを、その提供元に確かめること
- PHP 8.1 以前は PHP の開発チームからはもう更新されないが、提供元によっては修正を取り込んで出していること

つまり、Ubuntu のパッケージで入れた PHP では、`php -v` の番号は「OS を出した時の PHP の番号」です。直ったかどうかは、この番号には出ません。

## 直ったかは、パッケージの番号を OS の提供元の告知と照らす

Ubuntu は、弱点の番号ごとに告知のページを出しています。今回の 1 件目のページの表がこれです。

![Ubuntu の CVE-2026-109618 のページ（ubuntu.com/security/CVE-2026-109618）の表の一部。パッケージごとに Ubuntu のバージョンと状態が並ぶ。黄色の 2 行は、php8.1 の「22.04 LTS jammy」が「Fixed in 8.1.2-1ubuntu2.28」、php8.3 の「24.04 LTS noble」が「Fixed in 8.3.6-0ubuntu0.24.04.13」。ほかの行は「Not in release」](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/sec-017/17-3-ubuntu-cve.png)

php8.3 の 24.04 は「Fixed in 8.3.6-0ubuntu0.24.04.13」。この番号のパッケージで直った、という意味です。手元のパッケージは `8.3.6-0ubuntu0.24.04.14` で、この番号より新しいので、直っています。

同じ表で、php8.1 の 22.04 は「Fixed in 8.1.2-1ubuntu2.28」です。PHP の開発チームは 8.1 の新しい番号をもう出していませんが、Ubuntu 22.04 の PHP 8.1 は直っています。

確かめる手順は次の 4 つです。

1. Web のリクエストを処理している PHP のパッケージの番号を出す。PHP-FPM なら `dpkg-query -W php8.3-fpm`、Apache の PHP なら `dpkg-query -W libapache2-mod-php8.3`（8.3 は自分の PHP に合わせる）。`php -v` は命令で使う PHP の番号で、Web を処理する PHP は別のパッケージです。
2. OS の提供元の告知で、自分の OS の「直った番号」を見る。Ubuntu は `https://ubuntu.com/security/` の後ろに、Debian は `https://security-tracker.debian.org/tracker/` の後ろに、CVE の番号を付けたページです。今回のように弱点が 2 件なら、2 件ともページを見ます。Debian では、下の表の「Fixed Version」の欄が直った番号です。
3. 手元のパッケージの番号が、直った番号と同じか新しいかを見る。目で比べにくい時は `dpkg --compare-versions 手元の番号 ge 直った番号 && echo fixed` で、`fixed` と出れば同じか新しいです。
4. 更新したら PHP-FPM や Apache を再起動する。動いている PHP は、再起動するまで古いプログラムのまま動き続けます。

## 後ろに OS の名前が付いていても、直っているとは限らない

番号の後ろに `ubuntu` や `deb12u1` が付いていると、OS の提供元が手を入れたパッケージだと分かります。でも、それだけで直っているとは言えません。Debian の告知のページで見てみます。

![Debian の security tracker の CVE-2026-109618 のページ（security-tracker.debian.org/tracker/CVE-2026-109618）の「Vulnerable and fixed packages」の表。黄色の 2 行は php8.2 の Debian 12（bookworm）で、「bookworm」の 8.2.32-1~deb12u1 が「vulnerable」、「bookworm (security)」の 8.2.36-1~deb12u1 が「fixed」。その下に php8.4 の trixie などの行が「fixed」で並ぶ。下の直った番号の表では、php8.2 の bookworm の行（Fixed Version が 8.2.35-1~deb12u1・Origin が DLA-4848-1）が黄色](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/sec-017/17-4-debian-tracker.png)

Debian 12 の php8.2 は、ふつうの置き場の `8.2.32-1~deb12u1` が vulnerable（弱点が残っている）、セキュリティの更新の置き場の `8.2.36-1~deb12u1` が fixed（直った）です。どちらも後ろに `~deb12u1` が付いていますが、直っているのは片方だけです。セキュリティの更新を受け取る設定にして、更新を当てたサーバーだけが直ります。

下の表の「Fixed Version」を見ると、Debian 12 の直った番号は `8.2.35-1~deb12u1` です。Ubuntu と違い、PHP の修正版と同じ 8.2.35 の番号でした。番号の付け方は OS ごとに違うので、どちらの場合も告知の「直った番号」と照らします。

番号が古く見えても直っていることがあり、OS の名前が付いていても直っていないことがあります。どちらの場合も、決め手は OS の提供元の告知の「直った番号」です。

## 用語

- **修正版**: 弱点を直したバージョン。PHP の今回の修正版は 8.5.12・8.4.27・8.3.36・8.2.35。
- **OS のパッケージ**: Ubuntu や Debian などの OS の提供元が、ソフトウェアを OS に合わせてまとめたもの。apt で入れる PHP はこれ。
- **OS の提供元**: Ubuntu（Canonical 社）や Debian など、OS とそのパッケージを作って配っているところ。
- **バックポート**: 新しいバージョンで入った修正を、番号を上げずに今のバージョンに取り込むこと。
- **CVE の番号**: 公開された弱点に一つずつ付く番号。今回は CVE-2026-109618 と CVE-2026-109619。
- **PHP-FPM**: Web サーバーから届いたリクエストを PHP で処理するプログラム。
- **遠隔のコード実行（リモートコード実行）**: 離れた場所から、サーバーの上で好きな命令を実行されてしまうこと。

---

この連載では、攻撃ツールが誰でも無料で手に入る時代に、自分のサービスを自分で検査し、弱いところを先に見つけて直す手順を、順に示します。検査の対象は自社サービスに限り、他人のサイトを攻撃する手順は扱いません。
自分で検査するのが難しい方は、EarthLink Network が検査を代行します。弱点は見つかったが自分では直せない、という方は、私たちが修正まで引き受けます。お気軽にご相談ください。
役に立ったと感じたら、ぜひ「いいね」と記事の購読をお願いいたします。

自社プロダクトは https://www.eln.ne.jp/products にまとめています。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. でAI開発をしています。2025年からClaude Codeを開発の主体に据え、今は20を超えるプロダクトを1人で同時に開発・運用しています。この連載では、その現場で実際に起きたこと（うまくいったことも、失敗も）を、数字と一緒に書いていきます。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。
