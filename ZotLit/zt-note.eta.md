<% if (it.abstractNote) { %>
> [!abstract]
> <%= it.abstractNote.first() %>
<% } %>
<% if (it.attachment) { if (it.attachment.contentType = "application/pdf") { %>## Annotations
<%~ include("annots", it.annotations) %>
<% }  } %>