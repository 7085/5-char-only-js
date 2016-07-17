# 5-char-only-js

**IMPORTANT: unfortunately this does not work as expected - the execution function does not handle the encoded string appropriately**

After I saw a tweet (https://twitter.com/Burp_Suite/status/753964655916249088) about some minimal charset JavaScript yesterday which reminded me of JSFuck (http://www.jsfuck.com/) I read the article but didn't see much new stuff. I stumbled upon JSFuck a few years ago when I was doing a presentation about JavaScript obfuscation at university in one of the security courses. It was very fascinating to see those possibilities so I also read the original discussions on the sla.ckers.org website and all other related sites and approaches.

As I said in the beginning I didn't see much new which I did not know already in this recent blog post (http://blog.portswigger.net/2016/07/executing-non-alphanumeric-javascript.html) 
which was linked in the tweet. The only interesting thing were those JavaScript string templates or multiline strings (https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/template_strings) 
which were used and fiddled around with. I didn't know about the code execution possibilites by those, so I had to read more about them.

When I rechecked the blog post and read the JS documentation about template strings today (when I saw it yesterday it was already very late), I thought I should also give it a try. After some time I found a possible solution which only needs 5 different characters to execute arbitrary JavaScript. 
I noticed a lot of discussion was going on about breaking "The JS Wall" (which is a reference to the 
currently necessary 6 characters to be able to execute arbitrary JavaScript) in the comments and especially here: 
https://gitter.im/aemkei/jsfuck.

To cut a long story short here are my findings, it is far from optimal but it includes all necessary characters.


## Minimal charset:
```
[ ] ! + `
```

## Character translation table:
The translation tables which I found out so far, also with some help of the really nice JSFuck wiki (https://github.com/aemkei/jsfuck/wiki) which saved me some time, is as follows:
```javascript
// execute ``[`constructor`][`constructor`]`xxx```
// string conversion [``+xxx][0]

// example: ``[`constructor`][`constructor`]`alert("5 chars only!")```

const builtin = {
	"\"\"": "``",
	"true": "!``",
	"false": "!!``",
	"undefined": "``[``]",
	"NaN": "+`+`"
};

const numbers = {
	"0": "+``",
	"1": "+!+``",
	"2": "!+``+!+``",
	"3": "!+``+!+``+!+``",
	"4": "!+``+!+``+!+``+!+``",
	"5": "!+``+!+``+!+``+!+``+!+``",
	"6": "!+``+!+``+!+``+!+``+!+``+!+``",
	"7": "!+``+!+``+!+``+!+``+!+``+!+``+!+``",
	"8": "!+``+!+``+!+``+!+``+!+``+!+``+!+``+!+``",
	"9": "!+``+!+``+!+``+!+``+!+``+!+``+!+``+!+``+!+``" // ... etc.
};

const chars = {
	"a": "[``+!!``][0][1]", // false
	"b": "[``+0[`constructor`]][0][12]",
	"c": "[``+[][`fill`]][0][3]", // [].fill
	"d": "[``+``[``]][0][2]", // undefined
	"e": "[``+``[``]][0][3]", // undefined
	"f": "[``+``[``]][0][4]", // undefined
	"g": "[``+``[`constructor`]][0][14]",
	"h": "17[`toString`]`36`",
	"i": "[``+``[``]][0][5]", // undefined
	"j": "19[`toString`]`36`",
	"k": "20[`toString`]`36`",
	"l": "[``+!!``][0][2]", // false
	"m": "[``+0[`constructor`]][0][11]",
	"n": "[``+``[``]][0][1]", // undefined
	"o": "[``+[][`fill`]][0][6]", // [].fill
	"p": "25[`toString`]`36`",
	"q": "26[`toString`]`36`",
	"r": "[``+!``][0][1]", // true
	"s": "[``+!!``][0][3]", // false
	"t": "[``+!``][0][0]", // true
	"u": "[``+``[``]][0][0]", // undefined
	"v": "31[`toString`]`36`",
	"w": "32[`toString`]`36`",
	"x": "33[`toString`]`36`",
	"y": "[``+[][`constructor`]][0][13]",
	"z": "35[`toString`]`36`",

	"A": "[``+[][`constructor`]][0][9]",
	"B": "``[`constructor`][`constructor`]`return unescape("%42")```",
	"C": "``[`constructor`][`constructor`]`return unescape("%43")```",
	"D": "``[`constructor`][`constructor`]`return unescape("%44")```",
	"E": "``[`constructor`][`constructor`]`return unescape("%45")```",
	"F": "``[`constructor`][`constructor`]`return unescape("%46")```",
	"G": "``[`constructor`][`constructor`]`return unescape("%47")```",
	"H": "``[`constructor`][`constructor`]`return unescape("%48")```",
	"I": "``[`constructor`][`constructor`]`return unescape("%49")```",
	"J": "``[`constructor`][`constructor`]`return unescape("%4a")```",
	"K": "``[`constructor`][`constructor`]`return unescape("%4b")```",
	"L": "``[`constructor`][`constructor`]`return unescape("%4c")```",
	"M": "``[`constructor`][`constructor`]`return unescape("%4d")```",
	"N": "[``+0[`constructor`]][0][9]",
	"O": "``[`constructor`][`constructor`]`return unescape("%4f")```",
	"P": "``[`constructor`][`constructor`]`return unescape("%50")```",
	"Q": "``[`constructor`][`constructor`]`return unescape("%51")```",
	"R": "``[`constructor`][`constructor`]`return unescape("%52")```",
	"S": "[``+``[`constructor`]][0][9]",
	"T": "``[`constructor`][`constructor`]`return unescape("%54")```",
	"U": "``[`constructor`][`constructor`]`return unescape("%55")```",
	"V": "``[`constructor`][`constructor`]`return unescape("%56")```",
	"W": "``[`constructor`][`constructor`]`return unescape("%57")```",
	"X": "``[`constructor`][`constructor`]`return unescape("%58")```",
	"Y": "``[`constructor`][`constructor`]`return unescape("%59")```",
	"Z": "``[`constructor`][`constructor`]`return unescape("%5a")```"
};

const special = {
	" ": "[``+[][`fill`]][0][8]",
	"(": "[``+[][`fill`]][0][13]",
	")": "[``+[][`fill`]][0][14]",
	"{": "[``+[][`fill`]][0][16]",
	"[": "[``+[][`fill`]][0][18]", // WARNING: different between browsers, in FF this is at index 22
	"]": "[``+[][`fill`]][0][30]", // WARNING: different between browsers, in FF this is at index 34
	"}": "[``+[][`fill`]][0][32]", // WARNING: different between browsers, in FF this is at index 36
	"<": "``[`fontcolor`]``[0]",
	"=": "``[`fontcolor`]``[11]",
	"\"": "``[`fontcolor`]``[12]",
	">": "``[`fontcolor`]``[16]",
	"/": "``[`fontcolor`]``[18]",
	"%": "``[`constructor`][`constructor`]`return escape("]")```[0]",
	"!": "``[`constructor`][`constructor`]`return unescape("%21")```",
	"#": "``[`constructor`][`constructor`]`return unescape("%23")```",
	"$": "``[`constructor`][`constructor`]`return unescape("%24")```",
	"&": "``[`constructor`][`constructor`]`return unescape("%26")```",
	"'": "``[`constructor`][`constructor`]`return unescape("%27")```",
	"*": "``[`constructor`][`constructor`]`return unescape("%2a")```",
	"+": "``[`constructor`][`constructor`]`return unescape("%2b")```",
	",": "``[`constructor`][`constructor`]`return unescape("%2c")```",
	"-": "``[`constructor`][`constructor`]`return unescape("%2d")```",
	".": "``[`constructor`][`constructor`]`return unescape("%2e")```",
	":": "``[`constructor`][`constructor`]`return unescape("%3a")```",
	";": "``[`constructor`][`constructor`]`return unescape("%3b")```",
	"?": "``[`constructor`][`constructor`]`return unescape("%3f")```",
	"@": "``[`constructor`][`constructor`]`return unescape("%40")```",
	"\\": "``[`constructor`][`constructor`]`return unescape("%5c")```",
	"^": "``[`constructor`][`constructor`]`return unescape("%5e")```",
	"_": "``[`constructor`][`constructor`]`return unescape("%5f")```",
	"`": "``[`constructor`][`constructor`]`return unescape("%60")```",
	"|": "``[`constructor`][`constructor`]`return unescape("%7c")```",
	"~": "``[`constructor`][`constructor`]`return unescape("%7e")```"
};
```
## TODO
- *find working executor*
- optimize
- find missing chars / shorter versions
- improve this readme
- better explanation
