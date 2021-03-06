---
layout: news_post
title: "Ruby 2.7.0 veröffentlicht"
author: "naruse"
translator: "Marvin Gülker"
date: 2019-12-25 00:00:00 +0000
lang: de
---

Wir freuen uns, die Veröffentlichung von Ruby 2.7.0 bekannt geben
zu können.

Sie enthält eine Anzahl neuer Funktionalitäten und
Performanzverbesserungen, namentlich:

* Musterabgleiche
* Verbesserungen der interaktiven Kommandozeile _(REPL)_
* Defragmentierung für den GC _(Campaction GC)_
* Trennung der Positions- und der Schlüsselwortargumente

## Musterabgleiche [Experimentell]

Musterabgleiche sind eine häufig genutzte Funktion funktionaler
Programmiersprachen. Mit 2.7.0 werden sie als experimentell in die
Programmiersprache Ruby eingeführt. [#14912](https://bugs.ruby-lang.org/issues/14912)

Ein Musterabgleich untersucht das übergebene Objekt und weist seinen
Wert dann zu, wenn er auf ein bestimmtes Muster passt.

```ruby
require "json"

json = <<END
{
  "name": "Alice",
  "age": 30,
  "children": [{ "name": "Bob", "age": 2 }]
}
END

case JSON.parse(json, symbolize_names: true)
in {name: "Alice", children: [{name: "Bob", age: age}]}
  p age #=> 2
end
```

Weitere Details können Sie der Präsentation [Musterabgleiche - Neue Funktion in Ruby 2.7](https://speakerdeck.com/k_tsj/pattern-matching-new-feature-in-ruby-2-dot-7)
entnehmen.

## Verbesserung der interaktiven Kommandozeile

Die mitgelieferte interaktive Kommandozeile `irb` _(REPL: Read-Eval-Print-Loop)_
wird mehrzeiliges Editieren unterstützen. Dies wird mithilfe von
`reline`, einer `readline`-kompatiblen nur mit Ruby implementierten Bibliothek,
umgesetzt. Darüber hinaus wird `irb` besser mit RDoc integriert: es
wird möglich, die Referenzdokumentation für eine bestimmte Klasse,
ein bestimmtes Modul oder auch eine bestimmte Methode nachzuschlagen.
[[Feature #14683]](https://bugs.ruby-lang.org/issues/14683),
[[Feature #14787]](https://bugs.ruby-lang.org/issues/14787),
[[Feature #14918]](https://bugs.ruby-lang.org/issues/14918)

Schließlich werden von `Binding.irb` angezeigte Quellcode-Zeilen und
Ergebnisse von `inspect` farblich hevorgehoben werden.

<video autoplay="autoplay" controls="controls" muted="muted" width="576" height="259" poster="https://cache.ruby-lang.org/pub/media/irb-reline-screenshot.png">
  <source src="https://cache.ruby-lang.org/pub/media/irb_improved_with_key_take3.mp4" type="video/mp4">
</video>

## Defragmentierender GC

Die neue Version wird einen defragmentierenden GC _(Compaction GC)_
einführen, der fragmentierten Arbeitsspeicherplatz defragmentieren
kann.

Einige Ruby-Programme, die mit mehreren Threads arbeiten, leiden
derzeit unter Speicherfragmentierung. Dies verursacht einen hohen
Speicherverbrauch und eine reduzierte Ausführungsgeschwindigkeit.

Zur Behebung des Problems wird die neue Methode `GC.compact`
eingeführt. Sie defragmentiert den Freispeicher _(Heap)_, indem sie
noch aktive Objekte näher zusammenrückt, sodass weniger Speicherseiten
benötigt werden und der Freispeicher insgesamt besser für
Copy-on-Write (CoW) geeignet ist. [#15626](https://bugs.ruby-lang.org/issues/15626)

## Trennung von Positions- und Schlüsselwortargumenten

Die automatische Konvertierung zwischen Schlüsselwort- und
Positionsargumenten gilt nun als veraltet und wird mit Ruby 3 entfernt
werden. [[Feature #14183]](https://bugs.ruby-lang.org/issues/14183)

Siehe den Artikel "[Trennung von Positions- und Schlüsselwortargumenten in Ruby 3.0](https://www.ruby-lang.org/de/news/2019/12/12/separation-of-positional-and-keyword-arguments-in-ruby-3-0/)"
für Details. Die Änderungen stellen sich wie folgt dar:

* Wenn bei einem Methodenaufruf ein Hash als letztes Argument
  übergeben wird, wenn sonstige Argumente fehlen und wenn die
  aufgerufene Methode Schlüsselwortargumente annimmt, dann gibt Ruby
  eine Warnung aus. Um das Hash weiterhin als Schlüsselwortargument zu
  verwenden, ist es notwendig, den doppelten Auflösungsoperator
  _(double splat operator)_ zu benutzen. Nur so kann die Warnung
  vermieden und das korrekte Verhalten in Ruby 3 sichergestellt
  werden.

  ```ruby
  def foo(key: 42); end; foo({key: 42})   # Warnung
  def foo(**kw);    end; foo({key: 42})   # Warnung
  def foo(key: 42); end; foo(**{key: 42}) # OK
  def foo(**kw);    end; foo(**{key: 42}) # OK
  ```

* Wenn bei einem Methodenaufruf Schlüsselwortargumente an eine
  Methode, die auch Schlüsselwortargumente akzeptiert, übergeben
  werden, jedoch nicht genügend Schlüsselwortargumente bereitgestellt
  werden, dann werden die übergebenen Schlüsselwortargumente als
  finales erforderliches Positionsargument gewertet und eine Warnung
  wird ausgegeben. Übergeben Sie das Argument als Hash statt als
  Schlüsselwortargumentliste, um die Warnung zu vermeiden und
  korrektes Verhalten in Ruby 3 sicherzustellen.

  ```ruby
  def foo(h, **kw); end; foo(key: 42)      # Warnung
  def foo(h, key: 42); end; foo(key: 42)   # Warnung
  def foo(h, **kw); end; foo({key: 42})    # OK
  def foo(h, key: 42); end; foo({key: 42}) # OK
  ```

* Wenn eine Methode bestimmte Schlüsselwortargumente, nicht aber den
  doppelten Auflösungsoperator verwendet, und ein Hash oder eine
  Schlüsselwortargumenteliste mit einer Mischung aus Strings und
  Symbolen übergeben wird, dann wird das Hash weiterhin aufgespalten.
  Zudem wird eine Warnung ausgegeben. Sie müssen für das korrekte
  Verhalten in Ruby 3 den aufrufenden Code so ändern, dass zwei
  einzelne Hashes übergeben werden.

  ```ruby
  def foo(h={}, key: 42); end; foo("key" => 43, key: 42)   # Warnung
  def foo(h={}, key: 42); end; foo({"key" => 43, key: 42}) # Warnung
  def foo(h={}, key: 42); end; foo({"key" => 43}, key: 42) # OK
  ```

* Wenn eine Methode keine Schlüsselwortargumente akzeptiert, aber mit
  solchen aufgerufen wird, werden solche Schlüsselwortargumente
  weiterhin ohne Warnung als Hash für ein Positionsargument
  interpretiert. Dieses Verhalten wird auch in Ruby 3 weiterhin
  beibehalten.

  ```ruby
  def foo(opt={});  end; foo( key: 42 )   # OK
  ```

* Schlüsselwortargumente mit anderen Schlüsseln als Symbolen sind
  zulässig, wenn die Methode beliebige Schlüsselwortargumente
  akzeptiert. [[Feature #14183]](https://bugs.ruby-lang.org/issues/14183)

  ```ruby
  def foo(**kw); p kw; end; foo("str" => 1) #=> {"str"=>1}
  ```

* `**nil` kann genutzt werden, um in einer
  Methodendefinition ausdrücklich festzulegen, dass die Methode keine
  Schlüsselwörter akzeptiert. Der Aufruf einer solchen Methode mit
  Schlüsselwortargumenten erzeugt einen ArgumentError. [[Feature #14183]](https://bugs.ruby-lang.org/issues/14183)

  ```ruby
  def foo(h, **nil); end; foo(key: 1)       # ArgumentError
  def foo(h, **nil); end; foo(**{key: 1})   # ArgumentError
  def foo(h, **nil); end; foo("str" => 1)   # ArgumentError
  def foo(h, **nil); end; foo({key: 1})     # OK
  def foo(h, **nil); end; foo({"str" => 1}) # OK
  ```

* Die Übergabe einess leeren doppelten Auflösungsoperators an eine
  Methode, die keine Schlüsselwortargumente akzeptiert, führt nicht
  länger zur impliziten Übergabe eines leeren Hashes, es sei denn, das
  Hash ist wegen eines erforderlichen Parameters notwendig.
  In diesem Fall wird eine Warnung ausgegeben. Entfernen Sie den
  doppelten Auflösungsoperator, um ein Hash als Positionsargument zu
  übergeben. [[Feature #14183]](https://bugs.ruby-lang.org/issues/14183)

  ```ruby
  h = {}; def foo(*a) a end; foo(**h) # []
  h = {}; def foo(a) a end; foo(**h)  # {} und Warnung
  h = {}; def foo(*a) a end; foo(h)   # [{}]
  h = {}; def foo(a) a end; foo(h)    # {}
  ```

Wenn Sie die Veraltungswarnungen unterdrücken wollen, verwenden Sie
bitte den Kommandozeilenschalter `-W:no-deprecated` oder fügen Ihrem
Code den Befehl `Warning[:deprecated] = false` hinzu.

## Sonstige bemerkenswerte neue Funktionen

* Blockparameter werden für abgekürzten Zugriff automatisch nummeriert.
  [#4475](https://bugs.ruby-lang.org/issues/4475)

* Es werden anfangslose Range-Objekte eingeführt. Diese sind vielleicht nicht so
  nützlich wie endlose Range-Objekte, könnten aber für
  domänenspezifische Sprachen praktisch sein.
  [#14799](https://bugs.ruby-lang.org/issues/14799)

  ```ruby
  ary[..3]  # identical to ary[0..3]
  rel.where(sales: ..100)
  ```

* `Enumerable#tally` wird hinzugefügt. Die Methode zählt das Vorkommen
  jedes Elements.

  ```ruby
  ["a", "b", "c", "b"].tally
  #=> {"a"=>1, "b"=>2, "c"=>1}
  ```

* Es ist jetzt zulässig, eine private Methode auf dem Schlüsselwort `self` aufzurufen.
  [[Feature #11297]](https://bugs.ruby-lang.org/issues/11297)
  [[Feature #16123]](https://bugs.ruby-lang.org/issues/16123)

  ```ruby
  def foo
  end
  private :foo
  self.foo
  ```

* `Enumerator::Lazy#eager` wird hinzugefügt. Diese Methode generiert
  einen nicht verzögertern Enumerator (_non-lazy enumerator_) aus
  einem verzögerten Enumerator (_lazy enumerator_). [[Feature #15901]](https://bugs.ruby-lang.org/issues/15901)

  ```ruby
  a = %w(foo bar baz)
  e = a.lazy.map {|x| x.upcase }.map {|x| x + "!" }.eager
  p e.class               #=> Enumerator
  p e.map {|x| x + "?" }  #=> ["FOO!?", "BAR!?", "BAZ!?"]
  ```

## Performanzverbesserungen

* JIT [Experimentell]

  * JIT-kompilierter Code wird neu zu weniger optimiertem Code
    kompiliert, wenn eine Optimierungsannahme sich als falsch
    herausstellt.

  * Method-Inlining wird durchgeführt, wenn eine Methode als rein
    _(pure)_ gilt. Diese Optimierung ist noch experimentell und
    zahlreiche Methoden gelten noch nicht als rein.

  * Der Standardwert von `--jit-min-calls` wird von 5 auf 10.000
    geändert.

  * Der Standardwert von `--jit-max-cache` wird von 1.000 auf 100
    geändert.

* Die Cache-Strategie von Fiber wurde verändert und die Erstellung von
  Fibers beschleunigt.
  [GH-2224](https://github.com/ruby/ruby/pull/2224)

* `Module#name`, `true.to_s`, `false.to_s`,
  und `nil.to_s` geben jetzt immer einen eingefrorenen String zurück. Der
  zurückgegebene String ist für das jeweilige Objekt immer derselbe.
  [Experimenell] [[Feature #16150]](https://bugs.ruby-lang.org/issues/16150)

* Die Performanz von `CGI.escapeHTML` wurde verbessert. [GH-2226](https://github.com/ruby/ruby/pull/2226)

* Die Performanz von Monitor und MonitorMixin wurde verbessert.
  [[Feature #16255]](https://bugs.ruby-lang.org/issues/16255)

* Der aufruferspezifische Methoden-Cache, den es seit etwa 1.9 gibt,
  wurde verbessert: die Rückgriffsrate auf den Cache konnte von
  89% auf 94% erhöht werden. Siehe [GH-2583](https://github.com/ruby/ruby/pull/2583)

* Die Methode RubyVM::InstructionSequence#to_binary generiert
  kompilierten Binärcode. Die Größe des Binärcodes wurde reduziert.
  [Feature #16163]


## Sonstige bemerkenswerte Änderungen seit 2.6

* Einige Standardbibliotheken werden aktualisiert.
  * Bundler 2.1.2
    ([Veröffentlichungshinweise](https://github.com/bundler/bundler/releases/tag/v2.1.2))
  * RubyGems 3.1.2
    * ([Veröffentlichungshinweise für 3.1.0](https://github.com/rubygems/rubygems/releases/tag/v3.1.0))
    * ([Veröffentlichungshinweise für 3.1.1](https://github.com/rubygems/rubygems/releases/tag/v3.1.1))
    * ([Veröffentlichungshinweise für 3.1.2](https://github.com/rubygems/rubygems/releases/tag/v3.1.2))
  * Racc 1.4.15
  * CSV 3.1.2
    ([Neuigkeiten](https://github.com/ruby/csv/blob/v3.1.2/NEWS.md))
  * REXML 3.2.3
    ([Neuigkeiten](https://github.com/ruby/rexml/blob/v3.2.3/NEWS.md))
  * RSS 0.2.8
    ([Neuigkeiten](https://github.com/ruby/rss/blob/v0.2.8/NEWS.md))
  * StringScanner 1.0.3
  * Es werden auch einige Bibliotheken aktualisiert, die nicht über
    eine eigenständige Versionsnummer verfügen.

* Die folgenden Programmbibliotheken sind nicht länger als mitgelieferte
  Gems enthalten. Installieren Sie die entsprechenden Gems, um diese
  Features zu verwenden.

  * CMath (Gem cmath)
  * Scanf (Gem scanf)
  * Shell (Gem shell)
  * Synchronizer (Gem sync)
  * ThreadsWait (Gem thwait)
  * E2MM (Gem e2mmap)

* `profile.rb` wurde aus der Standardbibliothek entfernt.

* Änderung von stdlib-Bibliotheken in Standard-Gems:
  * Die folgenden Standard-Gems wurden auf rubygems.org veröffentlicht:
    * benchmark
    * cgi
    * delegate
    * getoptlong
    * net-pop
    * net-smtp
    * open3
    * pstore
    * singleton
  * Die folgenden Standard-Gems werden nur mit Rubys Kern, aber noch
    nicht auf rubygems.org veröffentlicht:
    * monitor
    * observer
    * timeout
    * tracer
    * uri
    * yaml

* Die Nutzung von `Proc.new` und `proc` ohne Block in einer Methode,
  die einen Block erwartet, führt nun zu einer Warnung.

* Die Nutzung von `lambda` ohne block in einer Methode, die einen
  Block erwartet, erzeugt einen Fehler.

* Die Unicode- und Emoji-Version werden von 11.0.0 auf 12.0.0 angehoben.
  [[Feature #15321]](https://bugs.ruby-lang.org/issues/15321)

* Die Unicode-Version wird auf 12.1.0 angehoben, um Unterstützung für
  U+32FF SQUARE ERA NAME REIWA zu erhalten.  [[Feature #15195]](https://bugs.ruby-lang.org/issues/15195)

* `Date.jisx0301`, `Date#jisx0301`, and `Date.parse` unterstützen die
  neue japanische Ära.
  [[Feature #15742]](https://bugs.ruby-lang.org/issues/15742)

* Compiler müssen jetzt C99 unterstützen.
  [[Misc #15347]](https://bugs.ruby-lang.org/issues/15347)
  * Details zum genutzten Dialekt:
    <https://bugs.ruby-lang.org/projects/ruby-master/wiki/C99>

Siehe die [NEWS](https://github.com/ruby/ruby/blob/v2_7_0/NEWS)
oder die [Commit-Logs](https://github.com/ruby/ruby/compare/v2_6_0...v2_7_0)
für weitere Details.

{% assign release = site.data.releases | where: "version", "2.7.0" | first %}

Mit diesen Änderungen wurden [{{ release.stats.files_changed }}
Dateien geändert, {{ release.stats.insertions }} Einfügungen(+), {{ release.stats.deletions }} Löschungen(-)](https://github.com/ruby/ruby/compare/v2_6_0...v2_7_0)
seit Ruby 2.6.0!

Frohe Weihnachten, schöne Ferien, und viel Spaß bei der Programmierung mit Ruby 2.7!

## Download

* <{{ release.url.bz2 }}>

      SIZE: {{ release.size.bz2 }}
      SHA1: {{ release.sha1.bz2 }}
      SHA256: {{ release.sha256.bz2 }}
      SHA512: {{ release.sha512.bz2 }}

* <{{ release.url.gz }}>

      SIZE: {{ release.size.gz }}
      SHA1: {{ release.sha1.gz }}
      SHA256: {{ release.sha256.gz }}
      SHA512: {{ release.sha512.gz }}

* <{{ release.url.xz }}>

      SIZE: {{ release.size.xz }}
      SHA1: {{ release.sha1.xz }}
      SHA256: {{ release.sha256.xz }}
      SHA512: {{ release.sha512.xz }}

* <{{ release.url.zip }}>

      SIZE: {{ release.size.zip }}
      SHA1: {{ release.sha1.zip }}
      SHA256: {{ release.sha256.zip }}
      SHA512: {{ release.sha512.zip }}

## Was ist Ruby

Die Programmiersprache Ruby wurde 1993 von Yukihiro „Matz“ Matsumoto
erfunden und wird heute als quelloffene Software entwickelt. Sie läuft
auf verschiedenen Plattformen und wird überall auf der Welt namentlich
für die Webentwicklung genutzt.
