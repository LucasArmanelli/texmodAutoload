# Automatically load tpf files to TexMod and optionally auto execute the program

## Usage/Examples
default .ini
```ini
[Settings]
WinWaitDelay=200
texmod=D:\TexMod\Texmod.exe
exe=D:\Games\Mass Effect 2\Binaries\MassEffect2.exe
AutoExec=0

[Mods]
mod1=D:\TexMod\Mass Effect 2\No Selection Box.tpf
mod2=D:\TexMod\Mass Effect 2\ME2-Improved Texture-v1.2.tpf
```

WinWaitDelay: Controls the time waited after successful window-related operation. Setting this too low causes it to miss necessary steps.
texmod: Path to Texmod.exe
exe: Path to game executable
AutoExec: Launch automatically after loading mods. 1 to enable, everything else disables

mod#: Path to tpf
index starts at 1 and you can add as many mods as you want

Supports loading ini files via command line and Drag & Drop

## .Exe

```vb
;Load ini file
If $CmdLine[0] = 1 Then
	Dim $IniFile = $CmdLine[1]
Else
	Dim $IniFile = @ScriptDir & "\default.ini"
EndIf

Dim $Sections = IniReadSectionNames($IniFile)
Dim $WinWaitDelay = IniRead($IniFile, $Sections[1], "WinWaitDelay", "")
Dim $Texmod = IniRead($IniFile, $Sections[1], "texmod", "")
Dim $EXE = IniRead($IniFile, $Sections[1], "exe", "")
Dim $AutoExec = IniRead($IniFile, $Sections[1], "AutoExec", "")
Dim $Mods = IniReadSection($IniFile, $Sections[2])
Dim $ModX

Opt("WinWaitDelay",$WinWaitDelay)

;Run TexMod
If WinExists("TexMod Beta Version v0.9b (c) RS")=0 Then 
	ShellExecute($Texmod)
Else
	WinActivate("TexMod Beta Version v0.9b (c) RS")
EndIf

;Select EXE
WinWait("TexMod Beta Version v0.9b (c) RS", "" ,1000)
ControlClick("TexMod Beta Version v0.9b (c) RS", "","[CLASS:Button; INSTANCE:2]")
WinWait("[CLASS:#32768]", "" ,1000)
ControlSend("[CLASS:#32768]","","","{DOWN}")
ControlSend("[CLASS:#32768]","","","{ENTER}")
WinWait("Select Executable", "" ,1000)
ControlSetText("Select Executable", "","[CLASS:Edit; INSTANCE:1]", $EXE)
ControlClick("Select Executable", "","[CLASS:Button; INSTANCE:2]")
;Use regular expression, prevents script getting confused if user has other windows titled TexMod open
WinWait("[REGEXPTITLE:^TexMod\s.*\.EXE]", "" ,1000) ;Wait for TexMod to load the EXE

;Load mods
For $ModX = 1 To $Mods[0][0]
	ControlClick("[REGEXPTITLE:^TexMod\s.*\.EXE]", "","[CLASS:Button; INSTANCE:12]")
	WinWait("[CLASS:#32768]", "" ,1000)
	ControlSend("[CLASS:#32768]","","","{DOWN}")
	ControlSend("[CLASS:#32768]","","","{ENTER}")
	WinWait("Select Texmod Packages to add.", "" ,1000)
	ControlSetText("Select Texmod Packages to add.", "","[CLASS:Edit; INSTANCE:1]", $Mods[$ModX][1])
	ControlClick("Select Texmod Packages to add.", "","[CLASS:Button; INSTANCE:1]")
Next

;Auto execute
If $AutoExec = 1 Then
	ControlClick("[REGEXPTITLE:^TexMod\s.*\.EXE]", "","[CLASS:Button; INSTANCE:11]")
EndIf
```