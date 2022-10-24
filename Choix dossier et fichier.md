<%*  
var parentPath = app.workspace.activeLeaf.view.file;
console.log(parentPath.parent.path);
var test = this.app.vault.getAllLoadedFiles();
console.log(test);
const folders = this.app.vault.getAllLoadedFiles().map(folder => folder.path);  
const folderChoicePath = await tp.system.suggester(folders, folders);  
const folder = app.vault.getAbstractFileByPath(folderChoicePath)  
_%>

