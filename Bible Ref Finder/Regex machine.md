<%* 

function connector(symbol){
	return "\\s?"+symbol+"\\s?"
}

function orRegex(regex1,regex2){
	return "("+regex1+"|"+regex2+")"
}

function opRegex(regex){
	return "("+regex+")?"
}

function namedRegex(name,regex){
	return "(?<"+name+">"+regex+")"
}

function verse(number){
	let name = "VERSE"+number
	return namedRegex(name,"\\d{1,4}[a-z]?")
}

function chapter(number){
	let name = "CHAPTER"+number
	return namedRegex(name,"\\d{1,4}[a-z]?")
}

function chapterSimple(number,c2v){
	let chapterNumber = number
	let verseNumber = number+"1"
	let name = "CHAPTER_SIMPLE"+number
	let regex = chapter(chapterNumber)+connector(c2v)+verse(verseNumber)
	return namedRegex(name,regex)
}

function chapterRange(number,c2v,cr){
	let init = number+"1"
	let end = parseInt(init)+1
	let name = "CHAPTER_RANGE"+number
	let regex = chapterSimple(init,c2v)+connector(cr)+chapterSimple(end.toString(),c2v)
	return namedRegex(name,regex)
}

function verseRange(number,vr){
	let init = number+"1"
	let end = parseInt(init)+1
	let name = "VERSE_RANGE"+number
	let regex = verse(init)+connector(vr)+verse(end)
	return namedRegex(name,regex)
}

function verseBlock(number,iterations,v2v){
	let start = number+"1"
	let name = "VERSE_BLOCK"+number
	let regex = verse(start)
	for (let i = 0; i < iterations; i++) {
		inumber = parseInt(start)+i+1
		regex += opRegex(connector(v2v)+verse(inumber.toString()))
	}
	return namedRegex(name,regex)
}

function chapterBlock(number,c2v,v2v,vr){
	let verseRangeNumber = number+"1"
	let verseBlockNumber = parseInt(verseRangeNumber)+1
	let name = "CHAPTER_BLOCK"+number
	let regex = chapter(number)+connector(c2v)+orRegex(verseRange(verseRangeNumber,connector(vr)),verseBlock(verseBlockNumber.toString(),8,connector(v2v)))
	return namedRegex(name,regex)
}

function chapterBlocks(number,c2c,c2v,v2v,vr){
	let name = "CHAPTER_BLOCKS"+number.toString()
	let chapterRangeNumber = number+"1"
	let chapterBlockNumber = number+"2"
	let regex = orRegex(chapterRange(chapterRangeNumber,c2v,vr),chapterBlock(chapterBlockNumber,c2v,v2v,vr))
	return namedRegex(name,regex)
}

function bookBlock(book, iterations, connectors){
	let b2c = connectors.b2c
	let c2c = connectors.c2c
	let c2v = connectors.c2v
	let v2v = connectors.v2v
	let cr = connectors.cr
	let vr = connectors.vr
	let name = "BOOK_BLOCK"
	let regex = book+b2c+chapterBlocks(1,c2c,c2v,v2v,vr)
	for (let i = 2; i < iterations+2; i++) {
		regex += opRegex(connector(c2c)+chapterBlocks(i,c2c,c2v,v2v,vr))
	}
	return new RegExp(regex)
}

let connectors = {b2c:" ", c2c:";",c2v:",",v2v:"\\.",cr:"-",vr:"-"};
let test = chapterRange("1",",","-")
let test2 = verseRange("11","-")
let test3 = verseBlock("11",8,"\\.")
let test4 = chapterBlock("1",",","\\.","-")
let book = bookBlock("Jn",9,connectors)
console.log(book)

%>