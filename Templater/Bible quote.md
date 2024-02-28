<%* // Modified from https://github.com/AnthropologieBiblique/ObsidianTheologicalStudies/blob/50341368622ffa5f297415ae1453a1da16bec5f9/Templates/Bible%20quoting/Detailed%20bible%20quote.md -%>
<%* 

//	let regexBible = new RegExp(`Bible\/[^\/]+\/[^\/]+.md$`);
texts = ["BHS", "ESV", "LSV", "LXX", "PST", "VG"];

//	bible = (await tp.system.suggester((item) => "📖 "+item.basename, app.vault.getMarkdownFiles().filter(file => file.path.match(regexBible)), false, "📖 Chose the Bible to quote")).basename;
bible = (await tp.system.suggester((item) => "📖 "+item, texts, true, "📖 Bible text"));

// let regexBook = new RegExp(`Bible\/${bible}\/Livres\/[^\/]+\/[^\/]+`);
if (bible == "ESV") {
	regexBook = new RegExp(`Bible\/ESV\/[^\/]+\/[^\/]+`);
} else if (bible == "LSV") {
	regexBook = new RegExp(`Bible\/LSV\/Chapters\/[^\/]+`);
} else {
	regexBook = new RegExp(`Bible\/${bible}\/Livres\/[^\/]+\/[^\/]+`);
}

book = (await tp.system.suggester((item) => "📜 "+item.basename, app.vault.getMarkdownFiles().filter(file => file.path.match(regexBook)), true, "📜 Chapter"));

let regexVerse = new RegExp(`###### ([0-9]{0,3}[^ ]{0,2})[\r?\n](.{0,70})`,'g');

let bookText = String(await app.vault.read(book));

let verseInit = (await tp.system.suggester((item) => item[1]+"		"+item[2]+" ...", [...bookText.matchAll(regexVerse)], true, "🟢 First verse"))[1];

let verseEnd = (await tp.system.suggester((item) => item[1]+"		"+item[2]+" ...", [...bookText.matchAll(regexVerse)].filter(item => Number(item[1].replace(/[a-zA-Z]/,''))>=Number(verseInit.replace(/[a-zA-Z]/,''))), true, "🛑 Last verse"))[1];

let visible = (await tp.system.suggester(["🔗 Text link", "📃 Visible transclusion"], [false, true], true, "Link or transclusion?"));

//	var style = "standard";

//	var bibleStandard = new RegExp(`/AELF/`);

//	if (style == "standard" && bibleStandard.test(book.path)) {
//		var bookName = book.basename;
//	} else if (style == "standard") {
//		var bookName = book.basename.replace(/^[^\ ]+\ /g,'');
//	} else {
//		var bookName = [...bookText.matchAll(/aliases : [\r?\n]-\ (.+)/g)][0][1];
//	}

if (verseInit == undefined || verseInit == null) {
	return;
} else if (verseInit == verseEnd && !visible) {
	return "[["+book.basename+"#"+verseInit+/*"|"+bookName+","+verseInit+*/"]] ";
} else if (!visible){
	return "[["+book.basename+"#"+verseInit+/*"#"+verseEnd+"|"+bookName+","+verseInit+"-"+verseEnd+*/"]]–"+verseEnd+" ";
} else if (verseInit == verseEnd && visible) {
	return "![["+book.basename+"#"+verseInit+/*"|"+bookName+","+verseInit+*/"]] ";
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
