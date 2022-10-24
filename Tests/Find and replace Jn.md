<%* 
   var newContent =  tp.file.content;
   var book = "Jn";
   let regex = new RegExp(`(${book}\s[0-9]*),([0-9]{1,2}[a-z]?)`);
   var counter = 0;
   console.log(regex);
   while(newContent.match(regex)) {
	  counter +=1; 
	   console.log(counter);
	   newContent = newContent.replace(regex,"[["+"$1"+"#"+"$2"+"]]")
   } 
    var file = app.workspace.activeLeaf.view.file
    app.vault.modify(file, newContent)
%>
