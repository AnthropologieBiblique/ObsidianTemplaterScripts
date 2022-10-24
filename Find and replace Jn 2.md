<%* 
   var newContent =  tp.file.content;

var books = {
  Jn: {
  ab : "Jn",
  français : "Jean"
  },
  Mt: {
  ab : "Mt",
  français : "Matthieu"
  },
  Mc: {
  ab : "Mc",
  français : "Marc"
  }
};

var counter = 0;
Object.entries(books).forEach(([book, items]) => {
   Object.entries(items).forEach(([kind, value]) => {
	   let regex = new RegExp(`(${value}) ([0-9]*),([0-9]{1,2}[a-z]?)`);
	   while(newContent.match(regex)) {
		   counter +=1; 
		   newContent =  link(newContent,book,regex)
	    }
	})
})
console.log(counter);

function link(content,book,regex){
	return content.replace(regex,"[["+book+" "+"$2"+"#"+"$3"+"]]")
}

var file = app.workspace.activeLeaf.view.file
app.vault.modify(file, newContent)
%>
