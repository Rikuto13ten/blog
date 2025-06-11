+++
title = "Hugo & ox-hugo & github-pages で、org-mode で投稿するブログを作る"
author = ["Rikuto13ten"]
date = 2025-06-08T00:00:00+09:00
tags = ["Hugo", "org-mode", "ox-hugo"]
draft = false
+++

このブログサイトを作るにあたってやったことを備忘録的に書いていきます。 <br/>


## 技術選定 {#技術選定}

要件は以下 <br/>

-   org-mode で書いたものを `.md` なりに変換してくれる <br/>
-   ある程度カスタマイズができる(見ため等) <br/>


### 静的サイトジェネレーター {#静的サイトジェネレーター}

候補にあがったのは、 **one.el** と **Hugo** です。 <br/>
結論から言うと、 **Hugo** にしました。 <br/>


#### one.el {#one-dot-el}

URL : <https://one.tonyaldon.com/> <br/>
公式の説明(翻訳)によると、 <br/>

> -   固有のorgファイルに含まれる、 <br/>
> -   たった1つのEmacsコマンドでレンダリングされ、 <br/>
> -   Emacs Lispコード（CSSも）を書いて変更できるもの <br/>
> -   プレーンなEmacs Lispデータである「HTMLテンプレート」を使用して、 <br/>
> -   設定ファイルなしで、 <br/>
> -   外部の静的サイトジェネレータに依存しないのですか? <br/>
> 
> one.el. <https://one.tonyaldon.com/> (参照 2025-06-07) <br/>

とのこと。 <br/>

なかなか魅力的ですね。 <br/>
org-mode -&gt; HTML に直接変換するようです。 <br/>
設定が Emacs-Lisp で書けるのは便利ですね。 <br/>

しかし、単一の org ファイルに書くようです。 <br/>
私は、現在は org-roam で 1ファイルごとに記事を書いていく方針なのでそこだけ合わなかったです。 <br/>

いつかどこかで試してみたいと思います。 <br/>


#### Hugo {#hugo}

URL : <https://gohugo.io/> <br/>
Hugo は、 Go で書かれた静的サイトジェネレーターです。 <br/>

実際に、 Hugo を使って org-mode で記事を書いている方も大勢おり、 <br/>
**ox-hugo** という org-mode を hugo の形式に合わせた markdown に変換してくれる Emacs のパッケージもあるくらいです。 <br/>
theme も自分でカスタマイズできるということもあり、こちらを使うことに決めました。 <br/>


## 環境構築 {#環境構築}

環境構築は、 nix-flake で行ないました。 <br/>
以下のように、書いて `nix develop` するだけです。 <br/>
Nix Flake を導入してから環境構築が本当に楽になりました。 <br/>

```nix
{
  description = "Hugo development environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = with pkgs; [
            hugo
          ];

          shellHook = ''
            echo "Hugo development environment loaded!"
            echo "Hugo version: $(hugo version)"
          '';
        };
      }
    );
}
```

環境構築が成功すると、以下のようにディレクトリが作られているかと思います。 <br/>
. <br/>
├─ archetypes <br/>
├─ assets <br/>
├─ content <br/>
├─ data <br/>
├─ i18n <br/>
├─ layouts <br/>
├─ public <br/>
├─ static <br/>
├─ hugo.toml <br/>
├─ flake.nix <br/>
├─ flake.lock <br/>
└─ README.md <br/>

**ox-hugo** の導入は、 <br/>
`package.el` なりに、以下のように書きます。 <br/>

```emacs-lisp
(use-package ox-hugo
  :ensure t
  :pin melpa
  :after ox
  :config
  (setq org-hugo-preserve-filling t)
  (setq org-export-preserve-breaks t))
```


## theme の作成 {#theme-の作成}

まず始めに、 theme を作るところから行いました。 <br/>
既存のテーマも良かったのですが、自分の満足いく見ためにしたかったので作ることにしました。 <br/>
以下のコマンドで、新しいテーマを作成します。 <br/>

```shell
hugo new theme [name]
```

実行が終わると、 `theme/[name]` に以下の構成でできているかと思います。 <br/>
. <br/>
├─ archetypes <br/>
├─ assets <br/>
├─ content <br/>
├─ data <br/>
├─ i18n <br/>
├─ layouts <br/>
├─ public <br/>
├─ static <br/>
└─ hugo.toml <br/>

見た目を変更するには、主に `assets/css/main.css` に css で修飾していきます。 <br/>


### コードブロックのシンタックスハイライトを変える {#コードブロックのシンタックスハイライトを変える}

<https://gohugo.io/quick-reference/syntax-highlighting-styles/> からシンタックスハイライトを選びます。 <br/>
例えば、 `rose-pine-moon` にするとします。 <br/>
以下のように、theme 内に `rose-pine-moon` の css を入れます。 <br/>

```shell
hugo gen chromastyles --style=rose-pine-moon > ./theme/my-theme/static/css/syntax.css
```

その後、 `./theme/my-theme/layouts/_partials/head.html` に以下を追記します。 <br/>

```css
<link rel="stylesheet" href="{{ .Site.BaseURL }}css/syntax.css" />
```


## org ファイルを markdown に変換する {#org-ファイルを-markdown-に変換する}

テーマがカスタマイズできたら、org ファイルをつくって記事にしていきます。 <br/>
プロジェクトルートに `org/` などのディレクトリを作成し、そのなかに org ファイルを作成します。 <br/>
org ファイルの先頭には Hugo 用のオプションを書く必要があります。 <br/>

```org
#+TITLE: Hugo & ox-hugo & github-pages で、org-mode で投稿するブログを作る
#+AUTHOR: Rikuto13ten
#+DATE: <2025-06-08>
#+HUGO_BASE_DIR: ../../
#+HUGO_DRAFT: false
#+HUGO_TAGS: Hugo org-mode ox-hugo
#+STARTUP: showall
```

`#+HUGO_BASE_DIR` は、 org ファイルからみて  `./content/` がどこにあるのかを書くものです。 <br/>
例えば、私の場合は `./org/permanent/` の中に記事を書いているので、org ファイルからのパスは `../../` となります。 <br/>

`#+HUGO_TAGS` は、単純に記事のタグです。Zenn や、Qiita と同じです。 <br/>

ファイルを markdonw に出力するには、 `C-c C-e H H` というキーバインドを打ちます。 <br/>
↓他のコマンドを割り当てている方用に <br/>

実行すると、 `./content/posts/` の中に md ファイルが作成されていると思います。 <br/>


## GitHub Pages でサイトを公開する {#github-pages-でサイトを公開する}

サイトの設定が済んだので、公開したいと思います。 <br/>
プロジェクトを GitHub に公開リポジトリとして登録します。 <br/>
ドキュメントに方法がのっているので、参考にします。 <br/>
特につまずくこともなくできました。 <br/>
<https://gohugo.io/host-and-deploy/host-on-github-pages/> <br/>


## つまったところ {#つまったところ}

-   **org-mode の改行が markdown に反映されない** <br/>
    以下のように、設定することで改行が反映されます。 <br/>
    ```elisp
    ;; エクスポート時にすべての改行が保持される
     (setq org-export-preserve-breaks t)
    ```

-   **インラインコード -&gt; `これ` に css が適用されない** <br/>
    以下のようにすることで適用されます。 <br/>
    ```css
    :not(pre) > code {}
    ```

