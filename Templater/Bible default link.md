<%* // Modified from https://github.com/AnthropologieBiblique/ObsidianTheologicalStudies/blob/50341368622ffa5f297415ae1453a1da16bec5f9/Templates/Bible%20quoting/Express%20bible%20quote.md -%>
<%* 


// Specify default text and find MOC file:

// let bibleName = "AELF";

// let regexBible = new RegExp(`Bible\/${bibleName}\/${bibleName}.md$`);

// let bible = [...app.vault.getMarkdownFiles().filter(file => file.path.match(regexBible))][0].basename;


// Specify and find chapter files for default text:
let regexBook = new RegExp(`Bible\/LSV\/Chapters\/[^\/]+`);

// Chapter selection dialog:
book = (
	await tp.system.suggester(
		(item) => "📜 " + item.basename, 
		app.vault.getMarkdownFiles().filter(file => file.path.match(regexBook)), 
		true, 
		"📜 Chapter"
	)
);


// Read verses from selected chapter file:

let regexVerse = new RegExp(
	`###### ([0-9]{0,3}[^ ]{0,2})[\r?\n](.{0,70})`,
	'g'
);

let bookText = String(await app.vault.read(book));


// Initial verse selection dialog:
let verseInit = (
	await tp.system.suggester(
		(item) => item[1] + "		" + item[2] + " ...", 
		[...bookText.matchAll(regexVerse)], 
		true, 
		"🟢 First verse"
	)
)[1];

// Final verse selection dialog:
let verseEnd = (
	await tp.system.suggester(
		(item) => item[1] + "		" + item[2] + " ...", 
		[...bookText.matchAll(regexVerse)].filter(
			item => Number(
				item[1].replace(
					/[a-zA-Z]/,
					''
				)
			)
			>=Number(
				verseInit.replace(
					/[a-zA-Z]/,
					''
				)
			)
		), 
		true, 
		"🛑 Last verse"
	)
)[1];


// Standard style (for future implementation?):

//	var standardRegex = "/" + bibleName + "/";
//	var bibleStandard = new RegExp(standardRegex);

//	if (bibleStandard.test(book.path)) 
//	{
//		var bookName = book.basename;
//	} 
//	else 
//	{
//		var bookName = book.basename.replace(
//			/^[^\ ]+\ /g,
//			''
//		);
//	}


// Compile and insert link:
if (verseInit == undefined || verseInit == null) 
{
	return;
} 
else if (verseInit == verseEnd) 
{
	return "[[" + book.basename + "#" + verseInit + "]] ";
} 
else 
{
	return "[[" + book.basename + "#" + verseInit + "]]–" + verseEnd + " ";
}

%>