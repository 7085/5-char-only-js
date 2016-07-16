# 5-char-only-js

After I saw a tweet (https://twitter.com/Burp_Suite/status/753964655916249088) about minimal JavaScript yesterday which reminded 
me of JSFuck (http://www.jsfuck.com/) I read the article but didn't see much new stuff. I stumbled upon JSFuck a few years ago 
when I was doing a presentation about JavaScript obfuscation at university in one of the security courses. It was very fascinating 
to see those possibilities and also read the original discussions on the sla.ckers.org website and all other related sites and 
approaches.

As I said in the beginning I didn't see much new in this recent blog post (http://blog.portswigger.net/2016/07/executing-non-alphanumeric-javascript.html) 
which was linked in the tweet. The only interesting thing were those JavaScript string templates or multiline strings (https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/template_strings) 
which were used and fiddled around with. I didn't know about the code execution possibilites by those, so I had to read more about 
them.

When I rechecked the blog post and read the JS documentation about template strings today (yesterday it was already very late), 
I thought I should also giving it a try. After some time I found a possible solution which only needs 5 different characters to 
execute arbitrary JavaScript. I noticed a lot of discussion was going on about breaking "The JS Wall" (which is a reference to the 
currently necessary 6 characters to be able to execute arbitrary JavaScript) in the comments and especially here: 
https://gitter.im/aemkei/jsfuck.

To cut a long story short here are my findings:


## Minimal charset:
```
[ ] ! + `
```

## Character translation table:
The translation tables which I found out so far, also with some help of the really nice JSFuck wiki (https://github.com/aemkei/jsfuck/wiki) which saved me some time, is as follows:
```javascript
// execute ``[`constructor`][`constructor`]`xxx```
// string conversion [``+xxx][0]

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
	"c": "[``+[][`fill`]][0][3]", // [].fill
	"d": "[``+``[``]][0][2]", // undefined
	"e": "[``+``[``]][0][3]", // undefined
	"f": "[``+``[``]][0][4]", // undefined
	"g": "[``+``[`constructor`]][0][14]",
	"i": "[``+``[``]][0][5]", // undefined
	"l": "[``+!!``][0][2]", // false
	"n": "[``+``[``]][0][1]", // undefined
	"o": "[``+[][`fill`]][0][6]", // [].fill
	"r": "[``+!``][0][1]", // true
	"s": "[``+!!``][0][3]", // false
	"t": "[``+!``][0][0]", // true
	"u": "[``+``[``]][0][0]", // undefined

	"S": "[``+``[`constructor`]][0][9]",
	
};

const special = {
	" ": "[``+[][`fill`]][0][8]", // [].fill
	"(": "[``+[][`fill`]][0][13]", // [].fill
	")": "[``+[][`fill`]][0][14]", // [].fill
	"{": "[``+[][`fill`]][0][16]", // [].fill
	"[": "[``+[][`fill`]][0][18]", // [].fill WARNING: different between browsers, in FF this is at index 22
	"]": "[``+[][`fill`]][0][30]", // [].fill WARNING: different between browsers, in FF this is at index 34
	"}": "[``+[][`fill`]][0][32]", // [].fill WARNING: different between browsers, in FF this is at index 36
};
```
## TODO
- optimize
- find missing chars / shorter versions
