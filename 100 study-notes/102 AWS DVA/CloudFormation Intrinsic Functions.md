---
created: 2022-05-16 12:39
updated: 2022-06-09 12:10
---
---
**Links**: [[102 AWS DVA Index]]

---
## Ref
- `Fn::Ref`
- YAML short hand `!Ref`
- If we refer **parameters** it returns the *value of the parameter*
- If we refer **resources** it returns the *physical ID* of the underlying resource (ex: *EC2 ID*)

## GetAtt
- `Fn:GetAtt`
- **Attributes** are attached to any *resources* you create
- We can get a lot of attributes depending on the resource. To know what is exposed by the resource we will have to go through the documentation.
	- ![[attachments/Pasted image 20220518155349.png]]
	- ![[attachments/Pasted image 20220518155530.png]]

## FindInMap
- Accessing map values
- `Fn::FindInMap` to return a named value from a specific key.
- Another notation `!FindInMap [MapName, TopLevelKey, SecondLevelKey]`

## ImportValue
- Import values that are exported In other templates
- `Fn:ImportValue`

## Join
- `Fn::Join`
- To create `a:b:c` we can use `!Join [":",[a, b, c]]`

## Sub
- `Fn::Sub`
- `Fn::Sub`, or `!Sub` as a shorthand, is used to *substitute variables from a text*. It's a very handy function that will allow you to fully customise your templates.
- For example, you can *combine* `Fn::Sub` with *references or AWS Pseudo variables*
- **String must contain** `${VariableName}` and will substitute them.
	- ![[attachments/Pasted image 20220518160148.png]]