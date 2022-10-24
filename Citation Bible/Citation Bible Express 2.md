---
bibleName : AELF
---

<%* 

let bibleName = tp.frontmatter.bibleName;
console.log(bibleName);

let regexBible = new RegExp(`Bible\/${bibleName}\/${bibleName}.md$`);

let bible = [...app.vault.getMarkdownFiles().filter(file => file.path.match(regexBible))][0].basename;

let regexBook = new RegExp(`Bible\/${bible}\/Livres\/[^\/]+\/[^\/]+`);

book = (await tp.system.suggester((item) => "📜 "+item.basename, app.vault.getMarkdownFiles().filter(file => file.path.match(regexBook)), false, "📜 Choisir le livre à citer"));

let regexVerse = new RegExp(`###### ([0-9]{0,3}[^ ]{0,2})[\r?\n](.{0,70})`,'g');

let bookText = String(await app.vault.read(book));

let verseInit = (await tp.system.suggester((item) => item[1]+" "+item[2]+" ...", [...bookText.matchAll(regexVerse)],false,"🎬 Choisir le verset de début"))[1];

let verseEnd = (await tp.system.suggester((item) => item[1]+" "+item[2]+" ...", [...bookText.matchAll(regexVerse)].filter(item => Number(item[1].replace(/[a-zA-Z]/,''))>=Number(verseInit.replace(/[a-zA-Z]/,''))),false,"🏁 Choisir le verset de fin"))[1];

var standardRegex = "/"+bibleName+"/";
var bibleStandard = new RegExp(standardRegex);

if (bibleStandard.test(book.path)) {
	var bookName = book.basename;
} else {
	var bookName = book.basename.replace(/^[^\ ]+\ /g,'');
}

if (verseInit == undefined || verseInit == null) {
	return;
} else if (verseInit == verseEnd) {
	return "[["+book.basename+"#"+verseInit+"]] ";
} else if (Number(verseInit.replace(/[a-zA-Z]/,'')) <= 5) {
	return "[["+book.basename+"]],"+verseInit+"-"+verseEnd+" ";
} else {
	return "[["+book.basename+"#"+verseInit+"]]"+"-"+verseEnd+" ";
}

%>