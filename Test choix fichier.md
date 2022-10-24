
<%*
const folders = this.app.vault.getAllLoadedFiles().filter(i => i.children).map(folder => folder.path);
const folderChoicePath = await tp.system.suggester(folders, folders);

const folder = app.vault.getAbstractFileByPath(folderChoicePath)
await tp.file.create_new("","",true,folder).basename
_%>
