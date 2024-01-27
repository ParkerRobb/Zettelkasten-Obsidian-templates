<% for (const annotation of it) { %>
##### <%= annotation.key %>

<%~ include("annotation", annotation) %>
<% } %>