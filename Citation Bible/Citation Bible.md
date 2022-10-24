<%* 

let regexBible = new RegExp(`Bible\/[^\/]+\/[^\/]+.md$`);

bible = (await tp.system.suggester((item) => "📖 "+item.basename, app.vault.getMarkdownFiles().filter(file => file.path.match(regexBible)), false, "📖 Choisir la bible à citer")).basename;

let regexBook = new RegExp(`Bible\/${bible}\/Livres\/[^\/]+\/[^\/]+`);

book = (await tp.system.suggester((item) => "📜 "+item.basename, app.vault.getMarkdownFiles().filter(file => file.path.match(regexBook)), false, "📜 Choisir le livre à citer"));

let regexVerse = new RegExp(`###### ([0-9]{0,3}[^ ]{0,2})[\r?\n](.{0,70})`,'g');

let bookText = String(await app.vault.read(book));

let verseInit = (await tp.system.suggester((item) => item[1]+" "+item[2]+" ...", [...bookText.matchAll(regexVerse)],false,"🎬 Choisir le verset de début"))[1];

let verseEnd = (await tp.system.suggester((item) => item[1]+" "+item[2]+" ...", [...bookText.matchAll(regexVerse)].filter(item => Number(item[1].replace(/[a-zA-Z]/,''))>=Number(verseInit.replace(/[a-zA-Z]/,''))),false,"🏁 Choisir le verset de fin"))[1];

let visible = (await tp.system.suggester(["✏️ Référence texte","👁 Encart visible"],[false, true],false,"Référence texte ou encart visible ?"));

if (!visible){
	var style = (await tp.system.suggester(["⚓️ Référence standardisée","🌍 Référence propre"],["standard", "local"],false,"Référence standardisée ou référence propre ?"));
} else {
	var style = "standard";
}

var bibleStandard = new RegExp(`/AELF/`);

if (style == "standard" && bibleStandard.test(book.path)) {
	var bookName = book.basename;
} else if (style == "standard") {
	var bookName = book.basename.replace(/^[^\ ]+\ /g,'');
} else {
	var bookName = [...bookText.matchAll(/aliases : [\r?\n]-\ (.+)/g)][0][1];
}

if (verseInit == undefined || verseInit == null) {
	return;
} else if (verseInit == verseEnd && !visible) {
	return "[["+book.basename+"#"+verseInit+"|"+bookName+","+verseInit+"]] ";
} else if (!visible){
	return "[["+book.basename+"#"+verseInit+"|"+bookName+","+verseInit+"]]"+"[["+book.basename+"#"+verseEnd+"|"+"-"+verseEnd+"]] ";
} else if (verseInit == verseEnd && visible) {
	return "![["+book.basename+"#"+verseInit+"|"+bookName+","+verseInit+"]] ";
} else {
	var flag=false;
	var quote = "";
	[...bookText.matchAll(regexVerse)].forEach(function(item) {
		if (!flag && item[1]==verseInit) {
			flag = true;
			quote+= "![["+book.basename+"#"+item[1]+"]] ";
		} else if (flag && item[1] != verseEnd) {
			quote+= "![["+book.basename+"#"+item[1]+"]] ";
		} else if (flag && item[1] == verseEnd) {
			quote+= "![["+book.basename+"#"+item[1]+"]] ";
			flag = false;
		}
	});
	return quote;
}

%>
