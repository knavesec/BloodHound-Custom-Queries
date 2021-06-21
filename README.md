## Various custom queries/Cypher tricks developed over the years

All of these queries work great with Max's query module!



Generic, return the full path returned in text format. Output: `nodename - edge -> nodename - edge -> nodename ...`
```
match p=<INPUT PATH>
WITH [node in nodes(p) | coalesce(node.name, '')] as nodeLabels,
    [rel in relationships(p) | type(rel)] as relationshipLabels,
    length(p) as path_len
WITH reduce(path='', x in range(0,path_len-1) | path + nodeLabels[x] + " - " + relationshipLabels[x] + " -> ") as path,
    nodeLabels[path_len] as final_node
return path + final_node as full_path
```


Based off NetSPI's PowerUpSQL Get-SQLInstanceDomain, used to pull SPNs with the MSSQL service
```
match (n {hasspn:true}) unwind [spn in n.serviceprincipalnames where spn starts with "MSSQLSvc"] as list return n.name, list
```


Converting Epoch time into human readable dates
```
MATCH (u:User) WHERE u.pwdlastset < (datetime().epochseconds - ({days} * 86400)) AND NOT u.pwdlastset IN [-1.0,0.0] RETURN u.name,date(datetime({{epochSeconds:toInteger(u.pwdlastset)}})) AS changedate ORDER BY changedate DESC
```
