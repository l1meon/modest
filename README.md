# modest

CSS selectors for HTML5 Parser Myhtml. Crystal wrapper for https://github.com/lexborisov/Modest

## Installation


Add this to your application's `shard.yml`:

```yaml
dependencies:
  modest:
    github: kostya/modest
```


## Usage of CSS Selectors with https://github.com/kostya/myhtml


```crystal
require "modest"

html = <<-PAGE
  <div>
    <p id=p1>
    <p id=p2 class=jo>
    <p id=p3>
      <a href="some.html" id=a1>link1</a>
      <a href="some.png" id=a2>link2</a>
    <div id=bla>
      <p id=p4 class=jo>
      <p id=p5 class=bu>
      <p id=p6 class=jo>
    </div>
  </div>
PAGE

parser = Myhtml::Parser.new(html)

# select all p nodes which id like `*p*`
p parser.css("p[id*=p]").map(&.attribute_by("id")).to_a # => ["p1", "p2", "p3", "p4", "p5", "p6"]

# select all nodes with class "jo"
p parser.css("p.jo").map(&.attribute_by("id")).to_a # => ["p2", "p4", "p6"]
p parser.css(".jo").map(&.attribute_by("id")).to_a # => ["p2", "p4", "p6"]

# select odd child tag inside div, which not contain a
p parser.css("div > :nth-child(2n+1):not(:has(a))").map(&.attribute_by("id")).to_a # => ["p1", "p4", "p6"]

# all elements with class=jo inside last div tag
p parser.css("div").to_a.last.css(".jo").map(&.attribute_by("id")).to_a # => ["p4", "p6"]

# a element with href ends like .png
p parser.css(%q{a[href$=".png"]}).map(&.attribute_by("id")).to_a # => ["a2"]

# find all a tags inside <p id=p3>, which href contain `html`
p parser.css(%q{p[id=p3] > a[href*="html"]}).map(&.attribute_by("id")).to_a # => ["a1"]

# find all a tags inside <p id=p3>, which href contain `html` or ends_with `.png`
p parser.css(%q{p[id=p3] > a:matches([href *= "html"], [href $= ".png"])}).map(&.attribute_by("id")).to_a # => ["a1", "a2"]

# create finder and use it in many places, this is faster, than create it many times
finder = Myhtml::Parser.finder(".jo")
p parser.css(finder).map(&.attribute_by("id")).to_a # => ["p2", "p4", "p6"]
```

## Example2
```crystal
require "modest"

html = <<-PAGE
  <html><body>
  <table id="t1"><tbody>
  <tr><td>Hello</td></tr>
  </tbody></table>
  <table id="t2"><tbody>
  <tr><td>123</td><td>other</td></tr>
  <tr><td>foo</td><td>columns</td></tr>
  <tr><td>bar</td><td>are</td></tr>
  <tr><td>xyz</td><td>ignored</td></tr>
  </tbody></table>
  </body></html>
PAGE

parser = Myhtml::Parser.new(html)

p parser.css("#t2 tr td:first-child").map(&.child!.tag_text).to_a # ["123", "foo", "bar", "xyz"]
```

## CSS Selectors rules
http://www.w3schools.com/cssref/css_selectors.asp
