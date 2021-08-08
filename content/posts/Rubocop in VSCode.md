---
title: "Rubocop in VSCode"
date: 2021-08-08T01:42:13+08:00
draft: false
description: 在 vscode 中自動套用 ruby linter - rubocop
tags:
  - Railse
  - rubocop
categories:
  - Railse
---

首先在 Gemfile 安裝 rubocop

`gem 'rubocop', '~> 1.18', require: false`

下 `bundle install` 安裝

在專案根目錄建立 `.rubocop.yml`

內容可以參考[這篇]([https://gist.github.com/jhass/a5ae80d87f18e53e7b56](https://gist.github.com/jhass/a5ae80d87f18e53e7b56))

vscode 可能會出現這樣的 warning:

```
The following cops were added to RuboCop, 
but are not configured. Please set Enabled to either `true` or `false` in your `.rubocop.yml` file. 
Please also note that can also opt-in to new cops by default by 
adding this to your config: AllCops: NewCops: enable
Gemspec/DateAssignment: # (new in 1.10) Enabled: true 
Layout/LineEndStringConcatenationIndentation: # (new in 1.18) Enabled: true 
....
```

官方有[一篇文章](https://docs.rubocop.org/rubocop/0.85/versioning.html)說明這個原因

意思是指在新版的 rubocop 有新增加的規則，但在 rubocop.yml 中沒有明確指示是否 enable

解法是可以在 rubocop.yml 中加上這行

`AllCops: NewCops: enable` 

代表說之後的新規則都預設打開

或者也可以根據 warning 中的每一條規則都加進 rubocop.yml 中去做設定

最終我的 rubocop.yml 是長這樣

```yaml
AllCops:
  NewCops: enable
# Commonly used screens these days easily fit more than 80 characters.
Layout/LineLength:
  Max: 120

# Too short methods lead to extraction of single-use methods, which can make
# the code easier to read (by naming things), but can also clutter the class
Metrics/MethodLength:
  Max: 20

# The guiding principle of classes is SRP, SRP can't be accurately measured by LoC
Metrics/ClassLength:
  Max: 1500

# No space makes the method definition shorter and differentiates
# from a regular assignment.
Layout/SpaceAroundEqualsInParameterDefault:
  EnforcedStyle: no_space

# Single quotes being faster is hardly measurable and only affects parse time.
# Enforcing double quotes reduces the times where you need to change them
# when introducing an interpolation. Use single quotes only if their semantics
# are needed.
Style/StringLiterals:
  EnforcedStyle: double_quotes

# We do not need to support Ruby 1.9, so this is good to use.
Style/SymbolArray:
  Enabled: true

# Most readable form.
Layout/HashAlignment:
  EnforcedHashRocketStyle: table
  EnforcedColonStyle: table

# Mixing the styles looks just silly.
Style/HashSyntax:
  EnforcedStyle: ruby19_no_mixed_keys

# has_key? and has_value? are far more readable than key? and value?
Style/PreferredHashMethods:
  Enabled: false

# String#% is by far the least verbose and only object oriented variant.
Style/FormatString:
  EnforcedStyle: percent

Style/CollectionMethods:
  Enabled: true
  PreferredMethods:
    # inject seems more common in the community.
    reduce: "inject"

# Either allow this style or don't. Marking it as safe with parenthesis
# is silly. Let's try to live without them for now.
Style/ParenthesesAroundCondition:
  AllowSafeAssignment: false
Lint/AssignmentInCondition:
  AllowSafeAssignment: false

# A specialized exception class will take one or more arguments and construct the message from it.
# So both variants make sense.
Style/RaiseArgs:
  Enabled: false

# Indenting the chained dots beneath each other is not supported by this cop,
# see https://github.com/bbatsov/rubocop/issues/1633
Layout/MultilineOperationIndentation:
  Enabled: false

# Fail is an alias of raise. Avoid aliases, it's more cognitive load for no gain.
# The argument that fail should be used to abort the program is wrong too,
# there's Kernel#abort for that.
Style/SignalException:
  EnforcedStyle: only_raise

# Suppressing exceptions can be perfectly fine, and be it to avoid to
# explicitly type nil into the rescue since that's what you want to return,
# or suppressing LoadError for optional dependencies
Lint/SuppressedException:
  Enabled: false

Layout/SpaceInsideBlockBraces:
  # The space here provides no real gain in readability while consuming
  # horizontal space that could be used for a better parameter name.
  # Also {| differentiates better from a hash than { | does.
  SpaceBeforeBlockParameters: false

# No trailing space differentiates better from the block:
# foo} means hash, foo } means block.
Layout/SpaceInsideHashLiteralBraces:
  EnforcedStyle: no_space

# { ... } for multi-line blocks is okay, follow Weirichs rule instead:
# https://web.archive.org/web/20140221124509/http://onestepback.org/index.cgi/Tech/Ruby/BraceVsDoEnd.rdoc
Style/BlockDelimiters:
  Enabled: false

# do / end blocks should be used for side effects,
# methods that run a block for side effects and have
# a useful return value are rare, assign the return
# value to a local variable for those cases.
Style/MethodCalledOnDoEndBlock:
  Enabled: true

# Enforcing the names of variables? To single letter ones? Just no.
Style/SingleLineBlockParams:
  Enabled: false

# Shadowing outer local variables with block parameters is often useful
# to not reinvent a new name for the same thing, it highlights the relation
# between the outer variable and the parameter. The cases where it's actually
# confusing are rare, and usually bad for other reasons already, for example
# because the method is too long.
Lint/ShadowingOuterLocalVariable:
  Enabled: false

# Check with yard instead.
Style/Documentation:
  Enabled: false

# This is just silly. Calling the argument `other` in all cases makes no sense.
Naming/BinaryOperatorParameterName:
  Enabled: false

# There are valid cases, for example debugging Cucumber steps,
# also they'll fail CI anyway
Lint/Debugger:
  Enabled: false

# Style preference
Style/MethodDefParentheses:
  Enabled: false

```

最後在設定檔 settings.json 中加入

```yaml
"editor.formatOnSave": true,
"ruby.format": "rubocop",
"ruby.rubocop.executePath": "",
"ruby.rubocop.onSave": true
```

就可以完成在每次存檔時自動修正
