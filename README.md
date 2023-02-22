# 🔍 kensaku.vim

[![Deno 1.28.0 or above](https://img.shields.io/badge/Deno-Support%201.28.0-yellowgreen.svg?logo=deno)](https://github.com/denoland/deno/tree/v1.28.0)
[![Vim 9.0.0472 or above](https://img.shields.io/badge/Vim-Support%209.0.0472-yellowgreen.svg?logo=vim)](https://github.com/vim/vim/tree/v9.0.0472)
[![Neovim 0.8.0 or above](https://img.shields.io/badge/Neovim-Support%200.8.0-yellowgreen.svg?logo=neovim&logoColor=white)](https://github.com/neovim/neovim/tree/v0.8.0)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![vim help](https://img.shields.io/badge/vim-%3Ah%20kensaku-orange.svg)](doc/kensaku.jax)
[![test](https://github.com/lambdalisue/kensaku.vim/actions/workflows/test.yml/badge.svg)](https://github.com/lambdalisue/kensaku.vim/actions/workflows/test.yml)

Kensaku (_kensaku.vim_) は Migemo
を利用してローマ字のまま日本語検索を行う最低限の機能を提供するプラグインです。

既存の Migemo プラグインとは異なり JavaScript で実装された [jsmigemo][jsmigemo]
を利用するため [C/Migemo][c/migemo] のインストールは不要です。

[jsmigemo]: https://github.com/oguna/jsmigemo
[c/migemo]: https://www.kaoriya.net/software/cmigemo/

## 必須条件

Kensaku は denops プラグインとして作られているため [Deno](https://deno.land) と
[vim-denops/denops.vim][vim-denops/denops.vim]
がインストールされている必要があります。

- [vim-denops/denops.vim][vim-denops/denops.vim]<br> An ecosystem for writing
  Vim/Neovim plugin in Deno.

[vim-denops/denops.vim]: https://github.com/vim-denops/denops.vim

## インストール

[Deno](https://deno.land) をインストール後
[vim-plug](https://github.com/junegunn/vim-plug)
などを利用して以下の様にインストールします。

```vim
Plug 'vim-denops/denops.vim'
Plug 'lambdalisue/kensaku.vim'
```

## 利用方法

### インテグレーションプラグイン

必要なインターフェースを提供するインテグレーションプラグインを利用します。

- [lambdalisue/kensaku-command.vim](https://github.com/lambdalisue/kensaku-command.vim)<br>ローマ字によるバッファ内日本語検索を行う
  `Kensaku` コマンドを提供するプラグイン
- [lambdalisue/kensaku-search.vim](https://github.com/lambdalisue/kensaku-search.vim)<br>
  `/` や `?` による検索にローマ字による日本語検索を自然に提供するプラグイン
- [Milly/ddu-filter-kensaku](https://github.com/Milly/ddu-filter-kensaku)<br>
  [Shougo/ddu.vim](https://github.com/Shougo/ddu.vim)
  にてローマ字による日本語フィルタを有効にするプラグイン
- [ompugao/ctrlp-kensaku](https://github.com/ompugao/ctrlp-kensaku)<br>
  [ctrlpvim/ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim)
  にてローマ字による日本語フィルタを有効にするプラグイン
- [yuki-yano/fuzzy-motion.vim](https://github.com/yuki-yano/fuzzy-motion.vim)<br>バッファ内の任意の場所にジャンプする際にローマ字指定で日本語を指定可能

### TypeScript からの利用 (denops)

[`denops.dispatch()`](https://deno.land/x/denops_std@v4.0.0/mod.ts?s=Denops#method_dispatch_5)
を利用して `query` 関数を呼び出します。

```
const pattern = await denops.dispatch("kensaku", "query", "kensaku");
```

### Vim script からの利用

`kensaku#query()` を以下のように呼び出します。

```vim
function! Search(value) abort
  let @/ = '\m' .. a:value
  normal! n
endfunction

call Search(kensaku#query('kensaku'))
```

上記は処理を同期的に待つので、非同期が必要な場合は代わりに
`kensaku#query_async()` を利用します。

```vim
function! Search(value) abort
  let @/ = '\m' .. a:value
  normal! n
endfunction

call kensaku#query_async('kensaku', {
      \ 'failure': { v -> Search(v) },
      \})
```

## v2 との違い

Kensaku [v2](https://github.com/lambdalisue/kensaku.vim/tree/v2) までは
`kensaku#query()` などで返す正規表現は JavaScript のものだったため `\v`
を前置することで Very magic を指定する必要がありました。 Kensaku v3 からは
`kensaku#query()` は Vim script の正規表現を返すように変更されたので `\v`
の代わりに `\m` を前置します。 なおプラグイン作者などが利用する
`denops.dispatch()` 経由での呼び出しは依然として JavaScript
の正規表現を返します。

## 参考情報

- [Migemo: ローマ字のまま日本語をインクリメンタル検索](http://0xcc.net/migemo/)
- [辞書構造を工夫した Migemo 実装の紹介](https://qiita.com/oguna/items/c70e8c409b663d74113e)
- [Vim で migemo を使って日本語でもローマ字のまま検索がしたい](http://haya14busa.com/vim_migemo_search/)

## License

The code in this repository follows MIT license, texted in [LICENSE](./LICENSE).
Contributors need to agree that any modifications sent in this repository follow
the license.
