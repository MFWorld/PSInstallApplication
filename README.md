Application: supports multiple applications or commands in order
	eg. First application setting could disable a service, then second install/update software, then third would restart service
	eg. First application setting would install software, then second would apply a patch to software
	
	Application Configurations:
		Name: Name of specific task to run (Used for logging and if DetectionRule has value of [Name])
	
		Installer: path to installer. Defaults to source directory, but name MUST be specified
		
		InstallerType: Identifies how script will process the Installer value
			Allowed Values (case insensitive): AUTO,EXE,MSI,MSP,MSU,CMD,BAT,VBS
			Values in development: PS1,PSD1,NUPKG
				AUTO will detect file extension in Installer
		
		InstallSwitches: Are used for the arguments the application may need. Spaces are allowed
		
		SupportedArc: Architecture to compare software with Operating System. If not a match this application will not run
			Allowed Values (case insensitive): Both, x64, x86
		
		DetectionType: Specifies how the software will detect if application was installed already
			Allowed Values (case insensitive): File,Reg,GUID
				GUID will scan system for installed products and compare it to DetectionRule is set {some-guid}. This process takes longer based on products already installed
		
		DetectionRule: Depending on DetectionType, this will be the path, name and value to check
			Value Requirements for REG Type: Registry path, Registry Key Name, Registry value (optional)
				If version is not provided it will detect if registry key name exists only
				If [version] is provided, it will compare it to the version specified in details
				eg. HKEY_LOCAL_MACHINE\Software\Microsoft\SMS\Mobile Client,ProductVersion,[Version]
			Value Requirements for FILE Type: Folder path, File Name, Version (optional)
				If version is not provided it will detect if file exists only
				If [version] is provided, it will compare it to the version specified in details
				eg. C:\Program Files (x86)\Java\jre1.8.0_181\bin,java.exe,[Version]
			Value Requirements for GUID Type: {some-guid}, Version (optional)
				If version is not provided it will detect if GUID exists only
				If [version] is provided, it will compare it to the version specified in details
				eg. {A68173CF-C68F-4878-A1A1-3AD0A286D38A},[Version]
		
		IgnoreErrorCodes: Ignores any exit codes in the list. Useful is installer requires a reboot (eg. 3010)	
	
	DetectionAfter: Used as an extra validation check. Run the detection Rule a second time. 
		Allowed Values (case insensitive): True or False
		
		Additional arguments can be call by the Intall-Application.ps1 directly. This allow dynamic arguments to be passed using SCCM/MDT properties or other external sources:
			[InstallArgument] = If specified in the <Installer> section will replace with value passed by the script
				This can be helpful if multple installers exist, but only one to be installed. 
				e.g.: Install-Application.ps1 -InstallerArg %CCTKInstallerVersion%
			[SwitchArgument] = If specified in the <InstallSwitches> section will replace with value passed by the script
				e.g.: Install-Application.ps1 -SwitchArg %siteserver%
			[DetectArgument] = If specified in the <DetectionRule> section will replace with value passed by the script
				e.g.: Install-Application.ps1 -DetectionArg %version%
