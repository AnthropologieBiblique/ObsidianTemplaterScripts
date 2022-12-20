<%* 

function regConnector(symbol){
	return "\\s?"+symbol+"\\s?"
}

function regOr(regex1,regex2){
	return "("+regex1+"|"+regex2+")"
}

function regOp(regex){
	return "("+regex+")?"
}

function regNamed(name,regex){
	return "(?<"+name+">"+regex+")"
}

function regVerse(number){
	let name = "regVerse"+number
	return regNamed(name,"\\d{1,4}[a-z]?")
}

function regChapter(number){
	let name = "regChapter"+number
	return regNamed(name,"\\d{1,4}[a-z]?")
}

function regChapterSimple(number,c2v){
	let regChapterNumber = number
	let regVerseNumber = number+"1"
	let name = "regChapter_SIMPLE"+number
	let regex = regChapter(regChapterNumber)+regConnector(c2v)+regVerse(regVerseNumber)
	return regNamed(name,regex)
}

function regChapterRange(number,c2v,cr){
	let init = number+"1"
	let end = parseInt(init)+1
	let name = "regChapter_RANGE"+number
	let regex = regChapterSimple(init,c2v)+regConnector(cr)+regChapterSimple(end.toString(),c2v)
	return regNamed(name,regex)
}

function regVerseRange(number,vr){
	let init = number+"1"
	let end = parseInt(init)+1
	let name = "regVerse_RANGE"+number
	let regex = regVerse(init)+regConnector(vr)+regVerse(end)
	return regNamed(name,regex)
}

function regVerseBlock(number,iterations,v2v,vr){
	let start = number+"1"
	let name = "regVerse_BLOCK"+number
	let regex = regOr(regVerseRange(start,vr),regVerse(start))
	for (let i = 0; i < iterations; i++) {
		inumber = parseInt(start)+i+1
		regex += regOp(regConnector(v2v)+regOr(regVerseRange(inumber.toString(),vr),regVerse(inumber.toString())))
	}
	return regNamed(name,regex)
}

function regChapterBlock(number,c2v,v2v,vr){
	let regVerseRangeNumber = number+"1"
	let regVerseBlockNumber = parseInt(regVerseRangeNumber)+1
	let name = "regChapter_BLOCK"+number
	let regex = regChapter(number)+regConnector(c2v)+regVerseBlock(regVerseBlockNumber.toString(),8,v2v,vr)
	return regNamed(name,regex)
}

function regChapterBlocks(number,c2c,c2v,v2v,vr){
	let name = "regChapter_BLOCKS"+number.toString()
	let regChapterRangeNumber = number+"1"
	let regChapterBlockNumber = number+"2"
	let regex = regOr(regChapterRange(regChapterRangeNumber,c2v,vr),regChapterBlock(regChapterBlockNumber,c2v,v2v,vr))
	return regNamed(name,regex)
}

function bookBlock(iterations, regConnectors){
	let c2c = regConnectors.c2c
	let c2v = regConnectors.c2v
	let v2v = regConnectors.v2v
	let cr = regConnectors.cr
	let vr = regConnectors.vr
	let name = "BOOK_BLOCK"
	let regex = regChapterBlocks(1,c2c,c2v,v2v,vr)
	for (let i = 2; i < iterations+2; i++) {
		regex += regOp(regConnector(c2c)+regChapterBlocks(i,c2c,c2v,v2v,vr))
	}
	// console.log(regex)
	return new RegExp(regex,'g')
}

let regConnectors = {c2c:";",c2v:",",v2v:"\\.",cr:"-",vr:"-"};
let book = bookBlock(8,regConnectors)
// console.log(book)

const str1 = 'Ceci est un test 2,3-4;3,5-6 et un autre test ici 7,8-9,10a, et 1,2-3.4.5;2,3-4.5-6 test';
let array1;


const result2 = (exec) => {
	if (exec.groups.regChapter_BLOCK12 != undefined) {
		console.log("premier verse block")
		return
	}
	if (exec.groups.regChapter_RANGE11 != undefined) {
		console.log("premier chapter range")
		return
	}
	else {
		console.log("ERROR")
		return
	}
}

const result = (exec) => {
	// Extract all matches
	const groups = 
		Object.entries(exec.groups).filter(group => group[1] != undefined)
	console.log(groups)
	let firstChapterBlock = true;
	for(const [groupName,match] of groups.filter(group => /regChapter_BLOCKS([0-9])/.test(group[0]))) {
		let blocksIndex = /regChapter_BLOCKS([0-9])/.exec(groupName)[1]
		let chapterRange = new RegExp("regChapter_RANGE"+blocksIndex+"([0-9])")
		let chapterBlock = new RegExp("regChapter_BLOCK"+blocksIndex+"([0-9])")
		for(const [groupName,match] of groups.filter(group => chapterRange.test(group[0]))) {
			let chapterRangeIndex = blocksIndex+chapterRange.exec(groupName)[1]
			console.log("Chapter Range Index",chapterRangeIndex)
			let chapter = new RegExp("regChapter"+chapterRangeIndex+"1")
			
		}
		for(const [groupName,match] of groups.filter(group => chapterBlock.test(group[0]))) {
			if (firstChapterBlock){
				let chapterNumber = blocksIndex+chapterBlock.exec(groupName)[1]
				console.log("First Chapter Block",chapterNumber)
			}
			else {
				let chapterNumber = blocksIndex+chapterBlock.exec(groupName)[1]
				console.log("Chapter Block",chapterNumber)
			}
			firstChapterBlock = false;
		}
	}
}

while ((array1 = book.exec(str1)) !== null) {
  //console.log(array1)
  //console.log(array1.groups.regChapter_BLOCKS1)
  //console.log(array1.groups.regChapter_BLOCKS1 != undefined);
	// console.log(array1.index);
	//  console.log(`Found ${array1[0]}. Next starts at ${book.lastIndex}.`);
	result(array1)
}

function Verse(number){
	this.name = name
	this.number = name.replace(/[a-zA-Z]/,'')
}

function VerseRange(verseA,verseB){
	this.verseA = verseA
	this.verseB = verseB
}

function VerseBlock(){
	return null;
}

function Chapter(chapter){
	this.number = number
}

function ChapterSimple(chapter,verse){
	this.chapter = chapter
	this.verse = verse
}

function ChapterRange(chapterSimpleA,chapterSimpleB){
	this.chapterSimpleA = chapterSimpleA
	this.chapterSimpleB = chapterSimpleB
}

%>