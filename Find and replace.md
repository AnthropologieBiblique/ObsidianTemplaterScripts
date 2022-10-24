<%* 
    var newStatusTag = (await tp.system.suggester(
        ((item) => item),
        ["#T/Open", "#T/WaitingFor", "#T/DiscussWith", "#T/Explore", "#T/OnGoing", "#T/AchievedBy", "#T/DiscussedWith", "#T/Canceled", "#T/Completed", "#T/Canceled", "#T/Failed", "#T/Frozen"])
    )
    var newContent= tp.file.content.replace(/(Status:: )([\w,\/,#]+)(\s.*)/, "$1"+newStatusTag+"$3")
    var file = app.workspace.activeLeaf.view.file
    app.vault.modify(file, newContent) 
%>
