# A Jenkins Pipeline to build a .Net Core solution hosted in GitHub

Setup a Virtual Machine
=======================
- Create a Windows Virtual Machine on Azure.
  - ARM templates for the VM are included inside the "ARM-Templates" folder.
- Switch OFF "IE Enhanced Security Configuration" in Server Manager > Local Server.
- Download and install Chrome and use it as the browser for steps here on.
- Download and Install Java SE Development Kit (v11.0.16) 64-bit.

Setup Jenkins
=============
- Download Jenkins (v2.346.2) and launch installation.
- During the setup of "Service Logon Credentials" give the Windows login username and password.
- Clicking on "Test Credentials" button should give an "no previledges" error.
- Search "Local Security Policy" and launch it.
- Find and double click - Local Policies > User Rights Assignment > Log on as a service.
- Click on "Add User or Group" button and give the Windows login username and click "Check names".
- Click "Apply". Now clicking on "Test Credentials" should work.
- Clicking on "Test Port" for default port 8080 should be successful.
- Accept the rest of default setting and finish the installation.
- On a browser launch - http://localhost:8080.
- Copy and paste the initial password from the file mentioned.
- Install all the "suggested plugins"
- Create the first admin user.
- Accept the defaults for "Instance Configuration".
- Click on "Start using Jenkins".

Install tools required for pipelines
====================================
- Download and install Git 64-bit (v2.37.1). Accept default settings during installation.
- Browse to www.nuget.org and download recommended latest nuget.exe file (v6.2.1). Save it to C:\ (root)!
- Download Visual Studio 2022 Community Edition which is free. 
- During VS installation select the followin 2 features.
  - ASP.NET and web development.
  - Azure development.
- If the pipeline build needs any other tool(s) they need to be installed as well.

Configure Jenkins
=================
- Enable MSBuild: Manage Jenkins > Manage Plugins > Available (tab) > search "msbuild" > seelect "MSBuild" > "Install without restart".
- Setup Git: Manage Jenkins > Global Tool Configuration > Git.
  - Path to Git executable = "C:\Program Files\Git\bin\git.exe".
- Setup MSBuild: Manage Jenkins > Global Tool Configuration > MSBuild > Add MSBuild.
  - Name: appbuild
  - Path to MSBuild: C:\Program Files\Microsoft Visual Studio\2022\Community\MSBuild\Current\Bin.
- Click "Save".
- Copy home directory path: Manage Jenkins > Configure System > Home directory.
  - C:\Users\dilanperera\AppData\Local\Jenkins\.jenkins

Add a .NET Core app to GitHub repository
========================================
- Create a new repository in GitHub for the project.
- Click on the green "Code" drop down, select "Open in Visual Studio" and Clone it to the local disk.
- Create a new ASP.Net Core Web App (.Net 6.0) called "App-Code" at the cloned repo folder.
- Make sure the new solution builds locally.
- Push the newly created Solution into the GitHub repo.

Setup the build pipeline
========================
- Create a new project: Manage Jenkins > Configure System > + New Item.
  - Enter an item name: "app-project".
  - Select - "Freestyle project".
  - Click "OK".
- Link to GitHub repo: app-project > General tab > Source Code Management.
  - Select: Git.
  - Repository URL: https://github.com/dilanp/DotNetCore-GitHub-Jenkins-Pipeline.
  - Branches to build: */main
- Add Nuget restore task: app-project > Build tab > Add build step > Execute Windows batch command.
  - Command: C:\nuget.exe restore "C:\Users\dilanperera\AppData\Local\Jenkins\.jenkins\workspace\app-project\App-Code\App-Code.sln".
- Add MSBuild task: app-project > General tab > Build > Add build step > Build a Visual Studio project or solution using MSBuild.
  - MSBuild Version: appbuild
  - Command Line Arguments: App-Code\App-Code.sln (because the .sln file was not at the root folder).
- Click "Save".
- NoW we can build the project: app-project > Build Now.
- Notice the "Build History" shown below. The "Console Output" dropdown option should be consulted when we come across build errors!