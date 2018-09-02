# julia-vim

<p align="center"><img src="logo.png" alt="julia-vim logo"/></p>

[Julia] support for Vim.

**[INSTALLATION INSTRUCTIONS]**

[Julia]: http://julialang.org/
[Installation instructions]: INSTALL.md
[YouCompleteMe]: https://github.com/Valloric/YouCompleteMe
[`sensible.vim`]: https://github.com/tpope/vim-sensible
[`a27c6ce`]: https://github.com/danielwe/julia-vim/commit/a27c6ce71e19ebf161abba5974405dd74214ba2e

## Complete documentation

The full documentation is available from Vim; after installation, type `:help
julia-vim`. The remainder of this README gives an overview of some of the
features.

* [Latex-to-Unicode substitutions](#latex-to-unicode-substitutions)
* [Block-wise movements and block text-objects](#block-wise-movements-and-block-text-objects)
* [Changing syntax highlighting depending on the Julia version](#changing-syntax-highlighting-depending-on-the-julia-version)

## LaTeX-to-Unicode substitutions

_This is an unstable fork._ Everything about LaTeX-to-Unicode is in flux at the
moment. Instead of trying to mimick the behavior in the julia shell, the goal
is to write an omnifunc that works with [YouCompleteMe] and similar
plugins for suggestions, while keeping the issue of substitution separate, as
this is conceptually more of a snippet- than completion-like functionality
(perhaps actually using snippets is the way to go?). The unicode functionality
will probably be refactored into a separate plugin in the end.

As of [`a27c6ce`] a workable integration with YouCompleteMe can be obtained by
adding the following to your `.vimrc` (hit `<C-Y>` to trigger
substitution&mdash;this is the default key to accept a suggested completion in
vim):
```vim
let g:latex_to_unicode_tab = 0
let g:latex_to_unicode_eager = 0
augroup unicode
  autocmd!
  autocmd FileType julia
        \ imap <silent> <buffer> <expr> <C-Y> LaTeXtoUnicode#AutoSub()
augroup END
if !exists('g:ycm_semantic_triggers')
  let g:ycm_semantic_triggers = {}
endif
let g:ycm_semantic_triggers.julia = ['re!\\\S+']
```
If you want to try as-you-go substitution, remove the `augroup` and add this
line instead:
```vim
let g:latex_to_unicode_auto = 1
```

## Block-wise movements and block text-objects

This plug-in defines mappings to move around julia blocks (e.g. `if/end`,
`function/end` etc.) and to manipulate them as a whole (analogously to the
standard `w`, `b` etc. commands to move on words, and to the `aw`, `iw`
commands which allow to manipulate them). These require the `matchit` plugin,
which is usually distributed with ViM but must be explicitly enabled, e.g.
adding this to your `.vimrc` file:

```vim
runtime macros/matchit.vim
```
_Note:_ if you use [`sensible.vim`], `matchit` is already enabled for you.

The default mappings use `]]`, `][`, `[[`, `[]`, `]j`, `]J`, `[j`, and `[J` for
the movements and `aj`, `ij` for the selections. These can be disabled
collectively by setting `g:julia_blocks` to `0`, or they can be remapped and/or
disabled individually by defining a `g:julia_blocks_mapping` variable. See the
documentation for details.

Note that this feature requires Vim version 7.4 or higher.

## Changing syntax highlighting depending on the Julia version

The plugin supports syntax highlighting different versions of Julia. By
default, the highlighting scheme assumes the latest stable release of Julia
(currently, version 1.0; the plugin does not differentiate between 0.7 and
1.0), but the previous one and the latest version under development are also
supported. You can set a global default in your `.vimrc`, e.g. if you follow
Julia's master you can use:

```vim
let g:default_julia_version = "devel"
```

or if you are still using Julia 0.6 you can use:

```vim
let g:default_julia_version = "0.6"
```

You can also switch version for a particular buffer, by using the
`julia#set_syntax_version()` function, e.g. by typing in Vim:

```vim
:call julia#set_syntax_version("0.6")
```
